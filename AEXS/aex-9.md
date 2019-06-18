# AEX 9

```
AEX: 9
Title: Fungible Token Standard
Author: Milen Radkov (@mradkov), Philipp Piwowarsky (@thepiwo)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-9-fungible-token/3565
Status: Draft
Type: Standards Track
Created: 2019-05-11
```

## Simple Summary

This document aims to outline a standard and define how fungible tokens should be created and used on aeternity blockchain.

## Abstract

The following standard allows for the implementation of a standard API for tokens within smart contracts. This standard provides basic functionality to transfer tokens, as well as allow tokens to be approved so they can be spent by another on-chain third party.


## Motivation

This standard will allow decentralized applications and wallets to handle tokens across multiple interfaces.

The most important here are `transfer`, `balance` and the `Transfer` event.

A standard interface allows any tokens to be re-used by other applications: from wallets to decentralized exchanges.

- The following specification is following the ERC20 standart introduced in Ethereum for fungible tokens.
- This standart is proven to be working and we think we can leverage of using it. It will also help us with interoperability and easier developers onboarding as the main differences will be Sophia syntax related.

## Specification

## Basic Token
### Methods

NOTES:

`meta_info` - Returns the meta_information record from the token in the required format `{ name : string, symbol : string, decimals : int }`.

```
public function meta_info() : meta_info
```

`total_supply` - Returns the total token supply.

```
public function total_supply() : int
```

`balances` - Returns the full balance state for static calls, e.g. by a blockchain explorer.

```
public function balances() : map(address, int)
```


`balance` - Returns the account balance of another account with address `owner`, if the account exists. If the owner address is unknown to the contract `None` will be returned, to be able to determine if the account has balance of 0 or is still unknown.

```
public function balance(owner: address) : option(int)
```

`transfer` - Transfers `value` amount of tokens to `to_account` address, and MUST fire the `Transfer` event. The function SHOULD abort if the message caller’s account balance does not have enough tokens to spend.

Note Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.

```
public stateful function transfer(to_account: address, value: int)
```


### Events

**Transfer** - MUST trigger when tokens are transferred, including zero value transfers.

The transfer event arguments should be as follows: `(from_account, to_account, value)`

```
Transfer(indexed address, indexed address, indexed int)
```


## Extension Mintable

`mint` - Mints `value` new tokens to `account`. The function SHOULD abort if `Call.caller` is not `state.owner`.

```
public stateful function mint(account: address, value: int)
```

### Events

**Mint** - MUST trigger when tokens are minted using the `mint` function.

The mint event arguments should be as follows: `(account,  value)`

```
Mint(indexed address, indexed int)
```

## Extension Allowance

`create_allowance`
Allows `for_account` to withdraw from your account multiple times, up to the `value` amount. If this function is called again it overwrites the current allowance with `value`.

NOTE: To prevent attack vectors (like the ones possible in ERC20) clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn’t enforce it, to allow backwards compatibility with contracts deployed before

```
public stateful function create_allowance(for_account: address, value: int)
```

`transfer_allowance`
Transfers `value` amount of tokens from address `from_account` to address `to_account`, and MUST fire the Transfer event.

The `transfer_allowance` method is used for a withdraw workflow, allowing contracts to transfer tokens on your behalf. This can be used for example to allow a contract to transfer tokens on your behalf and/or to charge fees in sub-currencies. The function SHOULD abort unless the `from_account` account has deliberately authorized the sender of the message via some mechanism.

Note Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.

```
public stateful function transfer_allowance(from_account: address, to_account: address, value: int)
```

`allowance`
Returns the amount which `for_account` is still allowed to withdraw from `from_account`, where `record allowance_accounts = { from_account : address, for_account : address }`. If no allowance for this combination of accounts exists, `None` is returned.

```
public function allowance(allowance_accounts : allowance_accounts) : option(int)
```

### Events

**Approval** - MUST trigger on any successful call to `create_allowance(for_account: address, value: int)`.

The approval event arguments should be as follows: `(from_account, for_account, value)`


```
Allowance(indexed address, indexed address, indexed int)
```

## Extension Allowance with Callback

*TODO*

## Extension Swappable

*TODO*

## Implementation
There are several implementations available at the moment, but they lack a thing or two (that is why this standard is being proposed).

Example implementations:
- [(REFERENCE) Aeternity Sophia Fungible Token](https://github.com/mradkov/aeternity-fungible-token/blob/master/contracts/fungible-token.aes)
- [(OUTDATED) Fungible token implementation example](https://github.com/aeternity/aepp-sophia-examples/blob/master/libraries/FungibleToken/contracts/fungible-token.aes)
- [(OUTDATED) ERC20 Sophia Translation](https://github.com/aeternity/aesophia/blob/master/test/contracts/erc20_token.aes)

## References
[ERC-20](https://eips.ethereum.org/EIPS/eip-20)
[ERC-20 attack vectors](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)


