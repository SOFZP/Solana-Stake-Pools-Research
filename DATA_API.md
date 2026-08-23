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
| `v1/mainnet-beta/live.json` | about every 20 minutes | `max-age=60` | The latest full snapshot (same schema as archived snapshots). |
| `v1/mainnet-beta/status.json` | after every pipeline run attempt | `max-age=30` | Pipeline health. Drive your own staleness checks from it. |
| `v1/mainnet-beta/manifest.json` | after every archived snapshot | `max-age=60` | Index: latest snapshot URL, live/status URLs, one snapshot URL per past epoch. |
| `v1/mainnet-beta/aggregated-history.json` | once per epoch | `max-age=300` | Compact epoch-by-epoch history used by the dashboard charts. |
| `v1/mainnet-beta/<epoch>/<file>.json` | never (immutable) | `max-age=31536000, immutable` | Archived snapshots, several per epoch. Cache them forever. |
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
| `script_info` | Run diagnostics: `run_mode` (`full` or `light`), `execution_time_seconds`, `total_validators_in_cluster`, `total_validators_processed_successfully`, `gpav2_fallback_count`, `withdraw_authority_cache`. |
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

All amounts are lamports (integers, 1 SOL = 1e9 lamports). Within one epoch a validator's active stake is constant, so any two snapshots of the same epoch agree on `active_lamports` for every validator they both cover.

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

Every archived snapshot of one epoch (canonical origin):

```
curl -sL https://api.github.com/repos/SOFZP/Solana-Stake-Pools-Research/contents/stakepool-data/mainnet-beta/1019 \
  | jq -r '.[].download_url'
```

## Fair use

No API key is required and there are no hard limits today; help us keep it that way. Epoch files are immutable, fetch each once and cache it forever. Polling `live.json` more often than once a minute or `status.json` more often than every 30 seconds buys you nothing because of the cache headers. We reserve the right to introduce limits if usage patterns force it. If you plan sustained heavy usage, say hi first: [cryptovik.info](https://cryptovik.info).
