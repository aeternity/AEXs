# AEX 8

```
AEX: 8
Title: Message Signing
Author: Andreas Gassmann (@AndreasGassmann), Alessandro De Carli (@dcale)
License: BSD-3-Clause
Discussions-To: TBD
Status: Draft
Type: Standards Track
Created: 2019-05-13
```

## Simple Summary

This document defines a standard how arbitrary messages or payload can be signed using an aeternity keypair.

## Motivation

Message signing can be used in a wide variety of scenarios. Common use cases include:

- Authentication / Login (Proving you are the owner of an address/pubkey)
- Authorization (Giving a user priviliges based on the coins / tokens he owns)
- Prove Data Integrity
- Voting

This technique has been around for a long time, but with the rise in crypto adoption it becomes more accessible to the average user.

## Specification

### Flow

1. DApp / Website / Wallet prepares a signing request containing a message (challenge) and other parameters (see below)
2. Signing request is sent to the Wallet / Signer containing the Private Key
3. User can select an identity (= keypair)
4. Message will be signed
5. User is either being redirected to the provided callback URL, or the signed message is displayed (eg. QR code)

### Encoding

The signature should be encoded in `hex`: `Buffer.from(signature).toString("hex");`

### Message Signing Request

```typescript

const signingRequest = {
	message: string // Message to be signed
	publicKey?: string // OPTIONAL: allows wallet to pre-select signing identity
	ttl?: string // OPTIONAL: Blockheight or timestamp to prevent replay attacks
	origin?: string // OPTIONAL: eg. aeternity.com
	callbackURL?: string // OPTIONAL: eg. https://aeternity.com/?signedMessage=
}
```

### Message Signing Response

```typescript

const signingResponse = {
	message: string // Message to be signed
	signature: string[] // Signature of the message
	publicKey: string[] // allows wallet to pre-select signing identity
	ttl?: string // Blockheight or timestamp to prevent replay attacks
	origin?: string // OPTIONAL: eg. aeternity.com
}
```

The response can contain an array of signatures and publicKeys, which allows a single message to be signed by multiple identities at once.

### Signing the message

```typescript
// TODO: How do we fit TTL and hostname into the message?

import * as sodium from "libsodium-wrappers";

const signMessage = async (
  message: string,
  privateKey: Buffer
): Promise<string> => {
  await sodium.ready;
  const signature = sodium.crypto_sign_detached(
    sodium.from_string(message),
    privateKey
  );
  const hexSignature = Buffer.from(signature).toString("hex");

  return hexSignature;
};
```

### Verifying the signature

```typescript
import * as sodium from "libsodium-wrappers";

const verifyMessage = async (
  message: string,
  hexSignature: string,
  publicKey: Buffer
): Promise<boolean> => {
  await sodium.ready;
  const signature = new Uint8Array(Buffer.from(hexSignature, "hex"));
  const isValidSignature = sodium.crypto_sign_verify_detached(
    signature,
    message,
    publicKey
  );

  return isValidSignature;
};
```

### Example

```typescript
// risk monitor path sick coconut cube ecology brief table adapt evil oven

const privateKey = Buffer.from(
  "7f192bc4b5d6e828b6aeed3958f791f2d4d0f69e9b34a164df41f0f325c48ceb7d29631b2cc36eb4931a2ebe8b0a1bd95a29825ec21430b9e472146c851d2cfd",
  "hex"
);
const publicKey = Buffer.from(
  "7d29631b2cc36eb4931a2ebe8b0a1bd95a29825ec21430b9e472146c851d2cfd",
  "hex"
);
const message = "This message will be signed.";

const signature = await signMessage(message, publicKey);
// e650110d48b42cf07f577b886f852b36945da4b175cb1629528b705799d1565799802c7fbb7d07685f8b22185db7ce5bd03f7e0e754b904b80b4fe4fda4f1802

const isValidSignature = await verifyMessage(message, signature, publicKey);
// true
```

### Serialization and Transport Layer

Serialization and Transport Layer are not part of this propaosal.

The Serialization is being discussed as part of `AEX-7 Data Serialization`

### Security

In order to prevent malicious DApps from sending a seeminly random message to be signed that actually contains data like a valid transaction, we should add a prefix to the message so this can't be exploited.

https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_sign
