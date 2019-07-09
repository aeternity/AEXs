# AEX Z: æpps Metadata Format Specification

```
AEX: <to be assigned by editors>
Title: æpps Meta Information Format
Author: Stoyan Vasilev (@j28), Denis Davidyuk (@davidyuk)
License: BSD-3-Clause
Discussions-To: <URL>
Status: Draft
Type: Meta
Created: 2019-07-09
```


## Simple Summary

The goal of this document is to specify a format for metadata which æpps provide to æpp listing services, decentralized æpp stores, æpp browsers, and wallets.


## Abstract

A format which is consistenly used by developers is needed to facilitate the uniform and useful user experience of browsing aepps inside æpps browsers, wallets, and listing services.


## Motivation

The motivation behind this aexpansion is to improve the user experience of browsing æpps inside æpps browsers, wallets, and listing services. Our goal is to make it easy for users to recognize the purpose of an æpp/what the æpp allows its users to do and whether users would like to open the æpp and give it access to the account data it requests.


## Specification

### Format

**Proposed fields:**
+ Name of aepp
+ Category (select from predermined list)
+ aepp description
+ aepp icon
+ aepp banner image (dimensions and format TBD)
+ Networks the aepp is available on [mainnet, testnet]
+ Link to aepp (which would contain the metadata specified in this proposal)
+ Age restrictions/rating?

### References


Reference visuals for the result of implementing such metadata format are included below.

A service listing dapps:

![](https://i.imgur.com/KZE2nT7.jpg =240x)

Detail view of a dapp metadata rendered inside a dapps browser (Coinbase Wallet):

![](https://i.imgur.com/31evUOm.png =240x)

## Rationale

### 2 Potential Formats

**We propose one of two potential formats (feedback is requested):**
+ a modified version of webmanifest format
+ a new "aeppmanifest" format

**The pros of using webmanifest include:**
+ developers are already familiar with the format
+ many aepps/dapps/apps likely already have a webmanifest file

**The cons of using webmanifest include:**
+ it could be confusing which fields are required/recommended for aeternity aepps

**The pros of specifying an aeppmanifest include:**
+ the aepp manifest would only include data pertaining to aepps, which would in turn make it more clear what and how an aepp should specify

**The cons of specifying an aeppmanifest include:**
+ it could be less convenient for aepp developers to have to maintain multiple manifest files, which include overlapping information.

### Other considerations
+ Consider if aeppsmanifest format applies to both web and non-web contexts.

+ This proposal does not address whether metadata provided by an aepp is accurate/truthful.

### Future Considerations

+ This proposal does not articulate a stance on storage concerns (as aepp metadata can be rendered dynamically or stored locally).
+ This proposal does not address the concern of aepps sharing aepps data between themselves.


## Backwards Compatibility


## Implementation

A reference implementation can be provided by the Base æpp team.

## Copyright

