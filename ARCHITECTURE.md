# Pipeline Architecture

How the CryptoVik Stake Pools data pipeline collects, validates and publishes Solana mainnet stake distribution data. Companion to [DATA_API.md](DATA_API.md), which documents the public outputs.

## Big picture

```
                    +--------------------------------------------+
                    |  watcher (systemd service, sequential loop) |
                    |  triggers: new epoch / pre-epoch-end /      |
                    |  every 5% of epoch (>=100 min apart) /      |
                    |  light window every 20 min                  |
                    +-----------------+--------------------------+
                                      | spawns per run
                                      v
                    +--------------------------------------------+
                    |  checker (bash, full cluster scan)          |
                    |  solana CLI per validator                   |
                    |  + getProgramAccountsV2 fallback            |
                    |  + withdraw-authority cache (TTL 6h)        |
                    +------+--------------------------+----------+
                           | heavy run                | light run
                           v                          v
        publish guards: >=95% processed, epoch consistent,
        total active stake within 2% of last published
                           |                          |
            +--------------+-----------+              |
            v                          v              v
   GitHub (canonical)            R2 (mirror + live-only files)
   epoch file + manifest         live.json, epoch file, manifest,
                                 registry CSV
                                      ^
   aggregator (once per epoch) -------+--> aggregated-history.json
   weekly mirror sync (cron) ---------+--> fills any gaps, never deletes
                                      |
                    watcher owns v1/<cluster>/status.json,
                    rewritten after every run attempt
```

Frontend consumption: the dashboard reads `live.json` from R2 first with a hard 8 second timeout, silently falls back to the GitHub file from the manifest, and drives its staleness banner from `status.json`. History stays on GitHub.

## Components

**Checker** (`cryptovik-stakepools-cluster-checker.sh`). One run = one full scan of every mainnet validator. For each validator it collects all delegated stake accounts (`solana stakes`), classifies stake authorities against the curated registry, and aggregates by pool, group and category. Two run modes: `full` (`--upload`) archives an immutable epoch snapshot to GitHub and R2 and updates the manifest; `light` (`--light`) publishes only `live.json` to R2. The mode is recorded in `script_info.run_mode`.

**GPAv2 fallback.** When the RPC provider deprioritizes a heavy `getProgramAccounts` scan ("please use getProgramAccountsV2"), the checker re-fetches that validator itself via paginated `getProgramAccountsV2` with the same vote-account filter and emits the same six fields the pipeline consumes. Hitting the pagination cap counts as a failure, never as partial data. Rescues are counted in `script_info.gpav2_fallback_count`.

**Withdraw-authority cache.** The single largest RPC cost used to be one `vote-account` call per validator for one field. The map `vote_pubkey -> withdraw_authority` is cached with a 6 hour TTL; new validators are fetched individually and merged without resetting the TTL clock.

**Watcher** (`automated_mainnet_beta_stakepool_checker.sh`, systemd). A single sequential loop, so runs can never overlap. Heavy triggers: new epoch, approaching epoch end, every 5 percent of the epoch with a 100 minute floor between percent runs (the floor exists because epochs shrink as mainnet slot time steps down to 200 ms). Light window: fires when no successful run of any kind happened for 20 minutes and the previous light attempt is also at least 20 minutes old, so a failing light run waits for the next window instead of hammering a degraded RPC. Every run is wrapped in a hard timeout. A PID lock with liveness detection survives crashes without ever killing a legitimately slow run.

**Publish guards** (both run modes): at least 95 percent of validators processed; the epoch must not change mid-run; total active stake must not drop more than 2 percent against the last published total (within an epoch active stake is constant, so a drop can only mean missing validators). If nothing was published for 6 hours the stake guard accepts the next snapshot with a loud warning instead of blocking forever. Publish order: R2 first (live, epoch file, registry), then GitHub (epoch file, manifest), then the manifest mirror to R2, so `latest_data_url` never advertises a file that does not exist yet.

**Status file.** The watcher owns `v1/<cluster>/status.json` and rewrites it after every run attempt. "Success" means data was actually published; a run whose upload was blocked by guards ages the status and increments `consecutive_failures`, which is what the dashboard staleness banner keys off.

**Aggregator** (`stakepools-aggregator.sh`). Once per epoch, after the rollover, it folds the freshest snapshot of the finished epoch into `aggregated-history.json` (one compact datapoint per epoch since 820) and pushes it to GitHub, then mirrors it to R2.

**Mirror sync** (`r2-mirror-sync.sh`, weekly cron). Diffs the GitHub tree against the R2 object list, uploads anything missing with correct cache headers, always refreshes the mutable files, never deletes. Doubles as a repair tool after any outage.

## Storage layout

| GitHub (canonical) | R2 (public API) |
| --- | --- |
| `stakepool-data/mainnet-beta/<epoch>/<file>.json` | `v1/mainnet-beta/<epoch>/<file>.json` |
| `stakepool-data/mainnet-beta/manifest.json` | `v1/mainnet-beta/manifest.json` |
| `stakepool-data/mainnet-beta/aggregated-history.json` | `v1/mainnet-beta/aggregated-history.json` |
| `stakepools_list.csv` (repo root) | `v1/registry/stakepools_list.csv` |
| - | `v1/mainnet-beta/live.json` (R2 only) |
| - | `v1/mainnet-beta/status.json` (R2 only) |

## Operational notes

Light runs are silent in Telegram by design; alerting for real outages comes from heavy-run notifications, `status.json` and the dashboard banner. Manual production runs are legitimate (`--upload` from a shell) and intentionally produce no Telegram messages. Every deploy is gated by md5 checksums of the files being replaced, and every script change ships with an offline test suite driven by mocked binaries.
