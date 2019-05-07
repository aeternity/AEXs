# AEX 2

```
AEX: 2
Title: Third-party Wallet Provider Support
Author: Shubhendu Shekhar (@shekhar-shubhendu), Andrea Giacobino (@noandrea), Enrico Icardi (@ricricucit) & Nazar Duchak (@nduchak)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-2-js-sdk-interfaces-for-wallets/2715
License-Code: Apache-2.0
Status: Draft
Type: Standards Track
Created: 2019-03-04
```

## Simple Summary

The document approaches the technical specification about how a wallet provider (ex. BaseApp, Metamask) can interact with Aeternity enabled applications.

## Motivation

Currently, there exist no one standard way for wallets and SDK to communicate and everything developed on the SDK side is developed only keeping base-aepp in mind and rest of the wallet providers need to follow the same path.
By defining the standard way of communication between SDK(aepps) and Wallet we will not only reduce the dependency that other wallet providers have on base-aepp as well as we'll have a clear standard method of communication on the SDK side that can be further extended whenever required.

## Specification

### Protocol Messages

(TODO: expand on each message and provide json-rpc)

#### General

- `aepp.wallet.error`: used to communicate any error occurred. this is a json-rpc 2.0 error object.

(TODO: list possible errors with codes. possible candidates:)

- `TX_VERIFICATION_ERROR`
- `INVALID_TX`
- `CONNECT_REJECT`
- `INVALID_ADDRESS`
- `ADDRESS_REJECT`
- `SIGN_REJECT`

#### By SDK/Aepp

- `aepp.wallet.accept`: connection acknowledgement send by the SDK.
- `aepp.get.address`: SDK requests for current address
- `aepp.request.sign`: SDK requests wallet for signature
- `aepp.broadcast.response`: standard response for `wallet.broadcast.tx` containing success message with tx id.
- `aepp.network`: network details wrapped in a response object

#### By Wallet

- `wallet.get.network`: get network details from sdk
- `wallet.request.connect`: connection request sent by the wallet.
- `wallet.update.address`: used by wallet for sending requested address. wallet can also send the list of address of the wallets it is further connected too
- `wallet.broadcast.tx`: ask SDK to broadcast the transaction.
- `wallet.verify.tx`: verify the tx from the SDK
- `wallet.aepp.disconnect`: wallet lets the aepp know that it will disconnect. no further acknowledgement required.
