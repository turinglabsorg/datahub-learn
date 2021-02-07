---
description: Complete guide to unit test your NEAR Rust smart contracts
---

# Test Now Or Forever Hold Your Peace 

![](../../../../.gitbook/assets/oysterpack-testing-code-meme.jpg)

Don't try this at home folks because it is very dangerous! Sadly this happens much too often than folks like to admit. 
In the corporate world, some might even say that is the price to pay for "agile" ... All kidding aside, you better have 
thoroughly tested your smart contract before you stripped it of all access keys because you will not get a second chance. 
After all access keys are deleted from the contract, the deployed contract is locked down and becomes **immutable**. 
Immutable means the contract code can never change and becomes married to the blockchain forever ... until death do us part. 

It boils down to risk management. In order to reap the high rewards that smart contracts offer, we must manage the high
risk that comes along. Hopefully we can learn from mistakes that other have made like the [DAO attack][4] or the 
[second Parity Wallet Hack][5] where literally hundreds of millions of dollars worth of assets were lost. Smart contracts
is serious business, and DeFi smart contracts are serious money. Testing is the crucial pillar of the software development 
process that directly correlates to risk. I hope I have your attention by now.

Testing is a huge topic and all we will only have time here to get started and focus on unit testing. Unit testing is just
the beginning and acts as a critical first line of defense. In this tutorial, we will continue where we left off from 
last time. We'll get hands-on and write unit tests for the fungible token NEP-141 interface on the [STAKE][3] project. 
There's a lot to cover, so let's roll up our sleeves and get to work ...

{% hint style="danger" %}
#### WARNING: Do not trust contract code blindly
The smart contract deployment is considered immutable after all full access keys have been deleted assuming the contract
does not contain any malicious code that will re-add full access keys to itself or redeploy. This means smart contracts
can only be fully trusted if the code has been audited either by yourself or by a trusted third party. 
{% endhint %}

## Show Me the Tests
Lucky for us, Rust provides comes with batteries included with robust support for [testing][1]. We'll also leverage the
NEAR Rust SDK which also provides great support for unit testing. The STAKE project links to NEAR Rust SDK [v2.4.0][6]
on GitHub to take advantage of the latest and greatest features. The current version of the NEAR Rust SDK on [crates.io][7]
as of this writing is v2.0.1, and unit testing support has changed a bit in v2.4.0 with some non-backward compatible breaking
changes. To pull in NEAR Rust SDK [v2.4.0][6] into the project, specify the dependency in Cargo.toml as:

```toml
[dependencies]
near-sdk = { git = "https://github.com/near/near-sdk-rs",  tag = "2.4.0" }
```

{% hint style="info" %}
#### How to generate NEAR Rust SDK locally
```bash
gh repo clone near/near-sdk-rs -- --branch 2.4.0
cd near-sdk-rs

# edit the rust-toolchain to use the Rust stable toolchain
echo stable > rust-toolchain
cd near-sdk
# builds the NEAR Rust SDK docs and opens the docs in your web browser
cargo doc --no-deps --open
```
**NOTE:** I prefer to use the [GitHub CLI][7] tool when working with GitHub
{% endhint %}

The relevant code to look at is located in the following files:
- [lib.rs][9] - the thing to pay attention to is StakeTokenContract::env
- [contract.rs][12] - decouples the contract from `near_sdk::env`, which is "hard wired" to the contract by default (this will make more sense down below)
- [test_utils.rs][10] - builds upon NEAR SDK unit testing support
- [contract/fungible_token.rs][11] - the unit test modules are located at the bottom of the file along side the fungible 
  token NEP-141 implementation
  
### How we will structure the test code
I will share with you my coding standards, but the key here is to have coding standards. Be consistent and disciplined 
in following the coding standard that is in place. This keeps the code better organized, easier to navigate, and simpler
to follow.

##### 1. Create one test module per contract function. For example:
```rust
#[cfg(test)]
mod test_transfer {
  // tests
}

#[cfg(test)]
mod test_transfer_call {
  // tests
}

#[cfg(test)]
mod test_resolve_transfer_call {
  // tests
}
```

#### 2. Unit tests are written following the [Arrange-Act-Assert][2] test pattern
```rust
#[test]
pub fn transfer_ok() {
    // Arrange
    // TODO: setup the test
  
    // Act
    // TODO: execute the code to test
  
    // Assert
    // TODO: check the test results
}
```

### NEAR Rust SDK Unit Testing Support
Here's what we use from the NEAR Rust SDK for unit testing:
- module `near_sdk::test_utils` 
  - struct `VMContextBuilder` - makes it easier to construct a `near_sdk::VMContext`
  - function`get_created_receipts()` - used to check that contract functions are creating the expected receipts
  - function `get_logs()` - used to check that the contract functions are emitting the expected logs
- `near_sdk::VMContext` - this is the key center piece for unit testing contract execution
- macro `near_sdk::testing_env!` - is the glue that brings it all together by preparing a mocked blockchain testing environment
  with a provided near_sdk::VMContext`
  
At a high level, the general unit test code pattern is:
```rust
// Arrange
let ctx: VMContext = create_vm_context();
testing_env!(ctx);                         // provides mocked blockchain with specified VMContext for the contract
let contract = create_contract();

// Act
contract.foo(); // execute contract function

// Assert
// check contract state
// check receipts
// check logs
// ...
```

### Mocking out the NEAR blockchain runtime environment  
The key to unit testing cross contract calls is to be able to inject receipts to simulate different test scenarios such as
- providing promise results to callbacks
- simulating promise failures

The NEAR Rust SDK does not provide any such ability and the NEAR team suggests using NEAR Rust SDK simulation tests for
testing cross contract calls. Simulation tests are great, but they are ***very*** slow to run. On my beefy laptop running
with 24 cores (3rd Gen AMD® Ryzen™ 9 PRO 3900: 3.1 up to 4.3 GHz - 12 Cores - 24 Threads) and 64 GB RAM, it takes on the 
order of minutes to run simple cross contract simulation tests. I want to be able to test cross contract call functionality
as much as possible before moving onto simulation tests because I can run unit tests orders of magnitude faster. 

To be able to inject promise results into unit tests, the code is decoupled from the `near_sdk::env` through a facade that
leverages Rust conditional compilation:

[contract.rs][12]
```rust
#[cfg(not(test))]
impl StakeTokenContract {
  /// checks if the first PromiseResult was successful
  ///
  /// ## Panics
  /// if there are no promise results - this should only be called if promise results are expected
  pub fn promise_result_succeeded(&self) -> bool {
    match env::promise_result(0) {
      PromiseResult::Successful(_) => true,
      _ => false,
    }
  }

  pub fn promise_result(&self, result_index: u64) -> PromiseResult {
    env::promise_result(result_index)
  }
}

/// in order to make it easier to unit test Promise func callbacks, we need to abstract away the near env
#[cfg(test)]
impl StakeTokenContract {
  /// checks if the first PromiseResult was successful
  ///
  /// ## Panics
  /// if there are no promise results - this should only be called if promise results are expected
  pub fn promise_result_succeeded(&self) -> bool {
    match self.env.promise_result(0) {
      PromiseResult::Successful(_) => true,
      _ => false,
    }
  }

  pub fn promise_result(&self, result_index: u64) -> PromiseResult {
    self.env.promise_result(result_index)
  }

  pub fn set_env(&mut self, env: near_env::Env) {
    self.env = env;
  }
}

#[cfg(test)]
pub(crate) mod near_env {
    use near_sdk::PromiseResult;

    /// abstracts away the NEAR env
    /// - this enables the Near env to be decoupled to make it easier to test
    pub struct Env {
        pub promise_results_count_: fn() -> u64,
        pub promise_result_: fn(u64) -> PromiseResult,
    }

    impl Env {
        pub fn promise_results_count(&self) -> u64 {
            (self.promise_results_count_)()
        }

        pub fn promise_result(&self, result_index: u64) -> PromiseResult {
            (self.promise_result_)(result_index)
        }
    }

    impl Default for Env {
        fn default() -> Self {
            Self {
                promise_results_count_: near_sdk::env::promise_results_count,
                promise_result_: near_sdk::env::promise_result,
            }
        }
    }
}
```
All contract interactions with the `near_sdk::env` go through the following functions:
- `promise_result_succeeded()`
- `promise_result()`

The implementation for those functions is chosen conditionally at compile time depending on the specified compile mode:
- release mode - functions use `near_sdk::env` directly
- test mode - functions use `near_env::Env` instead which enables promise results to be injected via functions

The last piece to the puzzle to make this all work is `StakeTokenContract::env` ([lib.rs][9]):
```rust
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize, PanicOnDefault)]
pub struct StakeTokenContract {
    // ...
  
    #[cfg(test)]
    #[borsh_skip]
    env: near_env::Env,
}
```
This is telling the Rust compiler to only add the `env` field when compiled in test mode. 

Cool - using this Rust conditional compilation trick, we will be able to unit test contract callback functions easily. 

### Key Ingredients For Unit Testing Contract Functions

1. **VMContext** - provided by the NEAR Rust SDK - it provides the context for contract execution
2. Contract instance
3. NEAR account used to execute the contract functions

In the STAKE project there is a [test_utils][20] module that you may find useful. I'll call out what's most interesting
and relevant to our current discussion - see the source code on github for full details.

In the STAKE project, the first thing each contract unit test does is create a **TestContext** to get the 3 key ingredients:
```rust
pub struct TestContext<'a> {
    pub contract: StakeTokenContract,
    pub account_id: &'a str,
    pub context: VMContext,
}
```
- TestContext is simply a wrapper that collects what is needed to test the contract

Knowing how to work with the VMContext provided by the NEAR Rust SDK is half the battle. It will be  worth your time to
familiarize yourself and become comfortable working with it. The basic pattern to work with the VMContext is:
1. Clone the VMContext
2. Modify the VMContext to setup the test
3. Update the testing env with the new VMContext
4. Execute contract function

For example:
```rust
#[test]
pub fn ok_with_refund_gt_transfer_amount() {
  // Arrange
  let mut test_ctx = TestContext::with_registered_account();            // initial TestContext contains VMContext
  let contract = &mut test_ctx.contract;

  let sender_id = test_ctx.account_id;
  let receiver_id = "receiver.near";

  // register receiver account
  {
    let mut context = test_ctx.context.clone();                         // clone the VMContext
    context.predecessor_account_id = receiver_id.to_string();           // modify the VMContext
    context.attached_deposit = YOCTO;
    testing_env!(context);                                              // Update the testing env with the new VMContext
    contract.register_account();                                        // Execute contract function
  }

  ...
}
```

### Unit Testing Cross-Contract Calls
The title is a little misleading because technically you can't unit test cross contract calls for technical reasons
discussed above. To test cross contract calls locally, you would use simulation tests. However, simulation tests also
has its limitations for more complex workflows. Ultimately, you will need to run integration tests on testnet to fully
test more complex cross-contract workflows ... but I digress ...

Try to test as much as possible with unit tests because they run the fastest and give the quickest feedback. Unit tests
allow us to verify that the expected cross-contract workflows are setup correctly.

Let's take a look at the happy case scenario for the `ft_transfer_call`:
```rust
#[test]
pub fn transfer_ok() {
  // Arrange
  let mut test_ctx = TestContext::with_registered_account();                            
  let contract = &mut test_ctx.contract;

  let sender_id = test_ctx.account_id;
  let receiver_id = "receiver.near";

  // register receiver account
  {
    let mut context = test_ctx.context.clone();
    context.predecessor_account_id = receiver_id.to_string();
    context.attached_deposit = YOCTO;
    testing_env!(context);
    contract.register_account();
  }

  assert!(contract.account_registered(to_valid_account_id(sender_id)));
  assert!(contract.account_registered(to_valid_account_id(receiver_id)));

  assert_eq!(contract.ft_total_supply(), 0.into());
  assert_eq!(
    contract.ft_balance_of(to_valid_account_id(sender_id)),
    0.into()
  );
  assert_eq!(
    contract.ft_balance_of(to_valid_account_id(receiver_id)),
    0.into()
  );

  // credit the sender with STAKE
  let mut sender = contract.registered_account(sender_id);
  let total_supply = YoctoStake(100 * YOCTO);
  sender.apply_stake_credit(total_supply);
  contract.total_stake.credit(total_supply);
  contract.save_registered_account(&sender);

  // Act - transfer with no memo
  let mut context = test_ctx.context.clone();
  context.predecessor_account_id = sender_id.to_string();
  context.attached_deposit = 1; // 1 yoctoNEAR is required to transfer
  testing_env!(context.clone());
  let transfer_amount = 10 * YOCTO;
  let msg = TransferCallMessage::from("pay");
  contract.ft_transfer_call(
    to_valid_account_id(receiver_id),
    transfer_amount.into(),
    msg.clone(),
    None,
  );

  // Assert
  
  // check that the funds were transfered
  assert_eq!(contract.ft_total_supply().value(), total_supply.value());
  assert_eq!(
    contract
            .ft_balance_of(to_valid_account_id(sender_id))
            .value(),
    total_supply.value() - transfer_amount
  );
  assert_eq!(
    contract
            .ft_balance_of(to_valid_account_id(receiver_id))
            .value(),
    transfer_amount
  );
  let sender = contract.predecessor_registered_account();
  assert_eq!(sender.near.unwrap().amount().value(), 1,
             "expected the attached 1 yoctoNEAR for the transfer to be credited to the account's NEAR balance");
  
  // check that the Promise workflow is setup correctly for the transfer call
  let receipts = deserialize_receipts();
  assert_eq!(receipts.len(), 2);
  {
    let receipt = &receipts[0];
    match &receipt.actions[0] {
      Action::FunctionCall {
        method_name,
        args,
        deposit,
        gas,
      } => {
        assert_eq!(method_name, "ft_on_transfer");
        assert_eq!(*deposit, 0);
        let args: TransferCallArgs = serde_json::from_str(args).unwrap();
        assert_eq!(args.sender_id, to_valid_account_id(sender_id));
        assert_eq!(args.amount, transfer_amount.into());
        assert_eq!(args.msg, msg);
        assert!(*gas >= context.prepaid_gas - (TGAS * 35).value())
      }
      _ => panic!("expected `ft_on_transfer` function call"),
    }
  }
  {
    let receipt = &receipts[1];
    match &receipt.actions[0] {
      Action::FunctionCall {
        method_name,
        args,
        deposit,
        gas,
      } => {
        assert_eq!(method_name, "ft_resolve_transfer_call");
        assert_eq!(*deposit, 0);
        let args: ResolveTransferCallArgs = serde_json::from_str(args).unwrap();
        assert_eq!(args.sender_id, to_valid_account_id(sender_id));
        assert_eq!(args.receiver_id, to_valid_account_id(receiver_id));
        assert_eq!(args.amount, transfer_amount.into());
        assert_eq!(
          *gas,
          contract
                  .config
                  .gas_config()
                  .callbacks()
                  .resolve_transfer_gas()
                  .value()
        )
      }
      _ => panic!("expected `ft_on_transfer` function call"),
    }
  }
}
```
The `ft_transfer_call` is expected to produce 2 function call action receipts for the cross-contract workflow.
The unit test is able to verify the following:
- That the cross-contract workflow is setup correctly
- That the correct amounts of gas is supplied to each function call
- That the function call arguments are correct

That's pretty cool for a unit test and it was made possible because NEAR Rust SDK exposed receipts for testing - see
`deserialize_receipts()` for details on how to retrieve the receipts.

Unit testing the callback is a bit more tricky because of the data dependencies ...
```rust
#[test]
pub fn ok_zero_refund() {
    // Arrange
    let mut test_ctx = TestContext::with_registered_account();
    let contract = &mut test_ctx.contract;

    let sender_id = test_ctx.account_id;
    let receiver_id = "receiver.near";

    // register receiver account
    {
        let mut context = test_ctx.context.clone();
        context.predecessor_account_id = receiver_id.to_string();
        context.attached_deposit = YOCTO;
        testing_env!(context);
        contract.register_account();
    }

    set_env_with_promise_result(contract, promise_result_zero_refund);

    // Act
    let result = contract.ft_resolve_transfer_call(
        to_valid_account_id(sender_id),
        to_valid_account_id(receiver_id),
        YOCTO.into(),
    );

    // Assert
    match result {
        PromiseOrValue::Value(refund_amount) => assert_eq!(refund_amount.value(), 0),
        _ => panic!("expected value to be returned"),
    }
}

/// used to inject PromiseResult into NEAR testing env
pub fn set_env_with_promise_result( contract: &mut StakeTokenContract, promise_result: fn(u64) -> PromiseResult) {
  pub fn promise_results_count() -> u64 {
    1
  }

  contract.set_env(Env {
    promise_results_count_: promise_results_count,
    promise_result_: promise_result,
  });
}

// used to inject a PromiseResult that provides the function call result for `TransferReceiver::ft_on_transfer`
fn promise_result_zero_refund(_result_index: u64) -> PromiseResult {
  PromiseResult::Successful(serde_json::to_vec(&TokenAmount::from(0)).unwrap())
}
```
The key to making this work is the magic performed by `set_env_with_promise_result(contract, promise_result_zero_refund);`
To summarize how this works, the Rust `env` is proxied . Instead of the contract using `env` provided by the NEAR Rust SDK 
directly, it was wrapped in order to be able to inject promise results into it - but only when the code is compiled in 
test mode. Rust conditional compilation feature is leveraged to select which `env` to use. If the code is compiled
in release mode, then it uses NEAR's provided `env`. Take a look at [lib.rs][16] to see exactly how that was done. If there 
any questions, feel free to post them on the tutorial.

[1]: https://doc.rust-lang.org/book/ch11-00-testing.html
[2]: https://automationpanda.com/2020/07/07/arrange-act-assert-a-pattern-for-writing-good-tests/
[3]: https://github.com/oysterpack/oysterpack-near-stake-token
[4]: https://medium.com/swlh/the-story-of-the-dao-its-history-and-consequences-71e6a8a551ee
[5]: https://hackernoon.com/parity-wallet-hack-2-electric-boogaloo-e493f2365303
[6]: https://github.com/near/near-sdk-rs/tree/2.4.0
[7]: https://crates.io/crates/near-sdk
[8]: https://cli.github.com/
[9]: https://github.com/oysterpack/oysterpack-near-stake-token/blob/main/contract/src/lib.rs
[10]: https://github.com/oysterpack/oysterpack-near-stake-token/blob/main/contract/src/test_utils.rs
[11]: https://github.com/oysterpack/oysterpack-near-stake-token/blob/main/contract/src/contract/fungible_token.rs
[12]: https://github.com/oysterpack/oysterpack-near-stake-token/blob/main/contract/src/contract.rs