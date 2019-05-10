# AEX 5

```
AEX: 5
Title: Inter-Wallet Communication
Author: Shubhendu Shekhar <@shekhar-shubhendu>
License: BSD-3-Clause
Status: Draft
Type: Standards Track
Created: 2019-04-29
```

## Simple Summary

This document describes the process and messages required for inter wallet communication between aeternity supported wallets.

## Motivation

The purpose of AEXs is to provide specification and json-rpc compatible messages required for inter wallet communication channel.

### Current Process (with regards to AEX-2)

1. SDK connects with a wallet
2. Wallet shares the account address with the SDK
3. If, SDK needs the wallet to sign the transaction it asks the wallet
4. Wallet signs and returns the signed transaction.

### Proposed process with inter-wallet communication

1. SDK connects with a wallet
2. Wallet returns it's personal address and other account address it may help the SDK in getting signature for.
3. If, SDK needs the wallet to sign the transaction it asks the wallet
4. Wallet performs steps in the below order
    1. checks if it can sign the transaction.
    2. else it checks if it holds direct link to the wallet that can sign the transaction and forwards the request to it.
    3. and at last it checks if there is any indirect link to the signing address i.e. via another connected wallet and forwards the request to it.
5. Once the transaction is signed, it is returned back to the requesting SDK for further processing.

## Specification

### Protocol Messages

#### General

- `error`: used to communicate any error occurred. Error code `1` to `5` are reserved and reused here from [AEX-2](https://github.com/apeunit/AEXs/blob/feature/update-aex-2/AEXS/aex-2.md#types-of-errors).

- `ping/pong`: ping/pong messages for liveness check.

#### Start and Close

- `wallet.channel.close`: close the channel
- `wallet.channel.close_incoming`: close channel only for signing requests i.e. closing wallet can still ask the wallet on other end to sign or forward the transactions.

#### Address Update

- `wallet.get.address`: to ask the connected wallet for its address
- `wallet.update.address`: issued by connected wallets for returning addresses incl. from the connected wallets

#### Sign and Broadcast Transaction

- `wallet.sign.tx_return`: ask the connected wallet to sign and return the signed transaction to the originating wallet.
- `wallet.sign.tx_broadcast`: ask the connected wallet to sign and if possible broadcast it to the network else see `wallet.tx.broadcast`.
- `wallet.tx.return`: used to return signed transaction back to the requestor.
- `wallet.tx.broadcast`: if the wallet does not support broadcasting then it will send the message back from where it received under this message. Any wallet in the connection chain that is capable of broadcasting or connected to the SDK can broadcast the transaction.

### Example Flow
