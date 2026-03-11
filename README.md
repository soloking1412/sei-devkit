# sei-devkit

> ⚠️ **Under active development — not production ready. APIs will change.**

Developer toolkit for building on Sei's parallelized EVM. Helps you deploy faster, catch parallel bottlenecks before they hit mainnet, and actually measure the performance gains you're getting.

---

## Why this exists

Sei runs a parallelized EVM — transactions that don't touch the same state execute concurrently. That's the whole pitch. But when devs move from Ethereum, they bring their existing contracts and workflow over unchanged, with no idea if those contracts are parallel-friendly or not.

Hardhat and Foundry work fine on Sei, but they treat it like any other EVM chain. They don't surface anything Sei-specific. Three things kept coming up as real friction:

- Contracts with shared storage slots causing parallel conflicts nobody knows about
- New devs spending 30+ minutes just setting up RPC config, getting testnet SEI, and figuring out SeiScan verification
- No way to actually see if your contract is faster on Sei vs standard Ethereum — you just have to trust the docs

sei-devkit is being built to fix all three.

---

## Planned Features

### 1. Parallel Conflict Detector *(in progress)*
Will analyze your Solidity contracts and flag state access patterns that serialize execution on Sei — shared mappings hit in the same block, storage slots that multiple transactions compete for, contract designs that look fine on Ethereum but become bottlenecks on a parallel chain.

```bash
# planned usage
sei-devkit analyze ./contracts
```

Expected output:
```
[WARN] contracts/Vault.sol:42 — shared mapping `balances` accessed in deposit() and withdraw()
       Transactions hitting both functions in the same block will execute sequentially.
       Consider per-user storage isolation.

[OK]   contracts/Token.sol — no parallel conflicts detected
```

This check is Sei-specific. No other EVM chain needs it because no other EVM chain runs parallel execution this way.

---

### 2. One-Command Deployer *(planned)*
Will handle RPC setup, testnet faucet check, contract deployment, and SeiScan verification in a single command — replacing the 30-minute copy-paste ritual that trips up most new Sei devs.

```bash
# planned usage
sei-devkit deploy --contract Vault --network sei-testnet
```

Expected output:
```
✔ RPC connected (evm-rpc-testnet.sei-apis.com)
✔ Wallet balance: 12.4 SEI
✔ Vault deployed → 0x3f4a...91bc
✔ Verified on SeiScan → https://seitrace.com/address/0x3f4a...91bc
```

---

### 3. Gas Profiler *(planned)*
Will benchmark your contract on Sei testnet vs a forked Ethereum environment and show a side-by-side so you can actually quantify the performance difference.

```bash
# planned usage
sei-devkit profile --contract Vault --function deposit
```

Expected output:
```
Function: deposit(uint256)
─────────────────────────────────
Ethereum (simulated):   47,200 gas  |  ~1.8s finality
Sei testnet:            47,200 gas  |  ~0.4s finality

Parallel execution:     eligible ✔
Estimated throughput:   4.2x vs single-threaded EVM
```

---

## Current Status

| Feature | Status |
|---|---|
| Parallel conflict detector | 🔨 In progress |
| One-command deployer | 📋 Planned |
| Hardhat plugin | 📋 Planned |
| Foundry wrapper | 📋 Planned |
| Gas profiler | 📋 Planned |
| Docs site | 📋 Planned |

---

## Roadmap

**M1**
- `sei-devkit analyze` — parallel conflict detector
- `sei-devkit deploy` — deployer with SeiScan verification
- npm package published as `@sei-devkit/cli`

**M2**
- Hardhat plugin (`@sei-devkit/hardhat`)
- Foundry script wrapper (`sei-devkit forge`)
- Auto SeiScan verification in deploy flow

**M3**
- Gas profiler
- Full docs site
- Tutorial on Sei builder Telegram + dev forum

---

## About

Built by a solo dev with a background in smart contract security auditing (15+ audits across Sherlock, Code4rena, Immunefi) and Arbitrum ecosystem tooling (Stylus Developer Toolkit grant). The parallel conflict detector comes from spotting this class of issue in real audits — it's not a theoretical edge case.

GitHub: [github.com/soloking1412](https://github.com/soloking1412)

---

## Contributing

Not ready for contributions yet. Star the repo to follow progress — will open up issues once M1 is stable.
