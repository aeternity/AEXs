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

  ```jsonc
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
    3|Broadcast Failed| returned when Aepp/SDK loses connection to the node and is unable to broadcast the transaction.
    4|Signature request denied|returned when wallet denies the signature request by Aepp/SDK.
    5|Get address request denied|returned when wallet denies the address request by Aepp/SDK.
    6|Invalid Address|returned by aepp when the address (or any address in a list) provided by wallet is invalid.
    7|Unknown Identifier| returned by aepp or wallet when the enclosing `id` is unknown to the receiving party or is missing entirely.
    8|Malformed Identifier| returned by aepp or wallet when the enclosing `id` does not conform to UUID v4 standards.

- `ping/pong`: general ping/pong messages to check liveness.

  json-rpc 2.0 structure:

  ```jsonc
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

#### SDK/Aepp

- `aepp.get.address`: request for current address.

  json-rpc 2.0 structure:

  ```jsonc
  {
    "jsonrpc": "2.0",
    "method": "aepp.get.address",
    "params": {
      "id": "<unique_identifier_uuidv4>"
    },
    "id": 1,
    "version": 1
  }
  ```

- `aepp.request.sign`: request wallet for signature

  json-rpc 2.0 structure:

  ```jsonc
  Request:
  {
    "jsonrpc": "2.0",
    "method": "aepp.request.sign",
    "params": {
      "id": "<unique_identifier_uuidv4>",
      "tx": "<raw_unsigned_tx>"
    },
    "id": 1,
    "version": 1
  }

  Response:
  {
    "jsonrpc": "2.0",
    "result": {
      "id": "<unique_identifier_uuidv4>",
      "tx": "<signed_tx>"
    },
    "id": 1,
    "version": 1
  }
  ```

- `aepp.update.network`: Message sent by Aepp/SDK asking wallet to update the network details.

  json-rpc 2.0 structure:

  ```jsonc
  {
    "jsonrpc": "2.0",
    "method": "aepp.update.network",
    "params": {
      "id": "<unique_identifier_uuidv4>",
      "network": "<network_id>"
    },
    "version": 1
  }
  ```

#### Wallet

- `wallet.request.connect`: connection request sent by wallet containing an identifier that it wants to assign to the aepp/sdk. The generated identifier must be unique and must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14).

  json-rpc 2.0 structure:

  ```jsonc
  Request:
  {
    "jsonrpc": "2.0",
    "method": "wallet.request.connect",
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
      "name": "<aepp_name>"
    },
    "id": 1,
    "version": 1
  }
  ```

- `wallet.get.network`: get network details from sdk

  json-rpc 2.0 structure:

  ```jsonc
  {
    "jsonrpc": "2.0",
    "method": "wallet.get.network",
    "params": {
      "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }
  ```

- `wallet.update.address`: used by wallet for sending requested address. wallet can also send the list of address of the wallets it is further connected to.

  json-rpc 2.0 structure:

  ```jsonc
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

- `wallet.broadcast.tx`: Ask Aepp/SDK to broadcast the transaction. If the Aepp/SDK is unable to broadcast it returns the `error`  with code `3`.

  json-rpc 2.0 structure:

  ```jsonc
  Request:
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

  Response:
  {
    "jsonrpc": "2.0",
    "result": {
      "id": "<unique_identifier_uuidv4>",
      "tx_id": "<tx_id>"
    },
    "id": 1,
    "version": 1
  }
  ```

- `wallet.verify.tx`: verify the tx from the SDK. On verification success, a response object is returned else with `status` field with a value `ok` else error with `code 1` is returned.

  json-rpc 2.0 structure:

  ```jsonc
  Request:
  {
    "jsonrpc": "2.0",
    "method": "wallet.verify.tx",
    "params": {
      "id": "<unique_identifier_uuidv4>",
      "tx": "<signed_tx>"
    },
    "id": 1,
    "version": 1
  }

  Response:
  {
    "jsonrpc": "2.0",
    "result": {
      "id": "<unique_identifier_uuidv4>",
      "status": "ok"
    },
    "id": 1,
    "version": 1
  }
  ```

- `wallet.disconnect.aepp`: wallet lets the aepp know that it will disconnect. no further acknowledgement required.

  json-rpc 2.0 structure:

  ```jsonc
  {
    "jsonrpc": "2.0",
    "method": "wallet.disconnect.aepp",
    "params": {
      "id": "<unique_identifier_uuidv4>"
    },
    "version": 1
  }
  ```

## Example Flow

<p align="center">
<img src="../assets/aex-2/example-flow.png" width="400px">
</p>
