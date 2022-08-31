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

To learn how the SDK works from a high-level perspective, go to the [SDK Intro](./docs/intro/overview.md).

If you want to get started quickly and learn how to build on top of the SDK, please follow the [SDK Application Tutorial](https://tutorials.cosmos.network/nameservice/tutorial/00-intro.html). You can also fork the tutorial's repository to get started building your own Cosmos SDK application.

For more, please go to the [Cosmos SDK Docs](./docs/).

## Cosmos Hub Mainnet

The Cosmos Hub application, `gaia`, has moved to its [own repository](https://github.com/cosmos/gaia). Go there to join the Cosmos Hub mainnet and more.

## Interblockchain Communication (IBC)

The IBC module for the SDK has moved to its [own repository](https://github.com/cosmos/ibc-go). Go there to build and integrate with the IBC module.

## Starport

If you are starting a new app or a new module you can use [Starport](https://github.com/tendermint/starport) to help you get started and speed up development. If you have any questions or find a bug, feel free to open an issue in the repo.

## Disambiguation

This Cosmos-SDK project is not related to the [React-Cosmos](https://github.com/react-cosmos/react-cosmos) project (yet). Many thanks to Evan Coury and Ovidiu (@skidding) for this Github organization name. As per our agreement, this disambiguation notice will stay here.

## Changes to Cudos fork of cosmos-sdk

Below are described the changes that Cudos have implemented to the cosmos-sdk for the purpose of Cudos Network.

### MinSelfDelegation minimum value 2000000000000000000000000

When validator is created there is a setting being set about the minimum self delegation amount required for the validator to be operational. For Cudos Network this is required to be at least 2M CUDOS. This is achieved with a check in the `CreateValidator` function in staking module's `msg_server.go`. Also we've added a check in the standard `ValdiateBasic` function for `MsgCreateValidator`.

There is also an additional error type added in `types/errors.go`.

The majority of changes are for the tests. Since we now require validators to have that setting, we also have to rise the actual self delegation they put on themselves. From there we have to give the addresses more funds, the calculated rewards are different and so on. That makes it so many changes in the tests are required just for this small setting change.

### [CUDOS-805] Empty moniker is not allowed for validators

This is implemented due to CUDOS-805. Validators with empty monikers should not be allowed on Cudos Network.

This is achieved through checks added in `validator.go`'s `EnsureLength` function, in the `ValidateBasic` functions for Create  and edit validator messages and in the functions for cli tx. Some tests where validators are created had to be edited as well, to set them up with any mnemonic as opposed to the default empty mnemonic.

### Crisis transactions only by admin token holders

In the original cosmos-sdk the crisis module's invariant check messages could be sent by anyone. For Cudos Network we limited them to only `adminToken` holders, since they produce heavy load on the network and could be used as an attack vector.

This is done by adding a simple check if the message sender has at least one `adminToken` in crisis module's `msg_server.go`. The other changes include just a specific error for the case when the caller doesn't have `adminToken` and some test suite changes to accomodate tests for those changes.

To test the cli commands for the invariants, we needed to add `adminToken` to the integration test suite's validator and that made it so all the checks for balances in the suite had to be modified to expect that new token.

### added unsafe-reset-all on root level

In the original cosmos-sdk version v0.45.3 the `unsafe-reset-all` was moved from the root cmd level. We've returned it back there in order to not change all the deployment and upgrade scripts we have.

### Burned tokens to community pool

In the original cosmos-sdk there are several cases where tokens are being burned. We can separate them into two categories - staking coins burns and vouches (ibc denom tokens, Gravity Bridge denoms) burns. The vouches are being burned for the purpose of not going over the cap when transferring them back and forth. The staking coin's burns are more of a deflation mechanic.

In Cudos Network the burned staking coins (acudos) are sent to the community pool and the burn mechanic for the vouchers is left as it is.

This is achieved by changing the `BurnCoins` function in bank module's keeper. It is being called anywhere there is burning of coins, so it is a central place where we can implement the change with a single fix. 

For it to work the app's distribution keeper should be passed to the bank keeper. Because of this we've added a check in the `BurnCoins` function to check this. There is also another check for the denom of the coins being burned - if it is `acudos`, transfer them to the community pool, if it is any other - use the old logic to burn them.

There is also a new test added to check this functionality and a log anytime burn or transfer to community pool is done.
