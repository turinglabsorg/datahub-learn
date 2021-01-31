---
description: STAKE Fungible Token NEP-141 Rust Implementation
---

# You Can Have Your STAKE and Trade It Too

## Show Me the Money: You Can Have Your STAKE and Trade It Too
Plain old vanilla staking is a great way to earn more NEAR, but their are some trade-offs to be aware of. One of the main 
trade-offs is that the staked NEAR is locked because it is effectively put up as collateral to help secure the NEAR network. 
We can do better than that by unlocking the value. The [STAKE][1] contract unlocks the value stored in the staked NEAR by 
transforming it into a [fungible token][3]. You can now have your STAKE and trade it too.

In this tutorial we'll learn about how staking works on NEAR. We'll see how the STAKE token adds and unlocks value. We'll
apply what we learned from the [fungible token][3] tutorial and implement the fungible token core NEP-141 standard in my
favorite programming language, Rust. We will take it step by step and divide it up into 3 phases - design, coding, and last 
but most important is testing. Finally, we'll deploy to testnet and take it for a test drive. There's a lot to cover, 
so let's get started ...

## NEAR Staking 101

![](../../../../.gitbook/assets/oysterpack-near-stake-token-basic-staking.png)

The NEAR protocol is a Proof-of-Stake (PoS) blockchain. Those who participate in staking to help secure the blockchain 
network earn staking rewards paid in NEAR tokens. There are two ways to participate:

#### As a **Validator**
Validators are responsible for running and operating the validator nodes that are actively producing blocks and must
come to a stake-weighted consensus about the valid state of the chain. On NEAR, the number of validator nodes per shard
is limited (currently 100 per shard). Anyone can submit a proposal to become a validator, but validator seats are auctioned
off to the highest bidders, i.e., those that who stake the most NEAR.
#### As a **Delegator**
Anyone can earn staking rewards by delegating their NEAR tokens to a validator through a [staking pool][2] contract.
Here's how it works:
1. Each deployed staking pool contract instance is owned by a single validator. 
2. Users deposit NEAR into the staking pool contract. Recall that validator seats are auctioned off to the highest bidder.
  Delegators pool their NEAR with validators to help them win auction bids for validator seats. Staking rewards earned
  by the validator are shared with the delegator minus validator fees. 
3. While delegated NEAR is deposited in the staking pool, it is effectively locked. While being staked, the delegated 
  NEAR is owned by the staking pool contract (which is owned by the validator). Effectively, delegators are lending
  their NEAR to validators through the staking pool contract. Delegators return on investment is their share of staking
  rewards - assuming the validator acquires a seat and does his job. 
4. When delegators choose to withdraw their NEAR they must first unstake the NEAR. The unstaked NEAR will remain locked 
  within the staking pool for 4 epoch periods (2 days) before being eligible for withdrawal from the staking pool contract.
   - **ATTENTION**: One thing users need to be aware and careful about is how the unstaking lock period functions in 
     the current version staking pool contract implementation. Each time the user submits a request to unstake NEAR it
     resets the lock period to 4 epochs (2 days). For example, if a user unstaked 1000 NEAR in epoch (1), then the 1000 NEAR
     will be available for withdrawal in epoch (4). What happens if the user submits another request to unstake 1 NEAR
     on epoch (3)? When you try to withdraw the 1000 NEAR in epoch (4), it is still locked because the lock period has
     been reset and extended for all unstaked NEAR. The 1001 NEAR will no be available for withdrawal in epoch (7).

For more information on how staking works on NEAR see:
- [Economics in a Sharded Blockchain - Validators section](https://near.org/papers/economics-in-sharded-blockchain/#validators)
- [Is NEAR a delegated proof of stake network](https://docs.near.org/docs/faq/economics_faq#is-near-a-delegated-proof-of-stake-network)
- [Staking Orientation](https://docs.near.org/docs/validator/staking-overview)

# You Can Have Your STAKE and Trade It Too
![](../../../../.gitbook/assets/oysterpack-near-stake-token-STAKE-FT.png)

When you deposit NEAR into the STAKE token contract, it will delegate the NEAR to the staking pool for you. In return you 
are issued STAKE tokens that grow in value via staking rewards issued through the staking pool contract. STAKE tokens unlock 
the value of the staked NEAR that is locked up by the staking pool contract. The STAKE token contract adds even more value
beyond letting you use staked NEAR as fungible tokens. However, we'll save that for future tutorials. For now, we'll
stay focused on how the STAKE token implements [NEP-141][3].

## Why Rust for Smart Contracts
Rust's website sums it up in 3 words: performance, reliability, and productivity. For me it's a no-brainer. Over the past
20+ years, I have used many programming languages, and [Rust][5] is the clear winner for me. For five years running, Rust has 
also been voted by developers as the[most loved language][4] on stackoverflow for good reason. It is becoming the preferred 
language of choice for the crypto space because it is a perfect fit to build highly performant, reliable, and secure 
blockchain software. On NEAR, for any serious or more complex smart contracts, especially within the DeFi space, I 
strongly encourage and recommend Rust to you. 

For developing NEAR smart contracts, you will need to learn to use the [NEAR RUST SDK][7]. I personally link to the latest
and greatest version which is tagged on github because:
- it provides a few features that are not yet released via crates.io to reduce boilerplate
  - `#[private]` macro for callbacks
  - `PanicOnDefault` used to derive `Default` implementation that panics. This is a helpful macro in case the contract is 
     required to be initialized with either `#[init]` or `#[init_once]`.
  - better unit testing support
- for simulation testing support

**Cargo.toml**
```toml
[dependencies]
near-sdk = { git = "https://github.com/near/near-sdk-rs",  tag = "2.4.0" }

[dev-dependencies]
near-sdk-sim = { git = "https://github.com/near/near-sdk-rs",  tag = "2.4.0" }
```

## Show Me the Design
To keep the code clean we will first design the interfaces separate from the implementation. The contract API will be defined
explicitly via an interface. In Rust, interfaces are called [traits][6]. The design approach will be to leverage Rust strongly
typed system to model the domain. The [NEP-141][3] standard defined the API using the lowest common denominator to keep the
API programming language neutral as musch as possible. In doing so, we lost type safety. For example, numeric amounts were
specified as `string` types. In Rust, we will instead be working with a typed domain model that makes the code clear and precise.

![](../../../../.gitbook/assets/oysterpack-near-stake-token-FT-NEP-141.png)
 
- see [Rust code][8] on github

**StakeTokeContract** represents the contract implementation that implements the **FungibaleToken** and **ResolveTransferCall**
traits. It depends on the **TransferReceiver** interface for cross-contract calls. 

**FungibleToken** trait
- specifies the core fungible token API
- instead of using native String types, I use specific domain type wrappers for `TokenAmount`, `Memo`, and `TransferCallMessage`
  - this leaves absolutely zero ambiguity in the code and enables the domain model to be encoded into the type system. You
    may think this is overkill for something so simple, but my advice is to never take shortcuts. If you are going to do
    something, then do it right in the first place. In addition, the beauty of Rust's zero cost abstractions, is that we 
    can leverage the type system for zero runtime costs (if done right).
    
**ResolveTransferCall** trait
- specifies the private callback interface used as part of the transfer call workflow
- private means that even though the function is exposed on the contract, only the contract itself is allowed to call the
  function. If any other account tries to call the private function, then it should fail.
  
**TransferReceiver** trait
- represents the contract API required by the transfer receiver contract

## Show Me the Code
You can refer to the full source code on github. I will be reviewing the most important parts of the code below.

We start by implementing the core fungible token interface on the contract:
```rust
#[near_bindgen]
impl FungibleToken for StakeTokenContract {
  // TODO
}
```
- [near_bindgen][9] generates the smart contract compatible with the NEAR blockchain at the WASM level
- the above reads as `StakeTokenContract` implements the `FungibleToken`  interface

Now we'll walk through each of the core contract API functions. For those of you who are Rust gurus or seasoned developers, 
please bear with me ... I will be pointing out Rust idioms and coding best practices that may seem obvious. Because I am 
showing actual Rust code for the first time, I will describe these Rust idioms and coding practices in more detail so 
that you can become familiar with my coding style. In future tutorials, I will assume this knowledge.

```rust
#[payable]
fn ft_transfer(
    &mut self,
    receiver_id: ValidAccountId,
    amount: TokenAmount,
    _memo: Option<Memo>,
) {
    assert_yocto_near_attached();
    assert_token_amount_not_zero(&amount);

    let stake_amount: YoctoStake = amount.value().into();

    let mut sender = self.predecessor_registered_account();
    self.claim_receipt_funds(&mut sender);
    sender.apply_stake_debit(stake_amount);
    // apply the 1 yoctoNEAR that was attached to the sender account's NEAR balance
    sender.apply_near_credit(1.into());

    let mut receiver = self.registered_account(receiver_id.as_ref());
    receiver.apply_stake_credit(stake_amount);

    self.save_registered_account(&sender);
    self.save_registered_account(&receiver);
}
```
- `#[payable]` marks the function to allow callers to attach NEAR to the function call. Recall that according to the 
   specification, callers must attach exactly 1 yoctoNEAR to the function call as a security measure
- `&mut self` tells the rust compiler that the function will modify contract state
- [ValidAccountId][10] comes from NEAR rust SDK. It provides boilerplate code to validate the NEAR account ID. If the account
  ID is not a valid NEAR account ID, then the function call will fail fast
- `_memo` - the STAKE contract has no use for memo, and thus tells the rust compiler that it will not be used by using a naming
  convention, i.e., by prefixing the name with an `_`. The rust compiler is very strict and disciplined. By default, it 
  will emit warnings for any sign of something possibly wrong with the code. 
- the first the code does is perform some checks:
  - it checks to make sure exactly 1 yoctoNEAR is attached
  - it checks that the transfer amount is not zero
  - by this point in the code the `receiver_id` has already been validated by NEAR SDK
- I like to keep the contract function code as clean and readable as possible. The goal is to be able to read the code 
  and easily understand the business logic. Implementation details or boilerplate should be separated out into other functions.
  If come back to code in 6 months and can't understand it or is hard to follow, then it's time to refactor and clean it up. 
- converting the token amount to `YoctoStake` is specific to the STAKE token business logic. The code is expecting the 
  transfer amount to be specified in yocto scale - yoctoSTAKE is the smallest unit for the STAKE token, just like yoctoNEAR
  is the smallest unit for NEAR. That's a bit off topic ... we'll revist this in future tutorials
- NEP-141 requires that the accounts involved in the transfer must both be registered. The `predecessor_registered_account()`
  and `registered_account()` helper functions will lookup the accounts and panic if the account is not registeredd
- `claim_receipt_funds()` is specific to STAKE business logic and we'll skip this for now
- `sender.apply_near_credit(1.into())` - remember the sender was required to attach 1 yoctoNEAR to the function call. How the
  the attached deposit is handled is not defined in the standard. In the STAKE contract, every registered account has a NEAR
  balance. Thus, the contract will credit the yoctoNEAR to the sender account (because 1 yoctoNEAR is not zero).
- the transfer amount is debited from the sender and then credited to the receiver
- in order to commit the transfer to the blockchain, we must remember to persist the state change to storage. Both the sender
  and receiver accounts are saved to storage.
  
> #### Best Practices
> 1. you should always use [ValidAccountId][10] as the contract function argument type
> 2. contract API function should be easy to read to understand the business logic
> 3. remember to commit contract state changes to storage

```rust
 #[payable]
fn ft_transfer_call(
    &mut self,
    receiver_id: ValidAccountId,
    amount: TokenAmount,
    msg: TransferCallMessage,
    _memo: Option<Memo>,
) -> Promise {
    self.ft_transfer(receiver_id.clone(), amount.clone(), _memo);

    ext_transfer_receiver::ft_on_transfer(
        env::predecessor_account_id(),
        amount.clone(),
        msg,
        receiver_id.as_ref(),
        NO_DEPOSIT.value(),
        self.ft_on_transfer_gas(),
    )
    .then(ext_resolve_transfer_call::ft_resolve_transfer_call(
        env::predecessor_account_id(),
        receiver_id.as_ref().to_string(),
        amount,
        &env::current_account_id(),
        NO_DEPOSIT.value(),
        self.resolve_transfer_gas(),
    ))
}
```
- transfer call workflow will first transfer the tokens - it simply delegates to `ft_transfer()`
- then it invokes the receiver contract and registers a callback to itself to finalize the transfer
- the code uses what's called the high level cross contract pattern provided by NEAR Rust SDK. It works as follows:

The remote function calls are declared as rust traits and annotated with the `#[ext_contract]` attribute. This attribute
will be used to generate the more low level code to invoke the remote call on the external contract. For each external
contract interface that is annotated a rust module is generated containing functions that map to the funtions defined
on the trait. I explicitly specify the module name in the attibute, i.e., `#[ext_contract(ext_transfer_receiver)]` specifies
the module name to be `ext_transfer_receiver`. The name is optional, and a default name will be generated based on the trait
name if not specified - but I prefer to be specific. 
```rust
#[ext_contract(ext_transfer_receiver)]
pub trait ExtTransferReceiver {
    fn ft_on_transfer(
        &mut self,
        sender_id: AccountId,
        amount: TokenAmount,
        msg: TransferCallMessage,
    ) -> PromiseOrValue<TokenAmount>;
}

#[ext_contract(ext_resolve_transfer_call)]
pub trait ExtResolveTransferCall {
    fn ft_resolve_transfer_call(
        &mut self,
        sender_id: AccountId,
        receiver_id: AccountId,
        amount: TokenAmount,
    ) -> PromiseOrValue<TokenAmount>;
}
```

Each module function that is generated for the external contract appends arguments required to make the remote contract
function call that is required by the NEAR protocol:
- contract account ID
- how much NEAR to attach to the function call
- how prepaid gas to supply to the function call

> #### Side topic ... 
> Something to be aware of is the the high level cross contract approach works for simple cross contract calls - as in this
> case. However, sometimes you may need to reach down to use the lower level cross contract approach because you need more
> robust error handling, or your use case requires batched transactions, etc. We'll explore this topic in future tutorials.


## Show Me the Tests

## Show Me the Demo

## It's a wrap folks...
That was longer than expected, but time flies by when you are having fun. STAKE makes staking better ... you can send
your friends some STAKE tokens.

## What's Next ...

[1]: https://github.com/oysterpack/oysterpack-near-stake-token
[2]: https://github.com/near/core-contracts/tree/master/staking-pool
[3]: 2-fungible-token.md
[4]: https://insights.stackoverflow.com/survey/2020#technology-most-loved-dreaded-and-wanted-languages-loved
[5]: https://www.rust-lang.org/
[6]: https://doc.rust-lang.org/book/ch10-02-traits.html
[7]: https://crates.io/crates/near-sdk
[8]: https://github.com/oysterpack/oysterpack-near-stake-token/blob/main/contract/src/interface/fungible_token.rs
[9]: https://crates.io/crates/near-bindgen
[10]: https://docs.rs/near-sdk/2.0.1/near_sdk/json_types/struct.ValidAccountId.html