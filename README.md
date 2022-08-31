<<<<<<< HEAD
<!--
parent:
  order: false
-->

<div align="center">
  <h1> Cosmos SDK </h1>
</div>

![banner](docs/cosmos-sdk-image.jpg)

<div align="center">
  <a href="https://github.com/cosmos/cosmos-sdk/releases/latest">
    <img alt="Version" src="https://img.shields.io/github/tag/cosmos/cosmos-sdk.svg" />
  </a>
  <a href="https://github.com/cosmos/cosmos-sdk/blob/master/LICENSE">
    <img alt="License: Apache-2.0" src="https://img.shields.io/github/license/cosmos/cosmos-sdk.svg" />
  </a>
  <a href="https://pkg.go.dev/github.com/cosmos/cosmos-sdk?tab=doc">
    <img alt="GoDoc" src="https://godoc.org/github.com/cosmos/cosmos-sdk?status.svg" />
  </a>
  <a href="https://goreportcard.com/report/github.com/cosmos/cosmos-sdk">
    <img alt="Go report card" src="https://goreportcard.com/badge/github.com/cosmos/cosmos-sdk" />
  </a>
  <a href="https://codecov.io/gh/cosmos/cosmos-sdk">
    <img alt="Code Coverage" src="https://codecov.io/gh/cosmos/cosmos-sdk/branch/master/graph/badge.svg" />
  </a>
</div>
<div align="center">
  <a href="https://github.com/cosmos/cosmos-sdk">
    <img alt="Lines Of Code" src="https://tokei.rs/b1/github/cosmos/cosmos-sdk" />
  </a>
  <a href="https://discord.gg/AzefAFd">
    <img alt="Discord" src="https://img.shields.io/discord/669268347736686612.svg" />
  </a>
  <a href="https://sourcegraph.com/github.com/cosmos/cosmos-sdk?badge">
    <img alt="Imported by" src="https://sourcegraph.com/github.com/cosmos/cosmos-sdk/-/badge.svg" />
  </a>
    <img alt="Sims" src="https://github.com/cosmos/cosmos-sdk/workflows/Sims/badge.svg" />
    <img alt="Lint Satus" src="https://github.com/cosmos/cosmos-sdk/workflows/Lint/badge.svg" />
</div>

The Cosmos-SDK is a framework for building blockchain applications in Golang.
It is being used to build [`Gaia`](https://github.com/cosmos/gaia), the first implementation of the Cosmos Hub.

**WARNING**: The SDK has mostly stabilized, but we are still making some
breaking changes.

**Note**: Requires [Go 1.15+](https://golang.org/dl/)

## Quick Start

To learn how the Cosmos SDK works from a high-level perspective, see the Cosmos SDK [High-Level Intro](./docs/intro/overview.md).

If you want to get started quickly and learn how to build on top of Cosmos SDK, visit [Cosmos SDK Tutorials](https://tutorials.cosmos.network). You can also fork the tutorial's repository to get started building your own Cosmos SDK application.

For more information, see the [Cosmos SDK Documentation](./docs/).

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for details how to contribute and participate in our [dev calls](./CONTRIBUTING.md#teams-dev-calls).
If you want to follow the updates or learn more about the latest design then join our [Discord](https://discord.com/invite/cosmosnetwork).

## Tools and Frameworks

The Cosmos ecosystem is vast. We will only make a few notable mentions here.

+ [Tools](https://v1.cosmos.network/tools): notable frameworks and modules.
+ [CosmJS](https://github.com/cosmos/cosmjs): the Swiss Army knife to power JavaScript based client solutions.

### Cosmos Hub Mainnet

The Cosmos Hub application, `gaia`, has moved to its own [cosmos/gaia repository](https://github.com/cosmos/gaia). Go there to join the Cosmos Hub mainnet and more.

### Inter-Blockchain Communication (IBC)

The IBC module for the Cosmos SDK has moved to its own [cosmos/ibc-go repository](https://github.com/cosmos/ibc-go). Go there to build and integrate with the IBC module.

## Starport

If you are starting a new app or a new module you can use [Starport](https://github.com/tendermint/starport) to help you get started and speed up development. If you have any questions or find a bug, feel free to open an issue in the repo.

## Disambiguation

This Cosmos-SDK project is not related to the [React-Cosmos](https://github.com/react-cosmos/react-cosmos) project (yet). Many thanks to Evan Coury and Ovidiu (@skidding) for this Github organization name. As per our agreement, this disambiguation notice will stay here.

## Changes to Cudos fork of cosmos-sdk

Below are described the changes that Cudos have implemented to the cosmos-sdk for the purpose of Cudos Network.

### Enabling flag parsing for evidence module

This is implemented fue to CUDOS-815 bug. Following query evidence cli command documentation, when passing no arguments it should return all the existing evidences. However, it looks like the command is parsing incorrectly the arguments, since cudos-noded q evidence returns error `Error: accepts at most 1 arg(s), received 5`

The problem was that the settings of the evidence module query Txs was ignoring flags, furthermore considering them as arguments of the call, leading to troublesome behaviour.

The fix is just changing `DisableFlagParsing` to falsein evidence module's `cli/query.go` `GetQueryCmd`.
>>>>>>> cudos-v0.45.3-fix-enabling-flag-parsing-for-evidence-module

## Changes to Cudos fork of cosmos-sdk

Below are described the changes that Cudos have implemented to the cosmos-sdk for the purpose of Cudos Network.

### CUDOS-1464 added authz non-determinism module fix

There was a non-determinism bug in cosmos-sdk, which was quickly fixed in new coosmos-sdk versions. We added it manually so we can have it asap.

The fix consists in sorting the keys in `events.go`'s `TypedEventToEvent`, before iterating over them, so that they are always iterated in the same order.

There was also a test added.
