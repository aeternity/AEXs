# AEX 5

```
AEX: 5
Title: Inter-Wallet Communication
Author: Shubhendu Shekhar <@shekhar-shubhendu>
License: CC0-1.0
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
5. Once the transaction is signed it is returned back to the requesting SDK for further processing.

## Specification

### Naming Convention

We try to enforce a `who - what - how` rule for naming wherever possible.

### Protocol Messages

- `wallet.channel.initiate`: initiate request to open a communication channel.
- `wallet.channel.accept`: accept `wallet.channel.initiate` request
- `wallet.channel.close`: close the channel
- `wallet.channel.close_ack`: acknowledgement for `close` request.
- `wallet.channel.close_incoming`: close channel only for signing requests i.e. closing wallet can still ask the wallet on other end to sign or forward the transactions.
- `wallet.channel.close_incoming_ack`: acknowledgment for `close_incoming`

- `wallet.get.address`: to ask the connected wallet for its address
- `wallet.address`: reply message with list of address(and maybe also addresses of other connected wallets)
- `wallet.update.address`: issued by connected wallets whenever there is address change
- `wallet.update.address_ack`: acknowledgement for successful address received
- `wallet.update.address_error`: error thrown if there is some error, like empty list, invalid format.

- `wallet.sign.tx_return`: ask the connected wallet to sign and return the signed transaction to the originating wallet.
- `wallet.sign.tx_broadcast`: ask the connected wallet to sign and if possible broadcast it to the network else see `wallet.tx.broadcast`.
- `wallet.tx.return`:
- `wallet.tx.broadcast`: if the wallet does not support broadcasting then it will send the message back from where it received under this message. Any wallet in the connection chain that is capable of broadcasting or connected to the SDK can broadcast the transaction.
- `wallet.tx.sign_failed`: if wallet is unable to sign the transaction of find someone who can
- `wallet.tx.sign_ack`: when requesting wallet receives the signed transaction and it is accepted by the SDK.
- `wallet.tx.sign_error`: when requesting wallet receives the signed transaction and there is error with the signed transaction (like signature mismatch etc).

### Message Flow