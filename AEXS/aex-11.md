# AEX 11 Fungible Token Standard

```
AEX: 11
Title: Fungible Token Standard
Author: Robert Zaremba (@robert-zaremba)
License: BSD-3-Clause
Discussions-To: <URL>
Status: Draft
Type: Interface
Created: 2019-09-19
```

## Abstract


A short (~200 word) description of the technical issue being addressed.


## Motivation


The following describes standard functions a token contract and contract working with specified token can implement to prevent accidentally sends of tokens to contracts and make token transactions behave like ether transactions.
Moreover, where designing the tokens we looked carfully at compliance work and wanted to make it easier for integrating compliance workflow. With this respect token transactions contains `data` field to be used freely to pass additional information (eg: reference) from a holder or an operator.

The goal is to make this specification useful with Native Tokens.


## Specification

### Interface

```
contract AEX11Token {
    record meta_info = { name : string
                         symbol : string
                         decimals : int}
    entrypoint meta(): meta_info
    entrypoint total_supply() external : int
    entrypoint balance_of(holder: address) : option(int)

    external default_operators() : list(address)
    external is_operator_for(operator: address, holder: address) : bool
    stateful external authorize_operator(operator: address)
    stateful external revoke_operator(operator: address)

    entrypoint stateful transfer(to: address, amount: int, data: string)
    entrypoint stateful op_transfer(from: address, to: address, amount: int, data: string, op_data: string)

    entrypoint stateful burn(amount: int, data: string)
    entrypoint stateful op_burn(from: address, amount: int, data: string, op_data: string)

    datetype event =
        // operator, from, to, amount ,data, op_data
        Transfer(indexed address,  // operator
                 indexed address,  // from
                 address,          // to
                 int,              // amount
                 string,           // data
                 string)           // op_data
      | Mint(indexed address,  // operator
             indexed address,  // to
             int,              // amount
             string,           // data
             string)           // op_data
      | Burned(indexed address,  // operator
               indexed address,  // from
               int,              // amount
               string,           // data
               string)           // op_data
      | AuthorizedOperator(indexed address,  // operator
                           indexed address)  // holder
      | RevokedOperator(indexed address,  // oprator
                        indexed address)  // holder
}
```

The token contract MUST implement the above interface. The implementation MUST follow the specifications described below.


### Operators

An operator is an address which is allowed to send and burn tokens on behalf of some holder.

NOTE: A holder MAY have multiple operators at the same time.

A default operator is an implicitly authorized operator for all holders. The rules below apply to default operators:

+ The token contract MUST define default operators at creation time. The default operators set MAY be empty.
+ The token contract MUST NOT add or remove default operators after contract instantiation.
+ `AuthorizedOperator` events MUST NOT be emitted when defining default operators.
+ A holder MUST be allowed to revoke a default operator (unless the holder is the default operator).
+ A holder MUST be allowed to re-authorize a previously revoked default operator.

The following rules apply to any operator:

+ An address MUST always be an operator for itself. Hence an address MUST NOT ever be revoked as its own operator.

NOTE: A holder MAY authorize an already authorized operator. An `AuthorizedOperator` MUST be emitted each time.

NOTE: A holder MAY revoke an already revoked operator. A `RevokedOperator` MUST be emitted each time.




### Functional Specification

#### General Notes

#### `meta() : meta_inf`

Returns information about token as a `meta_inf` record.

#### `total_supply() : int`

Returns: Total supply of tokens currently in circulation.

+ NOTE: MUST be equal to the sum of the balances of all addresses—as returned by the `balance_of` function.
+ NOTE: MUST be equal to the sum of all the minted tokens as defined in all the Minted events minus the sum of all the burned tokens as defined in all the Burned events.


#### `balance_of(holder: address) : option(int)`

Get the balance of the `holder` account. If the account is not registered (didn't use any token) then it MUST return `None`. Oherwise it MUST return non negative integer.


#### `default_operators() : list(address)`

List of addresses of all the default operators.

#### `is_operator_for(operator: address, holder: address) : bool`

MUST return true if the `operator` address is an operator for the `holder`. Otherwise MUST return false.

#### `authorize_operator(operator: address)`

Set a third party `operator` address as an operator of `Call.caller` to transfer and burn tokens on its behalf.

NOTE: The holder (`Call.caller`) is always an operator for itself. This right SHALL NOT be revoked.

#### `revoke_operator(operator: address)`

Remove the right of the `operator` address to be an operator for `Call.caller` and to send and burn tokens on its behalf.

NOTE: The holder (`Call.caller`) is always an operator for itself. This right SHALL NOT be revoked.

#### `transfer(to: address, amount: int, data: string)`

Send the `amount` of tokens from the `Call.caller` (holder) to the `to` address. `data` an information provided by the holder.

In the `Transfer` event, the operator and the holder MUST both be `Call.caller`.

#### `op_transfer(from: address, to: address, amount: int, data: string, op_data: string)`

Send the `amount` of tokens on behalf of the `from` address to the `to` address.

Reminder: If the operator address (`Call.caller`) is not an authorized operator of the `from` address, then the send process MUST `abort`.

#### `burn(amount: int, data: string)`

Burn the `amount` of tokens from the `Call.caller` address.

In the `Burned` event, the operator and the holder MUST both be `Call.caller`.


#### `op_burn(from: address, amount: int, data: string, op_data: string)`

Burn the `amount` of tokens on behalf of the `from` address.

Reminder: If the operator address (`Call.caller`) is not an authorized operator of the `from` address, then the burn process MUST `abort`.

#### `AuthorizedOperator` event

Indicates the authorization of `operator` as an __operator_ for `holder`.
The token contract MUST emit an `AuthorizedOperator` event with the correct values when a holder authorizes an address as its operator (even if it's already authorized or it's defined as a default oprator).

#### `RevokeOperator` event

Indicates the revokation of `operator` as an __operator_ for `holder`.
The token contract MUST emit an `RevokeOperator` event with the correct values when a holder revokes an address as its operator (even if it's already revoked or it's defined as a default oprator).


### Hooks

#### `tokensToTransfer` hook

The tokensToSend hook notifies of any request to decrement the balance (transfer and burn) for a given holder. Any address (regular or contract) wishing to be notified of token debits from their address MAY register for receiving this hooks

Interface:

```
stateful entrypoint tokensToTransfer(
    operator: address,
    from: address,
    to: address,
    amount: int,
    data: string,
    op_data: string
)
```

Notify a request to transfer or burn (if `to` is `0x0`) an amount tokens from the from address to the to address by the operator address. The _holder_ MAY block a send or burn process by aborting. (I.e., reject the withdrawal of tokens from its account.)

+ `to` MUST be `0x0` for a burn.
+ `amount` MUST be the number of tokens the holder sent or burned. MUST be non negative integer (0 is possible).
+ `data` MUST contain the extra information (if any) provided to the send or the burn process.
+ `op_data` MUST contain the extra information provided by the address which triggered the decrease of the balance (if any).

NOTE: A regular address MAY register a different address—the address of a contract—implementing the interface on its behalf. A contract MAY register either its address or the address of another contract but said address MUST implement the interface on its behalf.

*TODO*

#### `tokensReceived` hook

The tokensToSend hook notifies of any request to increment the balance (transfer and mint) for a given holder. Any address (regular or contract) wishing to be notified of token credits from their address MAY register for receiving this hooks

Interface:

```
function tokensReceived(
    operator: address,
    from: address,
    to: address,
    amount: int,
    data: string,
    op_data: string
)
```

Notify a send or mint (if `from` is `0x0`) of amount tokens from the from address to the to address by the operator address.


*TOOD*

## Backwards Compatibility

This token provides a foundation for compliance tokens. Because of the need for refence field (`data`), and introduction of operators (often required in the industry and interoperability with other smart contracts), the interface is not backward compatible with proposed AEX-9 simple token standard.

## Implementation

*TODO*


## Further consideration

+ register token interfaces
  Probably we don't need it because Sophia is strong static typed language.
