# CRONEX Swap Concepts

## Introduction

Token swaps in CRONEX are a simple way to trade one CRC-20 token for another.

For end-users, swapping is intuitive: a user picks an input token and an output token. They specify an input amount, and the protocol calculates how much of the output token they’ll receive. They then execute the swap with one click, receiving the output token in their wallet immediately.

In this guide, we’ll look at what happens during a swap at the protocol level in order to gain a deeper understanding of how CRONEX works.

Swaps in CRONEX are different from trades on traditional platforms. CRONEX does not use an order book to represent liquidity or determine prices. CRONEX uses an automated market maker mechanism to provide instant feedback on rates and slippage.

As we learned before, each pair on CRONEX is actually underpinned by a liquidity pool. Liquidity pools are smart contracts that hold balances of two unique tokens and enforces rules around depositing and withdrawing them.

This rule is the constant product formula. When either token is withdrawn (purchased), a proportional amount of the other must be deposited (sold), in order to maintain the constant.

### Anatomy of a Swap[​](https://docs.uniswap.org/protocol/V2/concepts/core-concepts/swaps#anatomy-of-a-swap) <a href="#anatomy-of-a-swap" id="anatomy-of-a-swap"></a>

At the most basic level, all swaps in CRONEX happen within a single function, aptly named `swap`:

```
function swap(uint amount0Out, uint amount1Out, address to, bytes calldata data);
```

## Receiving tokens

As is probably clear from the function signature, CRONEX requires `swap` callers to _specify how many output tokens they would like to receive_ via the `amount{0,1}Out` parameters, which correspond to the desired amount of `token{0,1}`.

## Sending Tokens

What’s not as clear is how CRONEX _receives_ tokens as payment for the swap. Typically, smart contracts which need tokens to perform some functionality require callers to first make an approval on the token contract, then call a function that in turn calls transferFrom on the token contract. This is _not_ how CRONEX pairs accept tokens. Instead, pairs check their token balances at the _end_ of every interaction. Then, at the beginning of the _next_ interaction, current balances are differenced against the stored values to determine the amount of tokens that were sent by the current interactor.

The takeaway is that **tokens must be transferred to pairs before swap is called** (the one exception to this rule is Flash Swaps). This means that to safely use the `swap` function, it must be called from _another smart contract_. The alternative (transferring tokens to the pair and then calling `swap`) is not safe to do non-atomically because the sent tokens would be vulnerable to arbitrage.
