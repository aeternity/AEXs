# AEX-4

```
AEX: 4
Title: æternity wallet deep linking specification
Author: Shubhendu Shekhar (@shekhar-shubhendu), Andrea Giacobino (@noandrea)
License: BSD-3-Clause
Status: Draft
Type: Standards Track
Created: 2019-04-03
```

## Simple Summary

The document describes and defines the deep linking specification that every æternity supported wallet can implement and follow to redirect users to a specific activity or page inside the application.

## Motivation

URI based deep linking enables websites or applications to interact with native applications registered to listen for aeternity URI scheme and trigger wallet actions like open wallet accounts directly from third-party applications.

This also enables users to sign transaction using desktop or app based wallets without the need to copy or remember long addresses.
This is especially beneficial for mobile wallet users as they can simply click on the wallet deeplink to open the wallet app from the vendor app, confirm the transaction and return back to the vendor app through the callback url.

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
    3. If the acoount is found with the wallet, then the user is redirected to the account information page of the provided address.
    4.If the account is not found then prompt the user with an `account not found` message.

    #### Example

    `aeternity:ak_2i2fioFMoEffBPeT3EBZEsxK1w579BuCgYE8WiMiADEQqUguU2`

2. Payment

    URI: `aeternity:<receiver_address>/<amount>/<vendor_name>?callback=<url>&txId=<transaction_id>`

    This URI scheme enables vendors to generate direct payment request URIs to user wallet.

    1. Vendor generates a address for receiving payment.
    2. Creates the URI substituting `receiver_address` with newly created address, `amount` with amount requested, `vendor_name` with unique self-identifier for the user to correctly identifiy vendor and `url` with a callback URL that accepts a transaction id under `txId` query param.

    #### Example

    `aeternity:ak_2gUJrd11cy65yqZ7mg1ULUG4kZ5r6v6vNqVtmA8HqUGKCf6kNf/125.12/myaeshop?callback=https://myaeshop.com/verify?txId=th_rox6XyzqDBTopoTYQNJux49jnLVd39QhxDSvYoQ5he9SAGupz`

## Reference

- https://github.com/bitcoin/bips/blob/master/bip-0021.mediawiki
- https://github.com/bitcoin/bips/blob/master/bip-0070.mediawiki
- https://github.com/bitcoin/bips/blob/master/bip-0072.mediawiki
- https://hackmd.io/s/BJObJntjQ
