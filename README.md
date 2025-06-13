# 🔥 Solana Stake Pool Research (12-June-2025)

This repository provides a structured and technical overview of major Solana stake pools as of June 2025.  
The document is intended for validator operators, contributors, and tooling developers who want to understand delegation criteria, performance benchmarks, and integration points with each pool.

> 🧪 Future updates will include eligibility-checking scripts using public APIs and validator performance scoring tools.

---

## 📘 Notes

- **Recommended Commission** refers to the typical commission level expected by the pool to qualify for delegation.  
  It is not always a hard requirement but rather reflects what is practically needed to receive stake.
- All data was collected manually and cross-verified where possible. If a detail is out of date, contributions are welcome!

---

## 1. 🧠 Jito Stake Pool

**Website**: [jito.network](https://www.jito.network/stakenet/steward/)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/Jito4APyf642JPZPx3hGc6WWJ8zPKtRbRs4P815Awbb)  
**Discord**: [Join](https://discord.gg/jCcXyerc)  
**Delegation Frequency**: Every epoch ending in `x9`, typically during the last 5–8% of the epoch  
**Recommended Commission**: 0% staking / 10% MEV  
**Blacklist Policy**: Subject to DAO vote  
**Requirements**:
- One-time 5k SOL self-stake
- Top credit score performance for 30 epochs
- 0% staking commission

**APIs**:  
```
https://kobe.mainnet.jito.network/api/v1/validators  
https://kobe.mainnet.jito.network/api/v1/steward_events?limit=100&vote_account=...  
https://kobe.mainnet.jito.network/api/v1/steward_events?limit=10000&event_type=ScoreComponents&epoch=...
```

---

## 2. 🥷 Shinobi Performance Pool

**Website**: [xshin.fi](https://xshin.fi/#Validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/spp1mo6shdcrRyqDK2zdurJ8H5uttZE6H6oVjHxN1QN)  
**Discord**: [Join](https://discord.gg/SGggRWJV)  
**Delegation Frequency**: Every epoch, in the last ~20 minutes  
**Recommended Commission**: Unspecified  
**Blacklist Policy**: Manual, maintained by founder (Zantetsu); includes SFDP exclusion but may be appealed directly  
**Requirements**:
- At least 10 epochs in top latency and CU performance
- Consistent quality and non-malicious behavior

**APIs**:  
```
https://github.com/1000xsh/xshin-data
```

---

## 3. 💻 Edgevana Liquid Staking

**Website**: [stake.edgevana.com](https://stake.edgevana.com/validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/edgejNWAqkePLpi5sHRxT9vHi7u3kSHP9cocABPKiWZ)  
**Discord**: [Join](https://discord.gg/edgevana)  
**Delegation Frequency**: Every epoch  
**Recommended Commission**: 0% staking / 10% MEV  
**Blacklist Policy**: Validators proven to engage in sandwiching or other malicious MEV behavior  
**Requirements**:
- Must be hosted on Edgevana infrastructure
- Average performance over last 10 epochs
- Strategy outlined in [delegation algorithm docs](https://docs.stake.edgevana.com/docs/validators/delegation-strategy-algorithm)

**APIs**:  
```
https://api.stake.edgevana.com/api/v2/scores
```

---

## 4. 📊 JPool Delegation Program

**Website**: [svt.one](https://svt.one/)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/CtMyWsrUtAwXWiGr9WjHT5fC3p3fgV8cyGpLTo2LJzG1)  
**Discord**: [Join](https://discord.gg/HAqkz9gC)  
**Delegation Frequency**: Every 5 epochs  
**Recommended Commission**: 0% staking / 10% MEV (must not increase more than +3%)  
**Blacklist Policy**: Undefined; possibly triggered by suspicious behavior  
**Requirements**:
- Top 500 APY over the last 10 epochs
- JPool validator score among top 350
- Active presence and community contribution  
  (see [scoring system](https://docs.jpool.one/technical-details/smart-strategy/validator-scoring-system))

**APIs**:  
```
https://api.thevalidators.io/jpool-scores/$EPOCH_NUMBER/$THIS_SOLANA_VOTE_ACCOUNT  
https://api.thevalidators.io/validators-history/history?network=mainnet&vote_id=$THIS_SOLANA_VOTE_ACCOUNT&epoch_count=1000&epoch_from=$LAST_EPOCH_NUMBER  
https://api.thevalidators.io/validators/list?network=mainnet&select=...  
https://api.thevalidators.io/jpool-scores/800  
Testnet History (optional):  
https://api.thevalidators.io/validators-history/history?network=testnet&identity=<TESTNET_IDENTITY>&epoch_count=200
```

---

## 5. 🔒 Vault Stake Pool

**Website**: [thevault.finance](https://thevault.finance/dapp/validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/Fu9BYC6tWBo1KMKaP3CFoKfRhqv9akmy3DuYwnCyWiyC)  
**Discord**: [Join](https://discord.gg/aQC53CGgQY) · [Twitter](https://x.com/thevaultfinance)  
**Delegation Frequency**: Every epoch; validators are queued and added in batches  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: Misbehavior such as sandwiching, ignoring governance (e.g., SIMD votes), or inactivity  
**Requirements**:
- Proven track record of contribution to the ecosystem
- Apply via [SaaS program](https://docs.thevault.finance/validators/stake-as-a-service-saas)
- Detailed criteria in:
  - [Application Process](https://docs.thevault.finance/validators/validator-application-process)  
  - [Pool Strategy](https://docs.thevault.finance/validators/get-stake-from-the-pool)  
  - [Kamino Strategy](https://docs.thevault.finance/validators/kamino-multiply-strategy)

**APIs**:  
```
https://raw.githubusercontent.com/SolanaVault/stakebot-data/main/bot-stats-latest.txt  
https://raw.githubusercontent.com/SolanaVault/stakebot-data/main/800/bot-stats-355971.json  
https://raw.githubusercontent.com/SolanaVault/stake-as-a-service-data/refs/heads/main/<EPOCH>/invoices.json
```

---

## 6. 🔥 Blazestake

**Website**: [stake.solblaze.org](https://stake.solblaze.org/validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/stk9ApL5HeVAwPLr3TLhDXdZS8ptVu7zp6ov8HFDuMi)  
**Discord**: [Join](https://discord.gg/wZNZ3BTG) · [Twitter](https://twitter.com/solblaze_org)  
**Delegation Frequency**: Every epoch  
**Recommended Commission**: 5% staking / 10% MEV  
🔹 *Note: Validators with >50k SOL stake may qualify for 0%/0% delegation.*  
**Blacklist Policy**: Unspecified  
**Requirements**:
- Highest estimated APY among candidates
- Technical and social presence encouraged
- Full criteria in [delegation docs](https://stake-docs.solblaze.org/protocol/delegation-strategy)
- Optional: direct validator integration on your site ([instructions](https://stake-docs.solblaze.org/developers/integrate))

**APIs**:  
```
https://stake.solblaze.org/api/v1/validator_set  
https://stake.solblaze.org/api/v1/validator_count  
https://stake.solblaze.org/api/v1/apy  
https://stake.solblaze.org/api/v1/cls_applied_validator_stake?validator=...  
https://stake.solblaze.org/api/v1/cls_boost?validator=...
```

📧 To apply, send an email to `contact@solblaze.org` with the subject:  
**“Request to Join BlazeStake Pool”**

---

## 7. 🪁 AeroPool

**Website**: [aeropool.io](https://www.aeropool.io/apply)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/aero2ePURjuEgLKTzcUmF6RypBncBGd7pMUYCoSsVJ6)  
**Twitter**: [@AeroPool_](https://x.com/AeroPool_)  
**Delegation Frequency**: Likely every epoch (unspecified)  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: Unspecified  
**Requirements**:
- Must be a Solana ecosystem contributor
- Strategy outlined here: [delegation strategy](https://www.aeropool.io/delegationstrategy)

**APIs**:  
Not available.  
Participant list available at: https://www.aeropool.io/validators

---

## 8. 🦕 DynoSOL

**Website**: [dynosol.io](https://www.dynosol.io) *(site may be unstable)*  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/DpooSqZRL3qCmiq82YyB4zWmLfH3iEqx2gy8f2B6zjru)  
**Twitter**: [@DynoSOL_](https://x.com/DynoSOL_) *(currently suspended)*  
**Delegation Frequency**: Every epoch  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**:
- Excessive commission increases
- Sandwiching or harmful MEV practices
- Lack of validator activity

**Requirements**:
- 100+ epochs of consistent activity
- >99% uptime
- Validator program launched in epoch 797 (approx. 4 epochs ago)
- Currently 27 validators with ~500k SOL delegated

**APIs**:  
Not available.  
Participant list not available on site.  
For contact, see [Discord message (direct)](https://discord.com/channels/428295358100013066/859540542608900127/1366367686099210271)

---

## 9. 🐈‍⬛ Jagpool

**Website**: [jagpool.xyz](https://www.jagpool.xyz/pool)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/jagEdDepWUgexiu4jxojcRWcVKKwFqgZBBuAoGu2BxM)  
**Twitter**: [@JagPool_xyz](https://x.com/JagPool_xyz)  
**Delegation Frequency**: Every 3 epochs  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**:
- Excessive commission hikes
- Sandwiching or malicious MEV
- Long-term inactivity

**Requirements**:
- Operate from Latin America
- Be active in the region for at least 10 epochs
- Have SFDP inclusion or 40k+ SOL stake
- Maintain online presence (website or Twitter)
- Full criteria:
  - [Delegation Criteria](https://docs.jagpool.xyz/DELEGATION-STRATEGY/Delegation-Criteria)  
  - [Performance Score](https://docs.jagpool.xyz/DELEGATION-STRATEGY/Performance-Score)  
  - [Application Process](https://docs.jagpool.xyz/DELEGATION-STRATEGY/Validator-Application-Process)  
  - [Community Goods](https://docs.jagpool.xyz/DELEGATION-STRATEGY/Community-Goods)

**APIs**:  
Not available.  
Validator list viewable at: https://www.jagpool.xyz/pool
---

## 10. ♾️ Definity Staked SOL

**Website**: [definity.finance](https://www.definity.finance/validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/Bvbu55B991evqqhLtKcyTZjzQ4EQzRUwtf9T4CcpMmPL)  
**Twitter**: [@realdefinity](https://x.com/realdefinity)  
**Delegation Frequency**: Every epoch (likely)  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: Exclusion for malicious validator behavior  
**Requirements**:
- Validator located in Asia-Pacific (APAC) region
- Goal: build geographic decentralization
- Strategy: [staking strategy](https://www.definity.finance/staking-strategy)

**APIs**:  
Not available

---

## 11. 🔥 Firedancer Delegation Program

**Website**: [delegation.firedancer.io](https://delegation.firedancer.io/delegation-program)  
**Solana Compass Pool Page**: Not available  
**Solscan Account**: [View Stake Accounts](https://solscan.io/account/8fxe1qGoDVLtqe9PAFyV4kR6zryTDyGQYb9AZQVUCvpM#stakeAccounts)  
**Delegation Frequency**: Program starts in a few weeks; 3-month rotation cycle  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: Automatic exclusion upon SFDP removal  
**Requirements**:
- Must be an active SFDP validator
- Minimum 50k SOL stake
- Validator must run Firedancer client (or be ready to migrate)
- Details: [intro article (June 9)](https://delegation.firedancer.io/blog/introducing-the-firedancer-delegation-program)

**APIs**:  
Not available  
Current validator list: https://delegation.firedancer.io/validators

---

## 12. 🥩 Marinade (PSR Program)

**Website**: [psr.marinade.finance](https://psr.marinade.finance)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/marinade)  
**Discord**: [Join](https://discord.gg/XM5Chpd7)  
**Delegation Frequency**: Every epoch  
**Recommended Commission**: Flexible; any value allowed  
**Blacklist Policy**: None — delegation is algorithmic, no manual exclusions  
**Special Note**:
- Validators using Marinade are **not eligible for SFDP**
- Involvement may lead to disqualification from other delegation programs

**Requirements**:
- Must place a bond and submit a bid
- Technical guide: [Validator Bonds CLI](https://github.com/marinade-finance/validator-bonds/blob/main/packages/validator-bonds-cli/README.md)

**APIs**:  
```
https://validators-api.marinade.finance/validators?epochs=10&limit=1000000  
https://validators-api.marinade.finance/validators?epochs=0&limit=10&query_vote_accounts=$YOUR_VOTE_ACCOUNT  
https://validators-api.marinade.finance/validators?limit=9999&query_vote_accounts=$YOUR_VOTE_ACCOUNT  
https://validators-api.marinade.finance/rewards?epochs=10  
https://validators-api.marinade.finance/reports/staking
```
