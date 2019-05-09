# AEX 2

```
AEX: 2
Title: Third-party Wallet Provider Support
Author: Shubhendu Shekhar (@shekhar-shubhendu), Andrea Giacobino (@noandrea), Enrico Icardi (@ricricucit) & Nazar Duchak (@nduchak)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-2-js-sdk-interfaces-for-wallets/2715
License-Code: Apache-2.0
Status: Review
Type: Standards Track
Created: 2019-03-04
```

## Simple Summary

The document approaches the technical specification about how a wallet provider (ex. BaseApp, MetaMask) can interact with Aeternity enabled applications.

## Motivation

Currently, there exist no one standard way for wallets and SDK to communicate and everything developed on the SDK side is developed only keeping base-aepp in mind and rest of the wallet providers need to follow the same path.
By defining the standard way of communication between SDK(aepps) and Wallet we will not only reduce the dependency that other wallet providers have on base-aepp as well as we'll have a clear standard method of communication on the SDK side that can be further extended whenever required.

## Specification

### Protocol Messages

#### General

- `error`: used to communicate any error occurred. Each error object will contain the `request` that triggered it.

  json-rpc 2.0 structure:

  ```json
  {
  "error": {
    "code": 1,
    "data": {
      "request": {}
    },
    "message": ""
    },
    "id": null,
    "jsonrpc": "2.0",
    "version": 1
  }
  ```

    ##### Types of errors

    **Code**|**Message**|**Meaning**
    :-----:|:-----:|:-----:
    1|Transaction verification failed|returned when verification of signed transaction fails.
    2|Invalid transaction|returned by node for an invalid transaction.
    3|Signature request denied|returned when wallet denies the signature request by Aepp/SDK.
    4|Get address request denied|returned when wallet denies the address request by Aepp/SDK.
    5|Invalid Address|returned by aepp when the address (or any address in a list) provided by wallet is invalid.
    6|Unknown Identifier| returned by aepp or wallet when the enclosing `id` is unknown to the receiving party or is missing entirely.
    7|Malformed Identifier| returned by aepp or wallet when the enclosing `id` does not conform to UUID v4 standards.

- `ping/pong`: general ping/pong messages to check liveness.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "ping",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }
  ```

  ```json
  {
    "jsonrpc": "2.0",
    "method": "pong",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }
  ```

#### SDK/Aepp

- `aepp.get.address`: request for current address.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "aepp.get.address",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }
  ```

- `aepp.request.sign`: request wallet for signature

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "aepp.request.sign",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "tx": "<raw_unsigned_tx>"
    },
    "version": 1
  }

- `aepp.broadcast.response`: standard response for `wallet.broadcast.tx` containing success message with transaction id.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "aepp.broadcast.response",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "tx_id": "<tx_id>"
    },
    "version": 1
  }

- `aepp.update.network`: network details wrapped in a response object.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "aepp.update.network",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "network": "<network_id>"
    },
    "version": 1
  }

- `aepp.accept.wallet`: response from sdk/aepp when it accepts the wallets registration request.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "aepp.accept.wallet",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "name": "<aepp_name>"
    },
    "version": 1
  }

#### Wallet

- `wallet.request.connect`: connection request sent by wallet containing an identifier that it wants to assign to the aepp/sdk. The generated identifier must be unique and must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14).

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.request.connect",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }

- `wallet.get.network`: get network details from sdk

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.get.network",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }

- `wallet.update.address`: used by wallet for sending requested address. wallet can also send the list of address of the wallets it is further connected to.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "aepp.accept.wallet",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "address": "<current_wallet_address>",
       "connected": "[List of addresses from the connected wallets]"
    },
    "version": 1
  }

- `wallet.broadcast.tx`: ask SDK to broadcast the transaction.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.broadcast.tx",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "tx": "<signed_tx>",
       "verify": true/false
    },
    "version": 1
  }

- `wallet.verify.tx`: verify the tx from the SDK

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.verify.tx",
    "params": {
       "id": "<unique_identifier_uuidv4>",
       "tx": "<signed_tx>"
    },
    "version": 1
  }

- `wallet.disconnect.aepp`: wallet lets the aepp know that it will disconnect. no further acknowledgement required.

  json-rpc 2.0 structure:

  ```json
  {
    "jsonrpc": "2.0",
    "method": "wallet.disconnect.aepp",
    "params": {
       "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }

## Example Flow

<p align="center">
<img src="../assets/aex-2/example-flow.png" width="400px">
</p>