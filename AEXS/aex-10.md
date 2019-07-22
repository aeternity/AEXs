```
AEX: 10
Title: Derivation path for deterministic wallets
Author: Andrea Giacobino (@noandrea), Denis Davidyuk (@davidyuk)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-10-derivation-path-for-deterministic-wallets/3586
License-Code: ISC
Status: Final
Type: Informational
Created: 2019-03-04
```

## Simple Summary

This aexpansion specify the derivation path used in the Aeternity blockchain for [deterministic wallets](https://en.bitcoin.it/wiki/Deterministic_wallet)

## Motivation

The aexpansion is meant to avoid incompatibility with deterministic wallet implementations across the Aeternity landscape and to make it easy for the derivation path to be found.

## Specification

Accounts derivation path is taken from [BIP-0044](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki), except that in all path segments hardened derivation is used since [SLIP-0010](https://github.com/satoshilabs/slips/blob/master/slip-0010.md) does not support public derivation.

The derivation path (`m/purpose'/coin_type'/account_index'/change'/address_index'`) is therefore:

m/44<sub>H</sub>/457<sub>H</sub>/\${account_index}<sub>H</sub>/0<sub>H</sub>/\${address_index}<sub>H</sub>

where `account_index` and `address_index`are integer starting from `0`.

The coin type value for Aeternity, `457`, has been generated on random.org and is registered in [SLIP-0044](https://github.com/satoshilabs/slips/blob/master/slip-0044.md).

## References

- [Deterministic wallets](https://en.bitcoin.it/wiki/Deterministic_wallet)
- [`BIP-0032`: Hierarchical Deterministic Wallets](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)
- [`BIP-0044`: Multi-Account Hierarchy for Deterministic Wallets](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki)
- [`SLIP-0010`: Universal private key derivation from master private key](https://github.com/satoshilabs/slips/blob/master/slip-0010.md)
- [`SLIP-0044`: Registered coin types for BIP-0044](https://github.com/satoshilabs/slips/blob/master/slip-0044.md)
- [`BIP32-Ed25519`: Hierarchical Deterministic Keys over a Non-linear Keyspace](https://ieeexplore.ieee.org/document/7966967?reload=true)
