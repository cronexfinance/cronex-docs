# Pricing

## How are prices determined?

Each pair on CRONEX is actually underpinned by a liquidity pool. Liquidity pools are smart contracts that hold balances of two unique tokens and enforces rules around depositing and withdrawing them. The primary rule is the constant product formula. When a token is withdrawn (bought), a proportional amount must be deposited (sold) to maintain the constant. The ratio of tokens in the pool, in combination with the constant product formula, ultimately determine the price that a swap executes at.

## How CRONEX handles prices

In CRONEX, trades are always executed at the "best possible" price, calculated at execution time. Somewhat confusingly, this calculation is actually accomplished with one of two different formulas, depending on whether the trade specifies an exact _input_ or _output_ amount. Functionally, the difference between these two functions is miniscule, but the very existence of a difference increases conceptual complexity. Initial attempts to support both functions in CRONEX proved inelegant, and the decision was made to **not provide any pricing functions in the core**. Instead, pairs directly check whether the invariant was satisfied (accounting for fees) after every trade. This means that rather than relying on a pricing function to _also_ enforce the invariant, CRONEX pairs simply and transparently ensure their own safety, a nice separation of concerns. One downstream benefit is that CRONEX pairs will more naturally support other flavors of trades which may emerge, (e.g. trading to a specific price at execution time).

At a high level, in CRONEX, _trades must be priced in the periphery_. The good news is that the library provides a variety of functions designed to make this quite simple, and all swapping functions in the router are designed with this in mind.

## Pricing Trades

When swapping tokens on CRONEX, it's common to want to receive as many output tokens as possible for an _exact input amount_, or to pay as few input tokens as possible for an _exact output amount_. In order to calculate these amounts, a contract must look up the _current reserves_ of a pair, in order to understand what the current price is. However, it is _not safe to perform this lookup and rely on the results without access to an external price_.

Say a smart contract naively wants to send 10 USDC to the USDC/WCRO pair and receive as much WCRO as it can get, given the current reserve ratio. If, when called, the naive smart contract simply looks up the current price and executes the trade, it is _vulnerable to front-running and will likely suffer an economic loss_. To see why, consider a malicious actor who sees this transaction before it is confirmed. They could execute a swap which dramatically changes the USDC/WCRO price immediately before the naive swap goes through, wait for the naive swap to execute at a bad rate, and then swap to change the price back to what it was before the naive swap. This attack is fairly cheap and low-risk, and can typically be performed for a profit.

To prevent these types of attacks, it's vital to submit swaps _that have access to knowledge about the "fair" price their swap should execute at_. In other words, swaps need access to an _oracle_, to be sure that the best execution they can get from CRONEX is close enough to what the oracle considers the "true" price. While this may sound complicated, the oracle can be as simple as an _off-chain observation of the current market price of a pair_. Because of arbitrage, it's typically the case that the ratio of the intra-block reserves of a pair is close to the "true" market price. So, if a user submits a trade with this knowledge in mind, they can ensure that the losses due to front-running are tightly bounded. This is how, for example, the CRONEX frontend ensure trade safety. It calculates the optimal input/output amounts given observed intra-block prices, and uses the router to perform the swap, which guarantees the swap will execute at a rate no less that `x`% worse than the observed intra-block rate, where `x` is a user-specified slippage tolerance (0.5% by default).

### Exact Input[???](https://docs.uniswap.org/protocol/V2/concepts/advanced-topics/pricing#exact-input) <a href="#exact-input" id="exact-input"></a>

If you'd like to send an exact amount of input tokens in exchange for as many output tokens as possible, you'll want to use getAmountsOut. The equivalent SDK function is getOutputAmount, or minimumAmountOut for slippage calculations.

### Exact Output[???](https://docs.uniswap.org/protocol/V2/concepts/advanced-topics/pricing#exact-output) <a href="#exact-output" id="exact-output"></a>

If you'd like to receive an exact amount of output tokens for as few input tokens as possible, you'll want to use getAmountsIn. The equivalent SDK function is getInputAmount, or maximumAmountIn for slippage calculations.
