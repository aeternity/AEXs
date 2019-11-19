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

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to
be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

This document describes the technical specification and methods that the wallet provider (ex. Base æpp, Wællet, MetaMask) MUST use to interact with Aeternity based applications (hereinafter referred to as 'aepp' or 'aepps').

## Motivation

Currently, there exists no standard way for wallets and aepps to communicate and everything developed on the SDK side is developed only keeping the Base æpp in mind and rest of the wallet providers need to follow the same path.
By defining the standard way of communication between aepps and Wallet we will not only reduce the dependency that other wallet providers have on the Base æpp as well as we'll have a clear standard method of communication on the SDK side that can be further extended upon whenever required.

## Specification

### Naming Convention

Wherever possible, this document tries to closely follow the `who.what.how` naming convention for JSON-RPC methods.

### JSON-RPC 2.0 Specification

#### Error

JSON-RPC 2.0 response error object that is used to communicate any error occurred while processing the request.

##### Types of errors

  |**Code**|**Message**|**Meaning**|
  |:-----:|:-----:|:-----:|
  |1|Invalid transaction| MUST be returned whenever the transaction validity check fails|
  |2|Rejected by user| MUST be returned by the wallet when user denies the action request by aepp.|
  |3|Unsupported protocol version| MUST be returned by wallet when it does not support protocol version the aepp wants to connect through.|
  |4|Already subscribed|MUST be returned by wallet when aepp is already subscribed with the same name.|
  |5|Not subscribed|MUST be returned by wallet when aepp requested to unsubscribe itself from a subscription that it wasn't subscribed to.|

#### Methods

##### Aepp Invokable Methods

This section defines the methods that the aepps MUST invoke to either get information from the wallet or request the wallet to perform an action.  Omitted "Parameters" section means that the corresponding method method doesn't accept any parameters. Omitted "Returns" section means that the corresponding method method MUST return `Null`.

Methods related to subscription flow SHOULD be omitted when used transports in which Wallet can't initiate message sending (for example, QrCodes or URL scheme calls). After calling method for subscription the corresponding result MUST be returned immediately through `subscription` notification.

- `connection.open`: connection request sent by the aepp to the wallet.

  **Parameters**

    _Object_

    - `version`: protocol version. Currently defaults to `1`.

  **Returns**

    _Object_

    - `networkId`: Network id used by the wallet
    
  **Returns errors**
  
  - Unsupported protocol version
  - Unsupported network

- `addresses.get`: request the wallet to return addresses.

  **Returns** 
  - array of addresses that can be used for transaction signing (Datatype: array of strings)

  **Returns errors**
  - Rejected by user

- `networkId.get`: request the wallet to return network id.

  **Returns**
  - id of current network (Datatype: string)

- `subscribe`: request the wallet to subscribe to changes of the return value of some method.

  **Parameters**
  - name of method to subscribe to (Datatype: string)

  **Returns errors**
  - Already subscribed

- `unsubscribe`: request the wallet to unsubscribe from data changes.

  **Parameters**
  - name of method to unsubscribe from (Datatype: String).

  **Returns errors**
  - Not subscribed

- `transaction.sign`: request wallet for signature

  **Parameters**
  - unsigned encoded transaction (Datatype: String).

  **Returns**
    signed encoded transaction (Datatype: String)
    
  **Returns errors**
  - Invalid transaction
  - Rejected by user

Aepp MUST be able to subscribe to return values these methods:
  - `addresses.get`
  - `networkId.get`

#### Notifications

##### Wallet Invokable Notifications

- `connection.announcePresence`: MAY be used by the wallets to announce their presence wherever required (e.g. postMessage API). This message SHOULD NOT be used by the wallets where a 1-to-1 connection with the aepp is already established but instead wait for the aepp to initiate the connection using `connection.open` message.

  Note: This is a helper message for the aepp to identify the presence of a wallet.

  **Parameters**

    _Object_

    - `networkId`: Network id used by the wallet

- `subscription.update`: MUST be used by Wallet to notify subscribed Aepp about changes of return value of method that aepp is subscribed to.

  **Parameters**
  _Object_
  - `name`: the name of method that aepp is subscribed to
  - `result`: value returned by method that aepp is subscribed to, MUST NOT exist if there was error triggered during invocation (Datatype: JSON Object)
  - `error`: error object returned by method that aepp is subscribed to, MUST NOT exist if there was no error triggered during invocation (Datatype: JSON Object)

##### Invokable by Wallet and Aepp

- `connection.close`: SHOULD be used by Aepp or Wallet for informing the other party that it is disconnecting and further requests will either be denied or not acknowledged.

## Example Flow

<p align="center">
<img src="../assets/aex-2/example-flow.png" width="400px">	
</p>

## Reference Implementation

- Aepp: https://github.com/aeternity/aepp-sdk-js/tree/feature/aex-2/examples/browser/vuejs/connect-two-ae

- Extension Wallet: https://github.com/aeternity/aepp-sdk-js/tree/feature/aex-2/examples/browser/extension

## References

- Transaction Encoding and Serialization
  https://github.com/aeternity/protocol/blob/master/node/api/api_encoding.md
  https://github.com/aeternity/protocol/blob/master/serializations.md

- JSON-RPC 2.0 Specification
  https://www.jsonrpc.org/specification
