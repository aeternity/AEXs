# AEX-4

```
AEX: 4
Title: æternity wallet deep linking specification
Author: Shubhendu Shekhar (@shekhar-shubhendu), Andrea Giacobino (@noandrea)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-4-aeternity-wallet-deep-linking-specification/3231
Status: Draft
Type: Standards Track
Created: 2019-04-03
```

## Simple Summary

The document describes and defines the deep linking specification that every æternity supported wallet can implement and follow to redirect users to a specific activity or page inside the application.

## Motivation

URI based deep linking enables websites or applications to interact with native applications registered to listen for aeternity URI scheme and trigger wallet actions like open wallet accounts directly from third-party applications.

This also enables users to sign transaction using desktop or mobile based wallets without the need to copy or remember long addresses.
This is especially beneficial for mobile wallet users as they can simply click on the wallet deep link to open the wallet app from the vendor app, confirm the transaction and return back to the vendor app through the callback url.

## Specification

### URI Scheme

- `ae:`
- `aeternity:`

### Methods

1. View/Open Account in the wallet

    URI: `aeternity:<wallet_address>`

    Associated wallet app will:

    1. Open the wallet app
    2. Check if the address exists or not
    3. If the account is found with the wallet, then the user is redirected to the account information page of the provided address.
    4. If the account is not found then prompt the user with an `account not found` message.

    #### Example

    `aeternity:ak_2i2fioFMoEffBPeT3EBZEsxK1w579BuCgYE8WiMiADEQqUguU2`

2. Payment

    URI: `aeternity:<receiver_address>/<amount>/<vendor_identifier>?callback=<url>`

    This URI scheme enables vendors to generate direct payment request URIs to user wallet.

    1. Vendor generates a address for receiving payment.
    2. Creates the URI substituting `receiver_address` with newly created address, `amount` with amount requested, `vendor_identifier` with a human readable identifier that can be used to correctly identify vendor and requested payment, and `url` with a callback URL that accepts a transaction id under `txId` query param.

    #### Example

    `aeternity:ak_2gUJrd11cy65yqZ7mg1ULUG4kZ5r6v6vNqVtmA8HqUGKCf6kNf/125.12/myaeshop-ref9834?callback=https://myaeshop.com/verify`

3. Sign and Broadcast

    URI: `aeternity:<transaction>/<network_id>/<label>?return=<txId/tx>&callback=<url>`

    This URI scheme enables users to sign(and broadcast) the transaction using deep lining enabled user wallet.

    1. The user generates the transaction to be signed
    2. Creates the URI substituting `transaction` and `network_id` with their respective value, `label` a human-readable text regarding the transaction and provides the query param `return` which indicates the return value type in the callback URL. It can contain only two possible values of string type:
        - `txId`: which indicates that the wallet needs to broadcast the transaction and return the transaction id
        - `tx`: means that the callback URL expects a signed transaction back.
    3. And at last, substitutes the query param `url` which contains a callback URL that accepts a transaction id under `txId` query param and signed transaction output under `tx` query param.

    #### Example

    `aeternity:tx_+E0MAaEBK4bq9XiZ/0QVdOa8Hs9V18v6dGZYIa8XXNYFpQh6yq6hAR8To7CL8AFABmKmi2nYdfeAPOxMCGR/btXYTHiXvVCjCoJOIAADgFcJyZ8=/ae_uat/sample_tx?return=tx&callback=https://myaeshop.com/verify`

## Reference

- https://github.com/bitcoin/bips/blob/master/bip-0021.mediawiki
- https://github.com/bitcoin/bips/blob/master/bip-0070.mediawiki
- https://github.com/bitcoin/bips/blob/master/bip-0072.mediawiki
- https://hackmd.io/s/BJObJntjQ
