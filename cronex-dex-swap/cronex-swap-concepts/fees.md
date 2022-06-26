# Fees

### Liquidity Provider Fees[​](https://docs.uniswap.org/protocol/V2/concepts/advanced-topics/fees#liquidity-provider-fees) <a href="#liquidity-provider-fees" id="liquidity-provider-fees"></a>

There is a **0.3%** fee for swapping tokens. **This fee is split by liquidity providers proportional to their contribution to liquidity reserves and the platform.**

Swapping fees are immediately deposited into liquidity reserves. This increases the value of liquidity tokens, functioning as a payout to all liquidity providers proportional to their share of the pool. Fees are collected by burning liquidity tokens to remove a proportional share of the underlying reserves.

Since fees are added to liquidity pools, the invariant increases at the end of every trade. Within a single transaction, the invariant represents `token0_pool / token1_pool` at the end of the previous transaction.

There are many community-developed tools to determine returns.&#x20;

### Protocol Fees[​](https://docs.uniswap.org/protocol/V2/concepts/advanced-topics/fees#protocol-fees) <a href="#protocol-fees" id="protocol-fees"></a>

At the moment there is a 0.15% protocol fees.

### Protocol Charge Calculation[​](https://docs.uniswap.org/protocol/V2/concepts/advanced-topics/fees#protocol-charge-calculation) <a href="#protocol-charge-calculation" id="protocol-charge-calculation"></a>

A protocol-wide charge of 0.15% per trade is in effect. This represents 50% of the 0.30% fee. The fee is in effect if feeTo is not `address(0)` (`0x0000000000000000000000000000000000000000`), indicating that feeTo is the recipient of the charge.

This amount would not affect the fee paid by traders, but would affect the amount received by liquidity providers.

Rather than calculating this charge on swaps, which would significantly increase gas costs for all users, the charge is instead calculated when liquidity is added or removed.
