![Solana Stake Pools Banner](solana-stake-pools-banner.png)

# 🔥 Solana Stake Pools Research (2025)

This repository provides a structured and technical overview of major Solana stake pools as of July 2025. It also includes **live tools and a dashboard** to help validators check their eligibility and performance across these pools.

The research is intended for validator operators, contributors, and tooling developers who want to understand delegation criteria, performance benchmarks, and integration points with each pool.

---

## 🚀 Live Tools & Dashboards

These are the primary tools developed from this research, now available for public use.

* ### 📊 **[Solana Stake Pools Dashboard](http://cryptovik.info/solana-stakepools-dashboard)**
    The main result of this project. A comprehensive web interface for real-time monitoring and comparison of validator eligibility and performance scores across major Solana stake pools like Jito, Edgevana, Vault, JPool, and others.

* ### ⚙️ **[Validator Eligibility Checker (CLI)](https://github.com/SOFZP/Solana-Stake-Pools-Checker)**
    A command-line script to check a specific validator's eligibility for various stake pools. Ideal for automated checks and integrations, with support for JSON output.

---

## 📚 Table of Contents

- [Live Tools & Dashboards](#-live-tools--dashboards)
- [Data & Resources](#️-data--resources)
- [Notes on Stake Pools](#-notes-on-stake-pools)
  - [1. Jito Stake Pool](#-jito-stake-pool)
  - [2. Shinobi Performance Pool](#-shinobi-performance-pool)
  - [3. Edgevana Liquid Staking](#-edgevana-liquid-staking)
  - [4. JPool Delegation Program](#-jpool-delegation-program)
  - [5. Vault Stake Pool](#-vault-stake-pool)
  - [6. Blazestake](#-blazestake)
  - [7. AeroPool](#-aeropool)
  - [8. DynoSOL](#-dynosol)
  - [9. Jagpool](#-jagpool)
  - [10. Definity Staked SOL](#-definity-staked-sol)
  - [11. Firedancer Delegation Program](#-firedancer-delegation-program)
  - [12. DoubleZero Delegation Program](#-doublezero-delegation-program)
  - [13. Marinade (PSR)](#-marinade-psr-program)
  - [14. SOL Strategies (STKESOL)](#-sol-strategies-stkesol)
  - [15. SharkPool](#-sharkpool)
  - [16. StarPool](#-starpool)
- [My Other Validator Scripts](#️-my-other-validator-scripts)
- [Further Reading & Resources](#-further-reading--resources)
- [Disclaimer](#️-disclaimer)
- [Usage & Attribution](#-usage--attribution)

---

## 🗃️ Data & Resources

This repository also serves as a source for curated stake pool data.

* **[Stake Pools Configuration File](https://github.com/SOFZP/Solana-Stake-Pools-Research/blob/main/stakepools_list.conf)**
    The master list of stake pools and their parameters used by the dashboard and CLI tool.

* **[On-Chain Stake Data Archive](https://github.com/SOFZP/Solana-Stake-Pools-Research/tree/main/stakepool-data/mainnet-beta)**
    A collection of historical on-chain data showing stake distribution across all validators in the ```mainnet-beta``` cluster. This data is regularly collected and updated.

---

## 📘 Notes on Stake Pools

-   **Recommended Commission** refers to the typical commission level expected by the pool to qualify for delegation. It is not always a hard requirement but reflects what is practically needed to receive stake.
-   All data was collected from **publicly available sources** including official documentation, stake pool dashboards, APIs, and Solana community forums.
-   This document is for **informational purposes only**. Interacting with any stake pool or program is your own responsibility and should be done after reviewing their official policies and terms. Do your own research (DYOR).

---

## 🧠 Jito Stake Pool

**Website**: [jito.network](https://www.jito.network/stakenet/steward/)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/Jito4APyf642JPZPx3hGc6WWJ8zPKtRbRs4P815Awbb)  
**Discord**: [Join](https://discord.gg/jCcXyerc)  
**Delegation Frequency**: Every epoch ending in ```x9```, typically during the last 5–8% of the epoch  
**Recommended Commission**: 0% staking / 10% MEV  
**Blacklist Policy**: Subject to DAO vote  
**Requirements**:
- 5k SOL stake
- Top credit score performance for 30 epochs
- 0% staking / 10% MEV commissions

**APIs**:  
```
https://kobe.mainnet.jito.network/api/v1/validators  
https://kobe.mainnet.jito.network/api/v1/steward_events?limit=100&vote_account=...  
https://kobe.mainnet.jito.network/api/v1/steward_events?limit=10000&event_type=ScoreComponents&epoch=...
```

---

## 🥷 Shinobi Performance Pool

**Website**: [xshin.fi](https://xshin.fi/#Validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/spp1mo6shdcrRyqDK2zdurJ8H5uttZE6H6oVjHxN1QN)  
**Discord**: [Join](https://discord.gg/SGggRWJV)  
**Delegation Frequency**: Every epoch, in the last ~20 minutes  
**Recommended Commission**: Unspecified  
**Blacklist Policy**: Manual, maintained by founder (Zantetsu); includes SFDP exclusion but may be appealed directly  
**Requirements**:
- At least 10 epochs in top latency, CU and Consensus Voting performance
- Consistent quality and non-malicious behavior

**APIs**:  
```
https://github.com/1000xsh/xshin-data
```

---

## 💻 Edgevana Liquid Staking

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

## 📊 JPool Delegation Program

**Website**: [svt.one](https://svt.one/), [jpool.one](https://app.jpool.one/validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/CtMyWsrUtAwXWiGr9WjHT5fC3p3fgV8cyGpLTo2LJzG1)  
**Discord**: [Join](https://discord.gg/HAqkz9gC)  
**Delegation Frequency**: Every epoch, 5 epochs scoring cycle  
**Recommended Commission**: 0% staking / 10% MEV (must not increase more than +3%)  
**Blacklist Policy**: suspicious behavior (marked by validators.app)  
**Requirements**:  
- Top 500 APY over the last 10 epochs  
- JPool validator score among top 350  
- Active presence and community contribution  
- Detailed criteria in:  
  - [Inclusion and removal criteria](https://docs.jpool.one/technical-details/smart-strategy/inclusion-and-removal-criteria)  
  - [Validator scoring system](https://docs.jpool.one/technical-details/smart-strategy/validator-scoring-system)  
  - [Extra stake for Community Good validators](https://docs.jpool.one/technical-details/smart-strategy/community-good)  

**APIs**:  
```
https://api.thevalidators.io/jpool-scores/<EPOCH>/<VOTE_ACCOUNT>  
https://api.thevalidators.io/validators-history/history?network=mainnet&vote_id=<VOTE_ACCOUNT>&epoch_count=1000&epoch_from=<EPOCH>  
https://api.thevalidators.io/validators/list?network=mainnet&select=...  
https://api.thevalidators.io/jpool-scores/<EPOCH>  
Testnet History:  
https://api.thevalidators.io/validators-history/history?network=testnet&identity=<TESTNET_IDENTITY>&epoch_count=200
```

---

## 🔒 Vault Stake Pool

**Website**: [thevault.finance](https://thevault.finance/)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/Fu9BYC6tWBo1KMKaP3CFoKfRhqv9akmy3DuYwnCyWiyC)  
**Discord**: [Join](https://discord.gg/aQC53CGgQY) · **Twitter**: [x.com/thevaultfinance](https://x.com/thevaultfinance)  
**Delegation Frequency**: Every epoch; validators are queued and added in batches  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: Misbehavior such as sandwiching, ignoring governance (e.g., SIMD votes), or inactivity  
**Requirements**:  
- Proven track record of contribution to the ecosystem  
- Agree to the [SaaS program](https://docs.thevault.finance/validators/stake-as-a-service-saas)  
- Detailed criteria in:  
  - [Application Process](https://docs.thevault.finance/validators/validator-application-process)  
  - [General Delegation Criteria](https://docs.thevault.finance/validators/get-stake-from-the-pool)  
Additional feature: [Kamino Multiply Strategy](https://docs.thevault.finance/validators/kamino-multiply-strategy)  

**APIs**:  
```
https://raw.githubusercontent.com/SolanaVault/stakebot-data/main/bot-stats-latest.txt  
https://raw.githubusercontent.com/SolanaVault/stakebot-data/main/<EPOCH>/<FILENAME_FROM_PREVIOUS_QUERY>  
https://raw.githubusercontent.com/SolanaVault/stake-as-a-service-data/refs/heads/main/<EPOCH>/invoices.json  
```

---

## 🔥 Blazestake

**Website**: [stake.solblaze.org](https://stake.solblaze.org/validators)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/stk9ApL5HeVAwPLr3TLhDXdZS8ptVu7zp6ov8HFDuMi)  
**Discord**: [Join](https://discord.gg/wZNZ3BTG)
**Twitter**: [twitter.com/solblaze_org](https://twitter.com/solblaze_org)  
**Delegation Frequency**: Every epoch  
**Recommended Commission**: 5% staking / 10% MEV, *note: Validators with >50k SOL stake may qualify only with 0% staking / 0% MEV* **Blacklist Policy**: Unspecified  
**Requirements**:  
- Highest estimated APY among candidates  
- Technical and social presence encouraged  
- Full criteria in [delegation docs](https://stake-docs.solblaze.org/protocol/delegation-strategy)  
- Optional: direct staking integration on your site ([instructions](https://stake-docs.solblaze.org/developers/integrate))  
Also, to apply you may send an email to ```contact@solblaze.org``` with the subject: **“Request to Join BlazeStake Pool”**   

**APIs**:  
```
https://stake.solblaze.org/api/v1/validator_set  
https://stake.solblaze.org/api/v1/validator_count  
https://stake.solblaze.org/api/v1/apy  
https://stake.solblaze.org/api/v1/cls_applied_validator_stake?validator=<VOTE_ACCOUNT>  
https://stake.solblaze.org/api/v1/cls_boost?validator=<VOTE_ACCOUNT>
```


---

## 🪁 AeroPool

**Website**: [aeropool.io](https://www.aeropool.io/apply)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/aero2ePURjuEgLKTzcUmF6RypBncBGd7pMUYCoSsVJ6)  
**Twitter**: [@AeroPool_](https://x.com/AeroPool_)  
**Delegation Frequency**: Likely every epoch (unspecified), applications review on a weekly basis  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: possibly triggered by suspicious behavior  
**Requirements**:
- Must be a Solana ecosystem contributor
- Strategy outlined here: [delegation strategy](https://www.aeropool.io/delegationstrategy)

**APIs**:  
Not available.  
Participant list available at: https://www.aeropool.io/validators

---

## 🦕 DynoSOL

**Website**: [dynosol.io](https://www.dynosol.io)   
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/DpooSqZRL3qCmiq82YyB4zWmLfH3iEqx2gy8f2B6zjru)  
**Twitter**: [@DynoSOL_](https://x.com/DynoSOLPool)  
**Delegation Frequency**: Likely every epoch  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**:
- Excessive commission increases
- Sandwiching or harmful MEV practices
- Lack of validator activity

**Requirements**:
- 100+ epochs of consistent activity  
- 99%+ uptime
- UPD: [docs & delegation strategy](https://docs.dynosol.io)  

This new stakepool launched only in epoch 797  
Currently 27 validators with ~500k SOL delegated  

**APIs**:  
Not available.  
Participant list not available on site.  

---

## 🐈‍⬛ Jagpool

**Website**: [jagpool.xyz](https://www.jagpool.xyz/pool)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/jagEdDepWUgexiu4jxojcRWcVKKwFqgZBBuAoGu2BxM)  
**Twitter**: [@JagPool_xyz](https://x.com/JagPool_xyz)  
**Delegation Frequency**: Every 3 epochs scoring cycle  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**:  
- Excessive commission hikes  
- Sandwiching or malicious MEV  
- Long-term online inactivity  

**Requirements**:
- Operate from Latin America (LATAM) region, Singapore or South Africa
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

## 🐉 Definity Staked SOL

**Website**: [definity.finance](https://www.definity.finance/validators)   
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/Bvbu55B991evqqhLtKcyTZjzQ4EQzRUwtf9T4CcpMmPL)  
**Twitter**: [@realdefinity](https://x.com/realdefinity)  
**Delegation Frequency**: Every epoch (likely)  
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: malicious validator behavior  
**Requirements**:
- Validator located in Asia-Pacific (APAC) region  
- Goal: build geographic decentralization  
- Strategy: [staking strategy](https://www.definity.finance/staking-strategy)  

**APIs**:  
Not available

---

## 🔥 Firedancer Delegation Program

**Website**: [delegation.firedancer.io](https://delegation.firedancer.io/delegation-program)  
**Solscan Account**: [View Stake Accounts 1](https://solscan.io/account/8fxe1qGoDVLtqe9PAFyV4kR6zryTDyGQYb9AZQVUCvpM#stakeAccounts) |  [View Stake Accounts 2](https://solscan.io/account/AjLzAtJHDVQ4c2WMnSXt94a5BNt4CorH63af2uEmgkyF#stakeAccounts)  

⚠️ **Status (Sep 20, 2025):** The program is **winding down**. Delegations will start to be withdrawn **in October 2025**, **in the order they were originally granted**. Recently granted delegations are expected to remain for **~3 months** from their assignment. The team may re-introduce targeted delegation windows in the future for major features or high-risk transitions. ([Official blog update](https://delegation.firedancer.io/blog/winding-down-delegation-program))

**Delegation Frequency**: _Winding down — withdrawals per the schedule above (no ongoing rotation cycle)._
**Recommended Commission**: 5% staking / 10% MEV  
**Blacklist Policy**: Automatic exclusion upon SFDP removal  
**Historical requirements (June 2025):**
- Must be an active SFDP validator  
- Minimum 50k SOL stake  
- Validator must run Firedancer client on mainnet  
- Details: [intro article (June 9)](https://delegation.firedancer.io/blog/introducing-the-firedancer-delegation-program)  

**APIs**:  
Not available  
Validators who have submitted applications: https://delegation.firedancer.io/validators

**References**
- Winding down announcement (Sep 20, 2025): https://delegation.firedancer.io/blog/winding-down-delegation-program  
- Introducing the program (Jun 9, 2025): https://delegation.firedancer.io/blog/introducing-the-firedancer-delegation-program

---

## 🌐 DoubleZero Delegation Program

**Website:** [doublezero.xyz](https://doublezero.xyz)  
**Solana Compass Pool Page:** [View](https://solanacompass.com/stake-pools/3fV1sdGeXaNEZj6EPDTpub82pYxcRXwt2oie6jkSzeWi)  
**Dashboard**: [dzSOL Staking](https://doublezero.xyz/staking)  
**Docs (Fees)**: [Paying Fees](https://docs.malbeclabs.com/paying-fees/)  
**Discord**: [Join](https://discord.com/invite/doublezerotech)   
**Twitter**: https://x.com/doublezero   

**Status (Oct 2025):** DoubleZero is live on **mainnet-beta** ([launched Oct 2, 2025](https://doublezero.xyz/journal/doublezero-launches-mainnet-beta-a-high-performance-global-network-for-distributed-systems-underpinned-by-2z-token)) and continues delegating stake to validators connected to the network. The **Delegation Program (DZDP)** is expanding rapidly: the stake pool grew from [**3M SOL** at launch](https://doublezero.xyz/journal/doublezero-establishes-3m-sol-dzsol-stake-pool-to-expand-high-performance-fiber-network) to **~13M SOL** ([an additional **+10M SOL**](https://doublezero.xyz/journal/doublezero-stake-pool-grows-10m-sol-expanding-total-to-13m-sol)) as part of the mainnet rollout.  

**Network Access Fee:** A flat **5%** fee on **block rewards** and **priority fees**, beginning **epoch 859** (starts **Sat, Oct 4, 2025**). This is an infrastructure fee for using DoubleZero’s private fiber network and is **separate from any public validator commission**.  

**Delegation cadence**  
DZDP prioritizes validators that are connected to **DoubleZero mainnet-beta**; allocations expand in waves as the pool scales  

**Validator requirements**:
- Stable connectivity to **DoubleZero** (low latency, reliable link)  
- No explicit cap on public validator commission (network fee applies regardless)  

**Useful links**  
- Stake pool launch (3M SOL): https://doublezero.xyz/journal/doublezero-establishes-3m-sol-dzsol-stake-pool-to-expand-high-performance-fiber-network
- Stake pool grows +10M → ~13M SOL: https://doublezero.xyz/journal/doublezero-stake-pool-grows-10m-sol-expanding-total-to-13m-sol
- [Delegation program guide / thread (DZDP)](https://doublezero.xyz/journal/guide-to-the-doublezero-delegation-pool-dzdp)  
- Independent coverage: [CoinDesk summary](https://www.coindesk.com/business/2025/07/30/doublezero-launches-3m-sol-stake-pool-to-turbocharge-solana-validator-network)   

Notes: The **5% network fee** is paid for DoubleZero infrastructure (private/dark fiber, DC gear) and is **not** a validator commission change.  

---

## 🥩 Marinade (PSR Program)
 
**Website**: [psr.marinade.finance](https://psr.marinade.finance)  
**Solana Compass Pool Page**: [View](https://solanacompass.com/stake-pools/marinade)  
**Discord**: [Join](https://discord.gg/XM5Chpd7)  
**Delegation Frequency**: Every epoch  
**Recommended Commission**: Flexible; any value allowed  
**Blacklist Policy**: Delegation is algorithmic, but DAO can exclude sandwitchers  

**Requirements**:
- Must place a bond and submit a bid  
- Technical guide: [Validator Bonds CLI](https://github.com/marinade-finance/validator-bonds/blob/main/packages/validator-bonds-cli/README.md)  

**APIs**:  
```
https://validators-api.marinade.finance/validators?epochs=10&limit=1000000  
https://validators-api.marinade.finance/validators?epochs=0&limit=10&query_vote_accounts=<VOTE_ACCOUNT>  
https://validators-api.marinade.finance/validators?limit=9999&query_vote_accounts=<VOTE_ACCOUNT>  
https://validators-api.marinade.finance/rewards?epochs=10  
https://validators-api.marinade.finance/reports/staking
```

---

## 🟣 SOL Strategies (STKESOL)

**Website:** [solstrategies.io](https://solstrategies.io)  
**Solana Compass Pool Page:** [View](https://solanacompass.com/stake-pools/StKeDUdSu7jMSnPJ1MPqDnk3RdEwD2QbJaisHMebGhw)  
**Blog Announcement:** [Everything is liquid: SOL Strategies' new liquid staking solution - STKESOL](https://solstrategies.io/blog/everything-is-liquid-sol-strategies-new-liquid-staking-solution-stkesol)  
**Delegation Frequency:** Not specified (algorithmic delegation using Stakewiz Wiz Score with a 30-day mean)  
**Recommended Commission:** Unspecified (selection is based on a network-health focused score, not solely APY or commission)  
**Blacklist Policy:** Not specified  

**Requirements:**  

- No application, no KYC, no token purchase required. Inclusion is algorithmic.  
- Delegation is based on the [Stakewiz Wiz Score](https://stakewiz.com/), using a 30-day mean to smooth temporary peaks and troughs.  
- The score [blends multiple factors](https://stakewiz.com/faq#faq-wizscore) (performance, decentralization signals, published validator info, being up to date, avoiding high-concentration locations, etc.).  
- Initial target is ~40-60 validators with equal delegation, with potential changes over time (validator set size and/or score-aligned weights).  

**APIs:**  
```
https://api.stakewiz.com/validators  
https://api.stakewiz.com/validator/<VOTE_ACCOUNT>  
https://api.stakewiz.com/wiz_score
```

---

## 🦈 SharkPool

**Website:** [sharklabs.sh](https://sharklabs.sh)  
**Solana Compass Pool Page:** [View](https://solanacompass.com/stake-pools/HQLwnQJFH7t9nBTP4vbdW4eHy62aecfDnj8te8VzqkFL)  
**Focus:** stake allocation to validators run in partnership with U.S. universities to provide hands on blockchain education  
**Access:** currently for U.S. universities

**Program model**  
SharkLabs funds and launches initial nodes, trains student teams, then transitions day to day operations to campus groups

**Scale**  
Launched with about 20 schools including Princeton and UPenn, with more expected to join

**Requirements**  
Priority for active campus teams showing reliable uptime and operations, with a reported no commission policy on university run validators

**Useful links**  
- Program announcement thread — https://x.com/ghostofharvard/status/1940054139192721618  
- Coverage: [Blockworks feature on SharkLabs’ university validator program](https://blockworks.co/news/solana-teen-brooklyn-manannikov)  

**Notes**  
Blacklist policy, other details and delegation criteria are not fully standardized in public docs at time of writing

---

## ✨ StarPool

**Website**: [starpool.global](https://starpool.global)  
**Docs**: [starpool.global/docs](https://starpool.global/docs/)  
**Discord**: [Join](https://starpool.global/)  
**X/Twitter**: [Follow](https://starpool.global/)  

**Focus:** advance Solana’s decentralization by delegating stake to underrepresented validators across Africa, Latin America, Asia, Canada, and beyond  

**Delegation Frequency**: rebalanced every two weeks (following the next Wednesday epoch).  
**Recommended Commission**: validator commission ≤ 5% over the last 90 days; Jito MEV required.  
**Blacklist Policy**: must not be on Solana Foundation blacklist; service restricted in OFAC/EU-sanctioned jurisdictions.  

**Requirements:**
- Outside OFAC-sanctioned countries  
- Validator commission ≤ 5% (rolling 90d)  
- Operates Jito MEV  
- Total stake in the ~50k–250k SOL range  
- Preferably in **low stake-concentration regions**  
- Not blacklisted by the Solana Foundation

**Notes:** early-stage, small pool at the beginning of growth.  

**Useful links**  
- App: https://starpool.global/app   
- Docs: https://starpool.global/docs   
- Strategy: https://starpool.global/docs/strategy.html   

---

## 🛠️ My Other Validator Scripts

A collection of other custom tools created to support validator operations.

### ✅ Active Projects

-   🔍 **[CVK — See Your Stake v3.0](https://github.com/SOFZP/CVK-See-Your-Stake-v3.0)** Display all stake accounts for your validator with aggregation by source, totals by status (active, activating, deactivating), and interactive sorting.

-   🚨 **[Solana Delinquency Alert Bot](https://github.com/SOFZP/Solana-Delinquency-Alert-Bot)** Lightweight Bash bot that tracks validator delinquency and sends instant Telegram alerts. Configurable for any number of validators.

### 🗃️ Archived Projects (Legacy)

> These scripts are no longer maintained, but remain public for reference:

-   🪞 [see-your-solana-node-stake](https://github.com/SOFZP/see-your-solana-node-stake)
-   🪞 [see-your-solana-node-stake-v-2](https://github.com/SOFZP/see-your-solana-node-stake-v-2)
-   🪞 [show-solana-node-info_v2](https://github.com/SOFZP/show-solana-node-info_v2)

---

## 🔗 Further Reading & Resources

-   [🛡️ SFDP — Solana Foundation Delegation Program](https://solana.org/delegation-program)
-   [📘 Solana Compass — Stake Pools Overview](https://solanacompass.com/stake-pools)
-   [📂 Validators.app Dashboard](https://www.validators.app)
-   [📈 Stakewiz — Validator Scoreboard](https://stakewiz.com)
-   [🔝 Topvalidators Leaderboard](https://topvalidators.app)
-   [🔍 Solana Validator Health Metrics](https://solana.thevalidators.io)
-   [🔍 Solana Validator Graphana](https://metrics.stakeconomy.com)
-   [🥪 Solana Sandwich Finder Reports](https://github.com/FixedLocally/sandwich-finder/tree/master/reports)

---

## ⚠️ Disclaimer

The tools and data provided in this repository automate validator eligibility checks across multiple Solana stake pools using official APIs and on-chain data.

All data in this document is sourced from **open and public sources** as of July 2025.  
While care has been taken to ensure accuracy, **I am not responsible for changes in delegation policies, pool criteria, or any decisions made by these third-party stake pools**.

Use at your own discretion.

Community feedback and contributions are welcome!

---

## 🧾 Usage & Attribution

The information in this research is provided freely under an open model.  
You are welcome to use, reference, or build upon this material in your own work, whether personal, educational, or professional.

If you find this research helpful and are using it in a **comprehensive way** (e.g. integrating into documentation, validator tooling, or your product), a visible reference or active link back to this repository is kindly appreciated.

> Let’s make validator knowledge transparent and accessible across Solana.
