# CryptoVik Stake Pools Data API

Free, public, no-auth JSON data behind the [CryptoVik Stake Pools Dashboard](https://cryptovik.info/solana-stakepools-dashboard/).
Stake distribution of the Solana mainnet validator set across stake pools, delegation programs, CEXes and other stakeholder groups, collected continuously since epoch 820.

If you build something with this data, please attribute **CryptoVik Validator**. See [Attribution](#attribution) for ready-made lines.

## Attribution

The data is free for any use, commercial included, as long as you attribute the source. A visible link is enough, no logo required:

- Text: `Data: CryptoVik Stake Pools Dashboard - https://cryptovik.info/solana-stakepools-dashboard/`
- HTML: `<a href="https://cryptovik.info/solana-stakepools-dashboard/">Data: CryptoVik Stake Pools Dashboard</a>`
- Research or media: cite "CryptoVik Validator, Solana Stake Pools Research" with the dashboard URL.

## Base URLs

| Origin | Purpose |
| --- | --- |
| `https://data.cryptovik.info/v1/` | Fast CDN mirror. Live data and the whole archive. CORS enabled (GET/HEAD from any origin). |
| `https://raw.githubusercontent.com/SOFZP/Solana-Stake-Pools-Research/main/stakepool-data/` | Canonical archive in git. Same epoch files, full history, diffable. |

The `/v1/` prefix is a compatibility contract: fields may be added, but existing fields and paths will not change or disappear. A breaking change would ship as `/v2/` while `/v1/` keeps serving.

## Endpoints

| Path | Refreshed | Cache-Control | Contents |
| --- | --- | --- | --- |
| `v1/mainnet-beta/live.json` | continuously during the epoch, after every published run | `max-age=60` | The latest full snapshot (same schema as archived snapshots). |
| `v1/mainnet-beta/status.json` | after every pipeline run attempt | `max-age=30` | Pipeline health. Drive your own staleness checks from it. |
| `v1/mainnet-beta/manifest.json` | after every archived snapshot | `max-age=60` | Index: latest snapshot URL, live/status URLs, one snapshot URL per past epoch. |
| `v1/mainnet-beta/aggregated-history.json` | once per epoch | `max-age=300` | Compact epoch-by-epoch history used by the dashboard charts. |
| `v1/mainnet-beta/<epoch>/<file>.json` | never (immutable) | `max-age=31536000, immutable` | Archived snapshots, several per epoch. Cache them forever. |
| `v1/mainnet-beta/validators/index.json` | once per epoch | `max-age=300` | Every validator seen since epoch 820, active or gone: vote and identity keys, current and former names, first and last epoch, latest active stake and stake rank. |
| `v1/mainnet-beta/validators/<vote_pubkey>.json` | once per epoch | `max-age=300` | One validator's history: an entry per finalized epoch with total stake, stake sources by registry group, stake rank and (since epoch 1042) commission, client version, delinquency and Jito fields. See [Per-validator history](#per-validator-history). |
| `v1/registry/stakepools_list.csv` | with every archived snapshot (content changes only when the registry changes) | `max-age=60` | The curated registry of pools, programs and stakeholder groups the pipeline classifies against. |

## status.json

```json
{
  "cluster": "mainnet-beta",
  "last_success_utc": "2026-08-22T10:41:03Z",
  "last_attempt_utc": "2026-08-22T10:41:03Z",
  "consecutive_failures": 0,
  "current_epoch": 1020,
  "epoch_completed_percent": 55.1,
  "expected_interval_seconds": 1200
}
```

`expected_interval_seconds` is the advertised cadence. Recommended staleness rule (the dashboard uses exactly this): compute `age = now - last_success_utc`; treat data as delayed when `age > 3 * expected_interval_seconds`, as unavailable when `age > 12 * expected_interval_seconds` or `consecutive_failures >= 3`. Read the interval from the file instead of hardcoding it: backend cadence changes then require nothing on your side.

## Snapshot structure

Top-level keys of `live.json` and every archived snapshot:

| Key | Contents |
| --- | --- |
| `metadata` | `timestamp_utc`, `epoch`, `epoch_completed_percent`, `cluster_name`. |
| `script_info` | Run diagnostics: `run_mode` (`full` or `light`), `execution_time_seconds`, `total_validators_in_cluster`, `total_validators_processed_successfully`, `gpav2_fallback_count`, `withdraw_authority_cache`, `checker_version` (since 6.0), `jito_metadata` (`status`: `ok`, `cached` = the Jito API failed and the last good response, at most 24 hours old, was reused, `unavailable` or `skipped`; `validators`: entries loaded; `cache_age_seconds`: age of the reused copy, null when fresh). |
| `pool_definitions` | The registry rows used for classification: `short_name`, `long_name`, `group`, `category`, `type`, `public_key`, `description`, `url`, `image`. |
| `validators` | One entry per validator, see below. |

Each `validators[]` entry:

| Key | Contents |
| --- | --- |
| `info` | `identity_pubkey`, `vote_pubkey`, `name`, `details`, `website`, `icon_url`. |
| `totals` | `total_active_lamports`, `total_activating_lamports`, `total_deactivating_lamports`, `total_stake_accounts`. |
| `aggregations.by_group` | Stake grouped by registry group: `name`, `active_lamports`, `activating_lamports`, `deactivating_lamports`, `count`, `percent`, `authority_keys`. |
| `aggregations.by_pool`, `aggregations.by_category` | Same idea at pool and category granularity. |
| `other_stake_account_keys` | Stake authorities that did not match any registry entry. |
| `meta` | Validator metadata as of the snapshot (since checker 6.0, epoch 1042, 25 September 2026; absent in older snapshots): `commission` (inflation commission, percent, may be fractional), `commission_bps` (the same in basis points, since 6.1), `block_revenue_commission_bps` (vote account v4, null until set), `version` (client version string), `delinquent` (bool), `credits`, `epoch_credits`, `skip_rate` (percent or null when the validator had no leader slots yet), `last_vote`, `root_slot`, `cli_activated_stake_lamports` (the stake figure reported by the cluster, a cross-check for `totals.total_active_lamports`), and `jito` - an object or `null` when the validator is unknown to the Jito network or the Jito API was unavailable for the run: `mev_commission_bps`, `priority_fee_commission_bps`, `running_jito`, `running_bam`, `bam_connection_rate`, `directed_stake_target`, `directed_stake_lamports`. |

All amounts are lamports (integers, 1 SOL = 1e9 lamports). Commissions in `meta` are percent (`commission`) or basis points (`mev_commission_bps`, `priority_fee_commission_bps`: 10000 = 100 percent). Within one epoch a validator's active stake is constant, so any two snapshots of the same epoch agree on `active_lamports` for every validator they both cover.

## Per-validator history

`v1/mainnet-beta/validators/<vote_pubkey>.json` is rebuilt once per epoch from the finalized snapshot of that epoch (the same snapshot the aggregated history uses), so the current epoch is not in it yet: take the current state from `live.json`. Stake sources are keyed by the CURRENT registry group, also for old epochs (retroactive regrouping: when a pool is renamed in the registry, the whole history follows).

| Key | Contents |
| --- | --- |
| `vote`, `identity`, `name`, `aliases` | Keys and the current name; `aliases` lists former names, `name_changes` and `identity_changes` say from which epoch each value applied. |
| `first_epoch`, `last_epoch`, `epochs_count` | Coverage. A validator whose `last_epoch` is behind the index `last_epoch` has left the active set. |
| `epochs[]` | Ascending. Each entry: `epoch`, `ts` (snapshot time), `active`, `activating`, `deactivating` (lamports), `accounts` (stake accounts), `stake_rank` (1 = largest active stake in that epoch), `groups` (registry group -> `[active, activating, deactivating]` lamports; `OTHER` = stake that matched no registry entry), `meta` (compact: `commission`, `commission_bps`, `mev_bps`, `pf_bps`, `version`, `delinquent`, `jito`, `bam`; null before epoch 1042). |

`validators/index.json` lists all validators with `vote`, `identity`, `name`, `aliases`, `first_epoch`, `last_epoch`, `epochs`, `active_lamports`, `stake_rank` and `groups` (count) as of each validator's last epoch, sorted by last epoch then stake; `active_validators_count` counts the ones present in the latest epoch.

## Data quality guarantees

A snapshot is published only if it passes all pipeline guards: at least 95 percent of validators processed, epoch consistent across the whole run, and total active stake not dropping more than 2 percent against the last published snapshot (which, within an epoch, would only mean missing validators, not market movement). Validators that hit RPC deprioritization are re-fetched via paginated `getProgramAccountsV2` instead of being skipped. In short: what is published is complete or it is not published.

## Examples

Latest total active stake in SOL:

```
curl -sL https://data.cryptovik.info/v1/mainnet-beta/live.json \
  | jq '[.validators[].totals.total_active_lamports] | add / 1e9'
```

Freshness check:

```
curl -sL https://data.cryptovik.info/v1/mainnet-beta/status.json \
  | jq '{age_s: (now - (.last_success_utc | fromdateiso8601)), expected: .expected_interval_seconds, fails: .consecutive_failures}'
```

One validator by identity:

```
curl -sL https://data.cryptovik.info/v1/mainnet-beta/live.json \
  | jq '.validators[] | select(.info.identity_pubkey == "IDENTITY_PUBKEY")'
```

Commission, MEV commission and client version of every validator (snapshots since epoch 1042):

```
curl -sL https://data.cryptovik.info/v1/mainnet-beta/live.json \
  | jq -r '.validators[] | select(.meta != null) | [.info.name, .meta.commission, (.meta.jito.mev_commission_bps // "n/a"), .meta.version] | @tsv'
```

Stake from one pool on one validator, epoch by epoch (lamports):

```
curl -sL https://data.cryptovik.info/v1/mainnet-beta/validators/VOTE_PUBKEY.json \
  | jq -r '.epochs[] | [.epoch, .stake_rank, (.groups.JITO_POOL[0] // 0)] | @tsv'
```

Find a validator by any name it ever had:

```
curl -sL https://data.cryptovik.info/v1/mainnet-beta/validators/index.json \
  | jq '.validators[] | select((.name // "") + (.aliases | join(" ")) | test("cryptovik"; "i"))'
```

Every archived snapshot of one epoch (canonical origin):

```
curl -sL https://api.github.com/repos/SOFZP/Solana-Stake-Pools-Research/contents/stakepool-data/mainnet-beta/1019 \
  | jq -r '.[].download_url'
```

## Fair use

No API key is required and there are no hard limits today; help us keep it that way. Epoch files are immutable, fetch each once and cache it forever. Polling `live.json` more often than once a minute or `status.json` more often than every 30 seconds buys you nothing because of the cache headers. We reserve the right to introduce limits if usage patterns force it. If you plan sustained heavy usage, say hi first: [cryptovik.info](https://cryptovik.info).
