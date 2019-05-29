# AEX 2

```
AEX: 2
Title: Third-party Wallet Provider Support
Author: Shubhendu Shekhar (@shekhar-shubhendu), Andrea Giacobino (@noandrea), Enrico Icardi (@ricricucit) & Nazar Duchak (@nduchak)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-2-js-sdk-interfaces-for-wallets/2715
License-Code: Apache-2.0
Status: Last call (2019-05-29 to 2019-06-04)
Type: Standards Track
Created: 2019-03-04
```

## Simple Summary

The document approaches the technical specification about how a wallet provider (ex. Base æpp, MetaMask) can interact with Aeternity enabled applications.

## Motivation

Currently, there exist no one standard way for wallets and SDK to communicate and everything developed on the SDK side is developed only keeping the Base æpp in mind and rest of the wallet providers need to follow the same path.
By defining the standard way of communication between SDK(aepps) and Wallet we will not only reduce the dependency that other wallet providers have on the Base æpp as well as we'll have a clear standard method of communication on the SDK side that can be further extended whenever required.

## Specification

### Naming Convention

This document tries to closely follow `who.what.how` naming convention for JSON-RPC methods(wherever possible).

### JSON-RPC 2.0 Methods

#### General

- `error`: used to communicate any error occurred. Embedded `data` field in each error object MUST contain the `request` that triggered it.

  ##### JSON-RPC 2.0 Error Example

  ```json
      {
        "error": {
            "code": 1,
            "data": {
                "request": {}
            },
            "message": "Transaction verification failed"
        },
        "id": null,
        "jsonrpc": "2.0"
    }
  ```

  ##### Types of errors

    |**Code**|**Message**|**Meaning**|
    |:-----:|:-----:|:-----:|
    |1|Transaction verification failed|returned when verification of signed transaction fails.|
    |2|Invalid transaction|returned by node for an invalid transaction.|
    |3|Broadcast Failed| returned when Aepp/SDK loses connection to the node and is unable to broadcast the transaction.|
    |4|Signature request denied|returned when wallet denies the signature request by Aepp/SDK.|
    |5|Get address request denied|returned when wallet denies the address request by Aepp/SDK.|
    |6|Invalid Address|returned by aepp when the address (or any address in a list) provided by wallet is invalid.|
    |7|Unknown Identifier| returned by aepp or wallet when the enclosing `id` is unknown to the receiving party or is missing entirely.|
    |8|Malformed Identifier| returned by aepp or wallet when the enclosing `id` does not conform to UUID v4 standards.|
    |9|Unsupported Protocol Version| returned by aepp when it does not support protocol version the wallet wants to connect through.

#### SDK/Aepp

- `aepp.get.address`: request for address from wallet.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)

- `aepp.request.sign`: request wallet for signature

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)
  - `tx` - raw unsigned transaction

  ##### Returns

    `Object`

  - `id` - same id as in the corresponding request
  - `tx` - signed tx returned by the wallet

- `aepp.update.network`: Message sent by Aepp/SDK asking wallet to update the network details.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)

  - `network` - Current network id used by the Aepp/SDK.

#### Wallet

- `wallet.request.connect`: connection request sent by the wallet containing an identifier that it wants to assign to the aepp/sdk. The provided identifier should be used for all the further communication between the aepp and wallet.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)
  - `name` - human readable wallet name
  - `version` - protocol version. Currently defaults to `1`.

  ##### Returns

    `Object`

  - `id` - same id as in the corresponding request
  - `name` - human readable aepp name

- `wallet.get.network`: get network details from sdk

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)

- `wallet.update.address`: used by wallet for sending requested address. wallet can also send the list of address of the wallets it is further connected to.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)
  - `address` - Object containing two objects named `current` and `connected`.

    - `current` - Object containing only a single account currently in use by the wallet.

    - `connected`(optional) - Object containing multiple connected accounts.

      ###### Example Account Format

        ```json
        {
            "current": {
                "ak_2iBPH7HUz3cSDVEUWiHg76MZJ6tZooVNBmmxcgVK6VV8KAE688": {
                    "name": "this property is optional"
                }
            }
        }
        ```

- `wallet.broadcast.tx`: Ask Aepp/SDK to broadcast the transaction. If the Aepp/SDK is unable to broadcast it returns the `error`  with code `3`.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)
  - `tx` - signed transaction to be broadcasted
  - `verify` - Boolean. Perform verification before broadcasting or not.

  ##### Returns

    `Object`

  - `id` - same id as in the corresponding request
  - `tx_id` - transaction id of the broadcasted transaction

- `wallet.verify.tx`: verify the tx from the SDK. On verification success, a response object is returned else with `status` field with a value `ok` else error with `code 1` is returned.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)
  - `tx` - signed transaction to be verified

  ##### Returns

    `Object`

  - `id` - same id as in the corresponding request
  - `status` - Value will always be equal to `ok`

- `wallet.disconnect.aepp`: wallet lets the aepp know that it will disconnect. no further acknowledgement required.

    ##### Parameters

    `Object`

  - `id` - A unique identifier, must conform to the [UUID v4 standards](https://tools.ietf.org/html/rfc4122#page-14)

## Example Flow

<p align="center">
<img src="https://github.com/aeternity/AEXs/raw/master/assets/aex-2/example-flow.png" width="400px">
</p>