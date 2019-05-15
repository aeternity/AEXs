# AEX 7

```
AEX: 7
Title: Data Serialization
Author: Andreas Gassmann (@AndreasGassmann), Alessandro De Carli (@dcale)
License: BSD-3-Clause
Discussions-To: TBD
Status: Draft
Type: Standards Track
Created: 2019-05-13
```

## Simple Summary

This document defines a serialization standard to facilitate data transfer between DApps, Wallets and devices.

## Motivation

Data needs to be transferred between different apps and devices. Those apps may or may not be located on the same device. The transport layer used by those apps is unknown and will not be part of this AEX. However, since many of the transport layers can only transfer a limited amount of data (eg. QR codes, Bluetooth, NFC, URLs), the data should always be encoded in a standardized and space efficient way.

The data serialization protocol in this AEX aims to provide a solution with the following goals in mind:

- Easy understand and implement
- Space efficient
- Make sure to not transmit redundant information, send only what is necessary
- Allow airgapped Apps such as AirGap Vault to properly display all necessary information when signing a transaction

## Specification

### Serialization - RLP

Basic description of [RLP](https://github.com/ethereum/wiki/wiki/RLP).

We use RLP for the following reasons:

- RLP is being used throughout the crypto space, with Aeternity adapting RLP as well after Ethereum
- It is able to serialize and deserialize data in a predictable way
- Easy to understand and implement
- Encoded Data as Binary, size-efficient

### Encoding

The resulting string will be encoded with `base58` because it is URL-safe, which is important for some transport layers.

### Transport Layers

The different transport layers are not part of this AEX, but we should keep the following transport layers in mind:

- QR Codes
- Deeplinking / URLs
- Clipboard copy/paste
- Push Notifications
- WebSockets
- Webhooks
- Bluetooth
- NFC

### Example

```typescript
[
  1, // ProtocolVersion
  1, // SerializationType: Full or Chunked
  [
    // Array of Messages
    [
      1, // MessageVersion
      2, // MessageType
      "ae", // Protocol
      "payload" // Payload depending on the MessageType
    ],
    [
      1, // MessageVersion
      3, // MessageType
      "ae", // Protocol
      "payload" // Payload depending on the MessageType
    ]
  ]
];
```

This example would result in the following, base58 encoded, string:

```
2hDLW1FiwvQs5ofPUgi5CgAJKDWiNncCoETXf7DGdkkDmrhN3z
```

The payload can be another RLP array, depending on the `MessageType`.

### Paging / Chunks

While the transport layer is not part of this AEX, we have to think about how we can split up a request if it doesn't fit into one "packet", depending on the transport layer.

The protocol should offer a "chunking" functionality where the user can pass a maximum size per chunk.

```typescript
function serialize(rlp, maxChunkSizeInByte): string[] {}
function deserialize(chunks): any[] {}

const chunks = serialize(rlp, 1024);
const deserializedRlp = deserialize(chunks);
```

```typescript
[
  1, // Protocol Version
  1, // SerializationType: Full or Chunked
  [
    1, // Current Page
    4, // Number of pages
    "payload of current chunk as string"
  ]
];
```

### Message Types

This standard allows for new message types to be added by every application. But ideally, we should provide a set of commonly used types so they can be re-used between different apps. Some of the default types could be:

- MetadataRequest (Information about the app, like name, version, url)
- MetadataResponse
- AccountShareRequest
- AccountShareResponse
- TransactionSignRequest
- TransactionSignResponse
- MessageSignRequest
- MessageSignResponse

### Versioning

There are 2 different kinds of versions:

- Protocol Version: Defines the version of the sync protocol. If this does not match, then the request most likely cannot be processed.
- Message Version: This version is specific to a currency and message type. This allows us to add additional functionality (new message types), without breaking existing (unchanged) message types.

Looking at the `MessageVersion`, `MessageType` and `ProtcolIdentifier` together, we can determine if we can handle that specific request or not.

## Rationale

We use RLP over more commonly used standards like JSON or XML because it's more space efficient.

We encode the resulting string with `base58` so it will be compatible with URLs.

### Adding an ID to the Request

It might make sense to include a random ID for all "Requests", and then send this ID back with the "Responses". This would allow the initiating application to provide additional context info for that specific request. However, we decided against that because it means that not the complete state is contained in the data that is sent. There are scenarios where the recipient of the Response is not the same application / device that initiated the request.

Example:

1. User uses web extension to prepare a transaction
2. User scans prepared transaction QR with mobile phone to sign
3. User broadcasts the transaction on the mobile phone directly

So in this example, the ID would be useless to the broadcasting app on the phone because the context would be missing.

## Implementation

There is currently one working implementation inside the [airgap-coin-lib](https://airgap-it.github.io/airgap-coin-lib/#/serialization), which uses this scheme to facilitate communication between online and offline devices.
