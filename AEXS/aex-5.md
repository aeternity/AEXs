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

  json-rpc 2.0 structure:

  ```json
  Ping:
  {
    "jsonrpc": "2.0",
    "method": "ping",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "id": 1,
    "version": 1
  }

  Pong:
  {
    "jsonrpc": "2.0",
    "result": {
       "id": "<unique_identifier_uuidv4>",
       "data": "pong"
    },
    "id": 1,
    "version": 1
  }
  ```

#### Start and Close

- `wallet.channel.initiate`: initiate request to open a communication channel. The request contains an identifier that will be used by either party to recognize and process incoming messages. The generated identifier must be unique and must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14).

  json-rpc 2.0 structure:

  ```json
  Request:
  {
    "jsonrpc": "2.0",
    "method": "wallet.channel.initiate",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }

  Response:
  {
    "jsonrpc": "2.0",
    "result": {
       "id": "<unique_identifier_uuidv4_as_request>",
       "name": "<optional_name_param>"
    },
    "id": 1,
    "version": 1
  }
  ```

- `wallet.channel.close`: close the channel

  json-rpc 2.0 structure:

  ```json
  Request:
  {
    "jsonrpc": "2.0",
    "method": "wallet.channel.close",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }

  Response:
  {
    "jsonrpc": "2.0",
    "result": {
       "id": "<unique_identifier_uuidv4_as_request>",
       "status": "ack"
    },
    "id": 1,
    "version": 1
  }
  ```

- `wallet.channel.close_incoming`: close channel only for signing requests i.e. closing wallet can still ask the wallet on other end to sign or forward the transactions.

  json-rpc 2.0 structure:

  ```json
  Request:
  {
    "jsonrpc": "2.0",
    "method": "wallet.channel.close_incoming",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }

  Response:
  {
    "jsonrpc": "2.0",
    "result": {
       "id": "<unique_identifier_uuidv4_as_request>",
       "status": "ack"
    },
    "id": 1,
    "version": 1
  }
  ```

#### Address Update

- `wallet.get.address`: to ask the connected wallet for its address

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.get.address",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "id":1,
    "version": 1
  }
  ```

- `wallet.update.address`: issued by connected wallets for returning addresses incl. from the connected wallets

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.update.address",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "address": {
         "current": {
           "<address>": {
             // object with related metadata(optional)
           }
         },
         "connected": {
           "<address1>": {
             // object with related metadata(optional)
           },
           "<address2>": {
             // object with related metadata(optional)
           }
         }
       }
    },
    "version": 1
  }
  ```

#### Sign and Broadcast Transaction

- `wallet.sign.tx`: ask the connected wallet to sign the tx. If the `return` param is `true` then wallet will sign and return the transaction to the requestor

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.sign.tx",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "return": true/false
    },
    "id":1,
    "version": 1
  }
  ```

- `wallet.broadcast.tx`: if the wallet does not support broadcasting then it will send the message back from where it received under this message. Any wallet in the connection chain that is capable of broadcasting or connected to the SDK can broadcast the transaction.

### Example Flow
