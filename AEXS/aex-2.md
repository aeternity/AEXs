# AEX 2

```
AEX: 2
Title: Third-party Wallet Provider Support
Author: Shubhendu Shekhar (@shekhar-shubhendu), Andrea Giacobino (@noandrea) & Nazar Duchak (@nduchak)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-2-js-sdk-interfaces-for-wallets/2715
License-Code: Apache-2.0
Status: Draft
Type: Standards Track
Created: 2019-03-04
```

## Simple Summary

The document approaches the technical specification about how a wallet provider (ex. BaseApp, Metamask) can interact with Aeternity enabled applications. 

[Link to discussion topic](https://forum.aeternity.com/t/aex-2-js-sdk-interfaces-for-wallets/2715).

## Abstract

The specification address interfaces that the Javascript SDK exposes for a third party wallet provider can implement to provide functionalities like signing. The specification is meant to have a broad coverage for different scenarios like: browser extensions, iframes, native applications, deep linking, etc..

## Motivation

The motivation for the AEX is to provide to the community a clear, straightforward and safe way to interact with æternity enabled applications that uses the Javascript SDK.

In this direction the goals that have been set for the specification are:

- generic: the proposal must cover as many as possible common uses cases
- secure: it should provide a safe model for communication between wallets and application
- simple: it should be easy to implement without much configuration or specific knowledge
- vendor free: it should not force the adoption of third party tools
- future proof: it should be extensible to support additional features

## Specification

The specification is divided in two main areas, the first one addresses applications running inside a browser or alike (ex. Electron) and the second one addresses deep linking.

### In Browser Applications

For in browser application the communication between `wallet <-> (sdk <-> app)` leverages the [Native messaging API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Native_messaging).

The advantages of using the _Native messaging API_ lie in the possibility of components to communicate independently from where they are being instantiated: a embedded iFrame can communicate with the parent page as well as a browser extension or a custom browser implementation.

Since the messages via _Native messaging API_ are broadcasted, the communication between the SDK and a Wallet provider are encrypted using the [nacl secrectbox](https://nacl.cr.yp.to/secretbox.html) implementation with throwaway keys.

#### Workflow

The workflow of the interaction between the App + SDK is divided in 3 phases:

- handshake
- operation
- de-registration

An example for the handshake is the following

```sequence
Note left of SDK: App
SDK --> Wallet: ae:sdkReady [optional]
Wallet ->SDK: ae:registerProvider(WalletPubKey)
SDK -> Wallet: ae:registrationComplete(SDKPubKey, WalletPubKey)
```

In the operation phase the SDK and the Wallet will be exchangin encrypted data
relative to signing end broadcasting transactions

The deregistration phase is optional but allows a wallet provider to
notifiy the SDK (and the app) that the wallet has been disconnected.

#### SDK Provided Methods

##### The window object listens for the following events:

- `ae:sdkReady` (_optional_): Invoked by `sdk` to announce the fact that it has been loaded.
- `ae:registerProvider`: Invoked by `wallet` to register with `sdk`.
- `ae:registrationComplete`: Invoked by `sdk` to let wallet know that the registration is done.
- `ae:walletDetail`: Invoked by `wallet`. this contains encrypted information about the list of addresses and extra wallet methods.
- `ae:sign`: Invoked by `sdk` when required to sign the tx.
- `ae:broadcast`: Invoked by `wallet` after it has signed the tx.
- `ae:deregister`: Invoked by `wallet` to deregister itself from the `sdk`.

#### Wallet Provided Methods

The wallet can also let the SDK know of extra funtionality that it provides during the time of registration. These methods will vary from wallet to wallet and are out of the scope of this proposal.

#### Workflow

##### Provider Registration

1. The wallet provider invokes the registration request, passing its public key.

   Message:

   ```json
   {
     "registerProvider": "<DH public key>"
   }
   ```

2. The SDK listens for `registerProvider` and on receiving a request generates a pub-priv key pair and broadcasts a `registrationComplete` message that includes `sdk` public key that should be used by wallet provider for all the future communications.
   Message:
   ```json
   {
     "registrationComplete": {
       "provider": "<DH public key>",
       "sdk": "<DH public key>"
     }
   }
   ```
3. At this step, the user should be prompted by the extension/wallet to accept the incoming SDK. If the user agrees, the wallet posts the `walletDetail` message encrypted using the secret key generated (wallet private key + sdk public key) and nonce.

   Message:

   ```json
   {
       "walletDetail": {
           "message": <encrypted payload>,
           "nonce": "<nonce>"
       }
   }
   ```

   Plaintext for encrypted payload:

   ```json
   {
     "address": [],
     "extra": []
   }
   ```

##### Transaction Signing

1. `SDK` posts the message containing `raw and unsigned transaction` encrypted with secret key generated using wallet public key and SDK private key(corresponding to the wallet provider).

   Message:

   ```json
   {
     "sign": {
        "message": <encrypted payload>,
        "nonce": "<nonce>"
     }
   }
   ```

   Plaintext:

   ```json
   {
     "transaction": <unsigned tx>,
     "network_id": "<network_id>",
   }
   ```

2. When the wallet receives this message it tries to decrypt it, and if successful validates and signs the tx, re-encrypts it and again posts it for `SDK` to receive.

   Message:

   ```json
   {
       "broadcast": {
           "message": <encrypted signed transaction>,
           "nonce": "<nonce>"
       }
   }
   ```

#### Wallet Deep Linking Specification

##### URI Scheme: `ae://` or `aeternity://`

URI based deep linking enables websites or applications to interact with native applications registered to listen for aeternity URI scheme.
This enables a users to sign transaction using desktop or app based wallet.

When the URI is invoked, if there is a default application selected by user for the URI scheme then it opens and application and then wallet can process the URL to open the appropriate page or trigger required action.

- Open Wallet Application
  - `aeternity://<address>`
- Sign Transaction
  - `aeternity://<address>/<raw_tx>?network_id=<network_id>&callback=<sdk_url>`
- Callback to SDK

  - Every `sign` request should specify `sdk callback` url. Once the signing is done by the wallet, it needs to invode the callback url with the signex transaction payload for the `SDK` to broadcast it.
  - The wallet should do a `POST` request on the `callback` url with following payload:

  ```json
  {
      "transaction": <signed_transaction>,
      "network_id": <network_id>
  }
  ```

## Rationale

### Encrypted Communication

- Encrypted communication layer protects the user privacy by not leaking the list of accounts to any malicious actor eavesdropping on the communication between `wallet` and `SDK`. The list of accounts is only provided to the `SDK`(in encrypted format) only if the user accepts the incoming `registrationComplete` request.
- Encryption communication also prevents any malicious actor to inject itself between the `SDK` and `wallet` and modify the requests and responses originating from either party. In other words, it prevents MITM attacks against wallets.

### Deep Linking

- Deep linking enables the users(& `SDK`) to interact with the `wallets` that don't support `postMessage` API i.e. native desktop wallets with no browser extension, mobile wallet applications, etc.

## References

- https://www.owasp.org/images/9/97/OWASPLondon_PostMessage_Security_in_Chrome_Extensions.pdf
- https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Native_messaging
- https://labs.detectify.com/2016/12/08/the-pitfalls-of-postmessage/
- https://developer.chrome.com/extensions/content_scripts#host-page-communication

### Extra reading

- https://medium.com/metamask/https-medium-com-metamask-breaking-change-injecting-web3-7722797916a8
- https://medium.com/@lyricalpolymath/web3designdecisionframework-e84075816515
- https://www.youtube.com/watch?v=aVE0eHp7QA0&feature=youtu.be
- https://docs.satoshipay.io/api/#introduction
- https://medium.com/@abhimuralidharan/universal-links-in-ios-79c4ee038272
- https://developer.android.com/training/app-links
- https://developer.apple.com/ios/universal-links/
