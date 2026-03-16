# Venus Protocol Supply Cap Bypass & TWAP Oracle Manipulation — PoC

> **Educational Purpose Only** — This PoC is created for security research and education
> purposes only. It runs against a simulated environment, not mainnet.

## Quick Start

```bash
git clone https://github.com/NomosLabs-Security/poc-venus-protocol-2026
cd poc-venus-protocol-2026
forge install foundry-rs/forge-std --no-git
forge test -vvvv
```

## Details

- **Exploit Date:** 2026-03-15
- **Chain:** BNB Chain (BSC)
- **Fork Block:** N/A (simulated attack)
- **Expected Output:** Demonstrates supply cap bypass and oracle manipulation leading to ~$2.15M in borrowed assets
- **Full Analysis:** [NomosLabs Security Intelligence Archive](https://nomoslabs.io/archive/venus-protocol-2026)

## Description

The Venus Protocol attack exploited two key vulnerabilities in the BNB Chain core pool:

### Phase 1: Supply Cap Bypass
Starting in June 2025, the attacker gradually accumulated THE tokens over nine months,
reaching 84% of the supply cap (~14.5M THE). The attacker then bypassed the normal
deposit path by directly transferring tokens to the vToken contract, circumventing the
supply cap entirely. This established a position of **53.2M THE** — **3.67x the designated
limit** of 14.5M.

### Phase 2: TWAP Oracle Manipulation
Exploiting the extremely low on-chain liquidity of THE tokens, the attacker manipulated
the TWAP oracle, driving THE's price from **$0.27 to $0.53** (~96% increase).

### Phase 3: Borrow Against Inflated Collateral
With the inflated collateral value, the attacker borrowed:
- **6.67M CAKE**
- **2,801 BNB + 1,970 WBNB**
- **1.58M USDC**
- **20 BTCB**

Total loss: approximately **$2,150,000**.

### Attacker Address
`0x1a35...6231`

### Response
Venus Protocol suspended borrowing and withdrawal for THE markets and other highly
concentrated liquidity markets (BCH, LTC, UNI, AAVE, FIL, TWT). Other Venus markets
remained operational.

## Key Vulnerabilities

1. **Supply Cap Bypass:** The protocol used `balanceOf()` to determine collateral, allowing
   direct token transfers to bypass the `deposit()` supply cap check.
2. **TWAP Oracle Manipulation:** Low on-chain liquidity for THE tokens made the TWAP oracle
   susceptible to price manipulation.
3. **Concentrated Collateral Risk:** A single user accumulated 84% of the supply cap over
   months without triggering risk alerts.

## Mitigation

- Use internal accounting (not `balanceOf()`) to track deposits
- Implement minimum liquidity thresholds for TWAP oracle feeds
- Add monitoring for concentrated collateral positions
- Enforce per-user deposit limits in addition to global supply caps

## RPC Providers

This PoC uses a simulated environment and does not require an RPC endpoint. For
running against a BSC fork:

| Provider | URL Format |
|----------|------------|
| [Ankr](https://www.ankr.com/) | `https://rpc.ankr.com/bsc` |
| [QuickNode](https://www.quicknode.com/) | `https://YOUR_ENDPOINT.quiknode.pro/YOUR_KEY` |

## License

MIT — For educational use only.
