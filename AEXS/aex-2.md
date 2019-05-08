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

#### General

- `error`: used to communicate any error occurred. Each error object will contain the `request` that triggered it.

##### Types of errors

**Code**|**Message**|**Meaning**
:-----:|:-----:|:-----:
1|Transaction verification failed|returned when verification of signed transaction fails.
2|Invalid transaction|returned by node for an invalid transaction.
3|Signature request denied|returned when wallet denies the signature request by Aepp/SDK.
4|Get address request denied|returned when wallet denies the address request by Aepp/SDK.

- `ping/pong`: general ping/pong messages to check liveness.

#### SDK/Aepp

- `aepp.get.address`: request for current address
- `aepp.request.sign`: request wallet for signature
- `aepp.broadcast.response`: standard response for `wallet.broadcast.tx` containing success message with transaction id.
- `aepp.update.network`: network details wrapped in a response object.

#### Wallet

- `wallet.get.network`: get network details from sdk
- `wallet.update.address`: used by wallet for sending requested address. wallet can also send the list of address of the wallets it is further connected too
- `wallet.broadcast.tx`: ask SDK to broadcast the transaction.
- `wallet.verify.tx`: verify the tx from the SDK
- `wallet.aepp.disconnect`: wallet lets the aepp know that it will disconnect. no further acknowledgement required.
