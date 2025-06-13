# 🔥 Solana Stake Pool Research (12-June-2025)

This repository provides a technical summary and comparison of major Solana stake pools as of June 2025.  
The document is prepared for validator operators and contributors who want to better understand delegation criteria, frequency, commission structures, and available APIs.

> 📌 Future updates will include automated scripts to check validator eligibility across pools via APIs.

---

## 📘 Overview

- 🧠 **Goal**: Provide a clear, structured, and regularly updated overview of stake pool policies.
- 🛠️ **Audience**: Solana validators seeking to optimize eligibility and participate in stake pools.
- 🧩 **Next step**: Add validator checker scripts and validator contribution examples.

---

## 1. 🧠 Jito Stake Pool

**Website**: [jito.network](https://www.jito.network/stakenet/steward/)  
**Compass**: [SolanaCompass](https://solanacompass.com/stake-pools/Jito4APyf642JPZPx3hGc6WWJ8zPKtRbRs4P815Awbb)  
**Discord**: [Join](https://discord.gg/jCcXyerc)  
**Delegation Frequency**: Every epoch ending in `x9`, typically in the last 5–8% of the epoch.  
**Commission**: 0% staking / 10% MEV  
**Blacklist Policy**: DAO vote based  
**Requirements**:
- One-time 5k SOL self-stake
- Top credit score performance for 30 epochs
- 0% commission

**APIs**:
```text
https://kobe.mainnet.jito.network/api/v1/validators
https://kobe.mainnet.jito.network/api/v1/steward_events?limit=100&vote_account=...
https://kobe.mainnet.jito.network/api/v1/steward_events?limit=10000&event_type=ScoreComponents&epoch=...
```
