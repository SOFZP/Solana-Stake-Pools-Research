# Pipeline Architecture

How the StakeMap data pipeline collects, validates and publishes Solana mainnet stake distribution data. Companion to [DATA_API.md](DATA_API.md), which documents the public outputs.

The pipeline is run by [CryptoVik](https://cryptovik.info/) and feeds [StakeMap](https://stakemap.info/). It grew out of the open-source [Solana Stake Pools Checker](https://github.com/SOFZP/Solana-Stake-Pools-Checker), a command-line tool that shows who stakes to a single validator. That tool was taken as the base and has been reworked many times over to scan the whole cluster, validate the result and publish it. This document describes how the pipeline works today. Everything it produces is open under CC BY 4.0: the files in this repository and the data API at `https://data.stakemap.info/v1/`.

## Big picture

```
                    +---------------------------------------------+
                    |  watcher (systemd service, sequential loop) |
                    |  heavy run: new epoch / before epoch end /  |
                    |  evenly spaced points of the epoch          |
                    |  light run: continuously in between         |
                    +-----------------+---------------------------+
                                      | spawns per run
                                      v
                    +---------------------------------------------+
                    |  checker (full cluster scan)                |
                    |  stake engine: one paginated pass over the  |
                    |  Stake program, reconciled per validator    |
                    |  fallback: solana CLI per validator         |
                    |  + validator metadata (cluster, Jito API)   |
                    +------+--------------------------+-----------+
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
   history builder (once per epoch) --+--> validators/*.json (R2 only)
   weekly mirror sync (cron) ---------+--> fills any gaps, never deletes
                                      |
                    watcher owns v1/<cluster>/status.json,
                    rewritten after every run attempt
```

How StakeMap reads the data: the manifest, the aggregated history and past epochs come from GitHub, and when GitHub cannot be reached the same files are taken from the data API. The current epoch comes from `live.json` on the data API with a hard 8 second timeout and falls back to the latest archived snapshot named in the manifest. Per-validator history is served by the data API only. The staleness notice is driven by `status.json`.

## Components

**Checker** (`cryptovik-stakepools-cluster-checker.sh`). One run = one full scan of every mainnet validator. It reads every delegated stake account of the cluster, classifies stake authorities against the curated registry (`stakepools_list.csv`, fetched fresh for every run), and aggregates by pool, group and category. Two run modes: `full` (`--upload`) archives an immutable epoch snapshot to GitHub and R2 and updates the manifest; `light` (`--light`) publishes only `live.json` to R2. Both modes do the same complete scan and pass the same guards. The mode is recorded in `script_info.run_mode`, the checker version in `script_info.checker_version`.

**Stake engine** (`cvk_stake_engine.py`, since checker 6.4, October 2026). Stake accounts are read in one paginated pass over the whole Stake program instead of one request per validator. For every account the engine computes active, activating and deactivating stake of the current epoch with a port of the activation math of the Agave client, so the figures equal what `solana stakes` prints. It then reconciles every validator with the `activatedStake` the cluster itself reports. When a large share of validators does not reconcile, epoch rewards are still being distributed, and the engine waits and repeats the pass once. A single validator that does not reconcile is re-read on its own. If it still does not reconcile, it is not taken from the engine: the checker reads it through the CLI path below, so an unverified figure never reaches a snapshot. Diagnostics are published in `script_info.engine`.

**CLI path and GPAv2 fallback.** The way the pipeline read stake before the engine stays in place as the fallback, for a single validator or for a whole run when the engine fails. It collects a validator's stake accounts with `solana stakes`. When the RPC provider deprioritizes that heavy scan ("please use getProgramAccountsV2"), the checker re-fetches the validator itself via paginated `getProgramAccountsV2` with the same vote-account filter and emits the same six fields the pipeline consumes. Hitting the pagination cap counts as a failure, never as partial data. Rescues are counted in `script_info.gpav2_fallback_count`. A light run that cannot use the engine takes this path only occasionally and is otherwise skipped. A heavy run is never skipped.

**Validator metadata.** Every validator entry carries a `meta` object (since checker 6.0, epoch 1042): commission, client and version, delinquency, vote credits and skip rate as the cluster reports them, and the Jito fields (MEV and priority fee commission, BAM) from the public Jito API. The last good Jito response is kept on disk and reused for at most 24 hours when that API fails, and `script_info.jito_metadata` records whether a snapshot used a fresh or a cached response. The age of the vote account comes from a registry that the history builder refreshes once per epoch.

**Withdraw-authority cache.** Self-stake is recognized by a key match: a stake account counts as the operator's own when its withdraw authority equals the withdraw authority of the vote account it delegates to. The checker therefore needs that key for every validator. The engine reads all of them in a few batched calls on every run and refreshes the map `vote_pubkey -> withdraw_authority`. On the CLI path the map is served from a cache with a 6 hour TTL, and new validators are fetched individually and merged without resetting the TTL clock.

**Watcher** (`automated_mainnet_beta_stakepool_checker.sh`, systemd). A single sequential loop, so runs can never overlap. Heavy runs are triggered by a new epoch, by the approach of the epoch end and at evenly spaced points of the epoch, with a minimum time between those points (the minimum exists because epochs shrink as mainnet slot time steps down to 200 ms). Light runs fill the time in between: the next one starts a short pause after the previous successful run, so `live.json` is refreshed continuously throughout the epoch. A failing light run is retried only after the same pause instead of hammering a degraded RPC. The cadence the watcher currently keeps is published as `expected_interval_seconds` in `status.json`, and consumers should read it from there. Every run is wrapped in a hard timeout. A PID lock with liveness detection survives crashes without ever killing a legitimately slow run.

**Publish guards** (both run modes): at least 95 percent of validators processed; the epoch must not change mid-run; total active stake must not drop more than 2 percent against the last published total (within an epoch active stake is constant, so a drop can only mean missing validators). If nothing was published for 6 hours the stake guard accepts the next snapshot with a loud warning instead of blocking forever. Publish order: R2 first (live, epoch file, registry), then GitHub (epoch file, manifest), then the manifest mirror to R2, so `latest_data_url` never advertises a file that does not exist yet. A GitHub write gets up to four attempts, and before a retry the target path is checked, so an upload that landed although the call reported a failure is not repeated.

**Status file.** The watcher owns `v1/<cluster>/status.json` and rewrites it after every run attempt. "Success" means data was actually published; a run whose upload was blocked by guards ages the status and increments `consecutive_failures`, which is what StakeMap's staleness notice keys off.

**Aggregator** (`stakepools-aggregator.sh`). Once per epoch, after the rollover, it folds the freshest snapshot of the finished epoch into `aggregated-history.json` (one compact datapoint per epoch since 820) and pushes it to GitHub, then mirrors it to R2. Then it starts the history builder.

**History builder** (`validators-history-builder.py`). From the same finalized snapshot it extends one file per validator (`validators/<vote_pubkey>.json`) and the index of every validator seen since epoch 820 (`validators/index.json`). These files are published to R2 only and are deliberately kept out of git, where hundreds of files would be rewritten every epoch. The builder also maintains the vote-account age registry the checker reads: Jito's ValidatorHistory record as the base, extended with this dataset's own finalized epochs. A builder failure never fails the aggregator.

**Mirror sync** (`r2-mirror-sync.sh`, weekly cron). Diffs the GitHub tree against the R2 object list, uploads anything missing with correct cache headers, always refreshes the mutable files, never deletes. Doubles as a repair tool after any outage.

## Storage layout

| GitHub (canonical) | R2 (public API, `https://data.stakemap.info/`) |
| --- | --- |
| `stakepool-data/mainnet-beta/<epoch>/<file>.json` | `v1/mainnet-beta/<epoch>/<file>.json` |
| `stakepool-data/mainnet-beta/manifest.json` | `v1/mainnet-beta/manifest.json` |
| `stakepool-data/mainnet-beta/aggregated-history.json` | `v1/mainnet-beta/aggregated-history.json` |
| `stakepools_list.csv` (repo root) | `v1/registry/stakepools_list.csv` |
| - | `v1/mainnet-beta/live.json` (R2 only) |
| - | `v1/mainnet-beta/status.json` (R2 only) |
| - | `v1/mainnet-beta/validators/index.json` (R2 only) |
| - | `v1/mainnet-beta/validators/<vote_pubkey>.json` (R2 only) |

## Operational notes

Light runs are silent in Telegram by design; alerting for real outages comes from heavy-run notifications, `status.json` and StakeMap's staleness notice. Manual production runs are legitimate (`--upload` from a shell) and intentionally produce no Telegram messages. Every deploy is gated by md5 checksums of the files being replaced, and every script change ships with an offline test suite driven by mocked binaries. A change to the way stake is read first runs in shadow next to the live pipeline and is compared with it validator by validator: inside one epoch the active stake of every validator must be identical.
