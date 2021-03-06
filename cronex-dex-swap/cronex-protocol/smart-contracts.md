# Smart Contracts

CRONEX is a binary smart contract system. Core contracts provide fundamental safety guarantees for all parties interacting with CRONEX. Periphery contracts interact with one or more core contracts but are not themselves part of the core.

## Core

The core consists of a singleton factory and many pairs, which the factory is responsible for creating and indexing. These contracts are quite minimal, even brutalist. The simple rationale for this is that contracts with a smaller surface area are easier to reason about, less bug-prone, and more functionally elegant. Perhaps the biggest upside of this design is that many desired properties of the system can be asserted directly in the code, leaving little room for error. One downside, however, is that core contracts are somewhat user-unfriendly. In fact, interacting directly with these contracts is not recommended for most use cases. Instead, a periphery contract should be used.

### Factory[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#factory) <a href="#factory" id="factory"></a>

The factory holds the generic bytecode responsible for powering pairs. Its primary job is to create one and only one smart contract per unique token pair. It also contains logic to turn on the protocol charge.

### Pairs[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#pairs) <a href="#pairs" id="pairs"></a>

Pairs have two primary purposes: serving as automated market makers and keeping track of pool token balances. They also expose data which can be used to build decentralized price oracles.

## Periphery

The periphery is a constellation of smart contracts designed to support domain-specific interactions with the core. Because of CRONEX's permissionless nature, the contracts described below have no special privileges, and are in fact only a small subset of the universe of possible periphery-like contracts. However, they are useful examples of how to safely and efficiently interact with CRONEX.

### Library[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#library) <a href="#library" id="library"></a>

The library provides a variety of convenience functions for fetching data and pricing.

### Router[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#router) <a href="#router" id="router"></a>

The router, which uses the library, fully supports all the basic requirements of a front-end offering trading and liquidity management functionality. Notably, it natively supports multi-pair trades (e.g. x to y to z), treats CRO as a first-class citizen, and offers meta-transactions for removing liquidity.

## Design Decisions

The following sections describe some of the notable design decisions made in CRONEX. These are safe to skip unless you're interested in gaining a deep technical understanding of how CRONEX works under the hood, or writing smart contract integrations!

### Sending Tokens[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#sending-tokens) <a href="#sending-tokens" id="sending-tokens"></a>

Typically, smart contracts which need tokens to perform some functionality require would-be interactors to first make an approval on the token contract, then call a function that in turn calls transferFrom on the token contract. This is _not_ how CRONEX pairs accept tokens. Instead, pairs check their token balances at the _end_ of every interaction. Then, at the beginning of the _next_ interaction, current balances are differenced against the stored values to determine the amount of tokens that were sent by the current interactor. The takeaway is that **tokens must be transferred to the pair before calling any token-requiring method.**

### WCRO[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#weth) <a href="#weth" id="weth"></a>

CRONEX pairs do not support CRO directly, so CRO⇄CRC-20 pairs must be emulated with WCRO. The motivation behind this choice was to remove CRO-specific code in the core, resulting in a leaner codebase. End users can be kept fully ignorant of this implementation detail, however, by simply wrapping/unwrapping CRO in the periphery.

The router fully supports interacting with any WCRO pair via CRO.

### Minimum Liquidity[​](https://docs.uniswap.org/protocol/V2/concepts/protocol-overview/smart-contracts#minimum-liquidity) <a href="#minimum-liquidity" id="minimum-liquidity"></a>

To ameliorate rounding errors and increase the theoretical minimum tick size for liquidity provision, pairs burn the first MINIMUM\_LIQUIDITY pool tokens. For the vast majority of pairs, this will represent a trivial value. The burning happens automatically during the first liquidity provision, after which point the totalSupply is forevermore bounded.
