# CRONEX Protocol

CRONEX is an _automated liquidity protocol_ powered by a constant product formula and implemented in a system of non-upgradeable smart contracts on the Cronos blockchain. It obviates the need for trusted intermediaries, prioritizing **decentralization**, **censorship resistance**, and **security**. CRONEX is a fork of Uniswap on Ethereum which is **open-source software** licensed under the GPL.

Each CRONEX smart contract, or pair, manages a liquidity pool made up of reserves of two CRC-20 tokens.

Anyone can become a liquidity provider (LP) for a pool by depositing an equivalent value of each underlying token in return for pool tokens. These tokens track pro-rata LP shares of the total reserves, and can be redeemed for the underlying assets at any time.

Pairs act as automated market makers, standing ready to accept one token for the other as long as the “constant product” formula is preserved. This formula, most simply expressed as `x * y = k`, states that trades must not change the product (`k`) of a pair’s reserve balances (`x` and `y`). Because `k` remains unchanged from the reference frame of a trade, it is often referred to as the invariant. This formula has the desirable property that larger trades (relative to reserves) execute at exponentially worse rates than smaller ones.

In practice, CRONEX applies a 0.30% fee to trades, which is added to reserves. As a result, each trade actually increases `k`. This functions as a payout to LPs, which is realized when they burn their pool tokens to withdraw their portion of total reserves. In the future, this fee may be reduced to 0.25%, with the remaining 0.05% withheld as a protocol-wide charge.

Because the relative price of the two pair assets can only be changed through trading, divergences between the CRONEX price and external prices create arbitrage opportunities. This mechanism ensures that CRONEX prices always trend toward the market-clearing price.
