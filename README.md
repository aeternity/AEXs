# Aeternity Expansions

The Aeternity Expansions (AEX), or aexpansions, are standards proposed by the
community at large, i.e. everyone. Some of them can be mandatory in a specific
context, e.g. [AEX-1](https://github.com/aeternity/AEXs/blob/master/AEXS/aex-1.md) describes the set of rules governing this repository, but
are restricted to the application layer.

## Quick links

- All [AExpansions](https://github.com/u2467/AEXs/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3AAExpansion+)
- Recently [updated](https://github.com/u2467/AEXs/issues?q=is%3Aissue+label%3AAExpansion+sort%3Aupdated-desc)
- Active aexpansions 
- Drafted
- etc....

## Goals

The purpose of this repository is to provide a high quality and accessible set
of specifications; ideally together with implementations ready to be used if
applicable.


## Contributing

If you want to contribute please follow these steps:

1. Read [AEX-1](https://github.com/aeternity/AEXs/blob/master/AEXS/aex-1.md)
2. Fork this repository
3. Add your proposal to your fork, using the template provided
4. Submit a pull request to the AEX repository

Each proposal should go into the `AEXS` directory. If you need to embed images
or other assets add a subdirectory in the `assets` directory with the number
of your proposal once assigned, e.g. `assets/aex-1/image.png`.

Everything beyond step 4 is governed by [AEX-1](https://github.com/aeternity/AEXs/blob/master/AEXS/aex-1.md).

## Status terms

Read [AEX-1](https://github.com/aeternity/AEXs/blob/master/AEXS/aex-1.md) for more details.

* Draft - an AEX that is open for consideration and is undergoing rapid iteration and changes.
* Review - an AEX that is done with its initial iteration and in review by a wide audience.
* Last Call - In review by a wide audience - last call for accepting changes.
* Final - an AEX  that has been in Last Call for at least 2 weeks and all technical changes that were requested have been addressed by the author.
* Active - proposal can be in constant flux. No unaddressed substantiated objections are left, crucial or cosmetic updates only.
* Updated - signalling that an updating standard might have to be considered
* Superseded - an AEX that is deprecated because other AEX supersedes it.
* Rejected - editors deemed this proposal to be unworkable
* Withdrawn - signalling that the proposal is no longer relevant.

## AEXs

| Code | Status | Description |
| --- | --- | --- |
| [AEX-1](AEXS/aex-1.md)     | Active | AEX process |
| [AEX-2](AEXS/aex-2.md)     | Review | Third-party Wallet Provider Support |
| [AEX-3](AEXS/aex-3.md)     | Review | Secret storage format |
| [AEX-4](AEXS/aex-4.md)     | Draft  | æternity wallet deep linking specification |
| [AEX-5](AEXS/aex-5.md)     | Draft  | Inter-Wallet Communication |
| [AEX-7](AEXS/aex-7.md)     | Draft  | Data Serialization |
| [AEX-9](AEXS/aex-9.md)     | Draft  | Fungible Token Standard |
| [AEX-10](AEXS/aex-10.md)   | Draft  | Derivation path for deterministic wallets |


## Copyright

This README is released under the
[CC0-1.0](https://creativecommons.org/publicdomain/zero/1.0/) license.
