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
