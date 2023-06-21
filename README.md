<div align="center">
  <h1> Cosmos SDK </h1>
</div>

![banner](docs/static/img/banner.jpg)

<div align="center">
  <a href="https://github.com/cosmos/cosmos-sdk/blob/main/LICENSE">
    <img alt="License: Apache-2.0" src="https://img.shields.io/github/license/cosmos/cosmos-sdk.svg" />
  </a>
  <a href="https://pkg.go.dev/github.com/cosmos/cosmos-sdk">
    <img src="https://pkg.go.dev/badge/github.com/cosmos/cosmos-sdk.svg" alt="Go Reference">
  </a>
  <a href="https://goreportcard.com/report/github.com/cosmos/cosmos-sdk">
    <img alt="Go report card" src="https://goreportcard.com/badge/github.com/cosmos/cosmos-sdk" />
  </a>
  <a href="https://codecov.io/gh/cosmos/cosmos-sdk">
    <img alt="Code Coverage" src="https://codecov.io/gh/cosmos/cosmos-sdk/branch/main/graph/badge.svg" />
  </a>
</div>
<div align="center">
  <a href="https://discord.gg/AzefAFd">
    <img alt="Discord" src="https://img.shields.io/discord/669268347736686612.svg" />
  </a>
  <a href="https://sourcegraph.com/github.com/cosmos/cosmos-sdk?badge">
    <img alt="Imported by" src="https://sourcegraph.com/github.com/cosmos/cosmos-sdk/-/badge.svg" />
  </a>
    <img alt="Sims" src="https://github.com/cosmos/cosmos-sdk/workflows/Sims/badge.svg" />
    <img alt="Lint Satus" src="https://github.com/cosmos/cosmos-sdk/workflows/Lint/badge.svg" />
</div>

The Cosmos SDK is a framework for building blockchain applications. [Tendermint Core (BFT Consensus)](https://github.com/tendermint/tendermint) and the Cosmos SDK are written in the Go programming language. Cosmos SDK is used to build [Gaia](https://github.com/cosmos/gaia), the implementation of the Cosmos Hub.

**WARNING**: The Cosmos SDK has mostly stabilized, but we are still making some breaking changes.

**Note**: Requires [Go 1.19+](https://go.dev/dl)

## Quick Start

To learn how the Cosmos SDK works from a high-level perspective, see the Cosmos SDK [High-Level Intro](https://docs.cosmos.network/main/intro/overview.html).

If you want to get started quickly and learn how to build on top of Cosmos SDK, visit [Cosmos SDK Tutorials](https://tutorials.cosmos.network). You can also fork the tutorial's repository to get started building your own Cosmos SDK application.

For more information, see the [Cosmos SDK Documentation](https://docs.cosmos.network).

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for details on how to contribute and participate in our [dev calls](./CONTRIBUTING.md#teams-dev-calls).
If you want to follow the updates or learn more about the latest design then join our [Discord](https://discord.com/invite/cosmosnetwork).

## Tools and Frameworks

The Cosmos ecosystem is vast.
[Awesome Cosmos](https://github.com/cosmos/awesome-cosmos) is a community-curated list of notable frameworks, modules and tools.

### Cosmos Hub Mainnet

The Cosmos Hub application, `gaia`, has its own [cosmos/gaia repository](https://github.com/cosmos/gaia). Go there to join the Cosmos Hub mainnet and more.

### Inter-Blockchain Communication (IBC)

The IBC module for the Cosmos SDK has its own [cosmos/ibc-go repository](https://github.com/cosmos/ibc-go). Go there to build and integrate with the IBC module.

## Disambiguation

This Cosmos SDK project is not related to the [React-Cosmos](https://github.com/react-cosmos/react-cosmos) project (yet). Many thanks to Evan Coury and Ovidiu (@skidding) for this Github organization name. As per our agreement, this disambiguation notice will stay here.

## Changes to Cudos fork of cosmos-sdk

Below are described the changes that Cudos have implemented to the cosmos-sdk for the purpose of Cudos Network.

### Burned tokens to community pool

In the original cosmos-sdk there are several cases where tokens are being burned. We can separate them into two categories - staking coins burns and vouches (ibc denom tokens, Gravity Bridge denoms) burns. The vouches are being burned for the purpose of not going over the cap when transferring them back and forth. The staking coin's burns are more of a deflation mechanic.

In Cudos Network the burned staking coins (acudos) are sent to the community pool and the burn mechanic for the vouchers is left as it is.

This is achieved by changing the `BurnCoins` function in bank module's keeper. It is being called anywhere there is burning of coins, so it is a central place where we can implement the change with a single fix. 

For it to work the app's distribution keeper should be passed to the bank keeper. Because of this we've added a check in the `BurnCoins` function to check this. There is also another check for the denom of the coins being burned - if it is `acudos`, transfer them to the community pool, if it is any other - use the old logic to burn them.

There is also a new test added to check this functionality and a log anytime burn or transfer to community pool is done.
