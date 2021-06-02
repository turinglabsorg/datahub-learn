
# How to write and deploy a smart contract in Rust

## Introduction

In this tutorial, we are going to write and test a smart contract using Rust. Then we will deploy it to NEAR Testnet. 

Why Rust? Rust is the preferred programming language for writing smart contracts on NEAR. Rust offers many features like memory safety, small runtime, etc. This allows us to write a smart contract that doesn’t have memory bugs and consumes less storage on the blockchain.

## Prerequisite

Please make sure that you have completed [NEAR Intro Pathway](https://learn.figment.io/network-documentation/near/tutorials/intro-pathway-write-and-deploy-your-first-near-smart-contract). Pathway covers basics of NEAR development.

## Requirements
You should have following requirements installed:
- Rust (Installation [guide](https://www.rust-lang.org/tools/install) and if you want to learn more about Rust, check this guide [HERE](https://doc.rust-lang.org/book/))
- NEAR CLI (Installation [guide](https://www.npmjs.com/package/near-cli))
- NEAR Testnet account (If you don't have testnet account, check this guide [HERE](https://learn.figment.io/network-documentation/near/near-wallet))

## Setup

To setup our project, we need to add the WASM (WebAssembly) target to our toolchain. To add that we need to run the following command in the terminal:

```sh
rustup target add wasm32-unknown-unknown
```
What is Rust toolchain? A toolchain is a specific version of the collection of programs needed to compile a Rust application.

Why do we need to add the WASM target? Because to deploy our smart contract on NEAR, we need to compile it to WebAssembly (`.wasm` file). When we first install Rust, it downloads libraries for our host platform like Linux, Windows, etc. For compiling our Rust smart contract to WebAssembly, we need standard libraries for WebAssembly platform. The above command installs those standard libraries. You can read more about cross-compilation [HERE](https://rust-lang.github.io/rustup/cross-compilation.html)

Now, let’s create a folder called `key_value_storage`, go inside the folder and run following command in terminal:
```sh
cargo init
```
This will create a basic Rust project for us. Now open `Cargo.toml`, clear all the content in it and add the following snippet:

```
[package]
name = "key_value_storage"
version = "0.1.0"
authors = ["Your Name <Your Email>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
near-sdk = "3.1.0"

[profile.release]
codegen-units = 1
# Tell `rustc` to optimize for small code size.
opt-level = "z"
lto = true
debug = false
panic = "abort"
# Opt into extra safety checks on arithmetic operations https://stackoverflow.com/a/64136471/249801
overflow-checks = true
```
Finally, change the name of file `main.rs` to `lib.rs` as it is a convention while writing smart contracts.

We are all set now! You can use this as a template for any future project.

## Writing Smart contract

We will create a simple CRUD (Create, Read, Update, Delete) smart contract that utilizes on-chain storage offered by NEAR. You can read more about how on-chain storage works [here](https://docs.near.org/docs/concepts/storage-staking).

Now we can start by clearing all the code in `lib.rs` and the following code snippet:

```rust
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::{env, near_bindgen};
use near_sdk::collections::UnorderedMap;

near_sdk::setup_alloc!();

// 1. Main Struct
// 2. Default Implementation
// 3. Core Logic
// 4. Tests
```

First, we are importing items using `use`. Here items can be anyting like funcitons, struts, macros, etc. As we go through the tutorial, we will learn more about items we have imported now.

Then, setting up global allocator from `wee_alloc` crate using `setup_alloc!` macro. Allocators are the way that programs in Rust obtain memory from the system at runtime. 

`wee_alloc` is a memory allocator designed for WebAssembly. It generates less than a kilobyte of uncompressed WebAssembly code.

You should read more about [global allocator](https://doc.rust-lang.org/edition-guide/rust-2018/platform-and-target-support/global-allocators.html) and [wee_alloc](https://github.com/rustwasm/wee_alloc).

### Main Struct

While writing a smart contract, we are going to follow a pattern where we've one structure (`struct`) and an implementation (`impl`) associated with it. You see this pattern in most smart contracts. Add the following snippet below the comment `// 1. Main Struct`:

```rust
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
pub struct KeyValue {
    pairs: UnorderedMap<String, String>,
}
```
We have our main structure `KeyValue` which has one field `pairs`. `pairs` field have a type of `UnorderedMap` which we have imported from `near_sdk::collections`. [Unordered Map](docs.rs/near-sdk/3.1.0/near_sdk/collections/struct.UnorderedMap.html) is a data structure that utilizes underlying blockchain trie storage more efficiently.

`#[near_bindgen]` and `#[derive(BorshDeserialize, BorshSerialize)]` are attributes.

What are attributes? A declarative tag that is used to convey information to runtime about the behaviors of various elements like classes, methods, structures, enumerators, assemblies, etc.

We wrap our struct `KeyValue` in `#[near_bindgen]` and it generates a smart contract compatible with NEAR blockchain. The second attribute helps to serialization and de-serialization of the data.

### Default Implementation

Every type in Rust has a `Default` implementation but here we want provide our own default implementation for `keyValue` struct. Add following snippet below the comment `// 2. Default Implementation`:

```rust
impl Default for KeyValue {
    fn default() -> Self {
        Self {
            pairs: UnorderedMap::new(b"r".to_vec())
        }
    }
}
```

Now, let’s go step by step. First, we are creating a `Default` implementation for `KeyValue`. After that, we added the `default` method inside that implementation which returns `Self`. `Self` refers to the current type i.e. `KeyValue`. Lastly, we are returning `Self` with a new unordered map with ID `r` as a type to `pairs` field. While creating a new unordered map, we have to pass ID as `Vec<u8>` type so we are converting `b"r"` which byte string to `Vec<u8>` using `to_vec()` function. Prefix `b` is used to specify that we want byte array of the string. You can read about byte string literals [HERE](https://doc.rust-lang.org/reference/tokens.html#byte-string-literals)

### Core Logic

Now we are going to add methods to `KeyValue` struct. These methods are core logic for our smart contract. Add following snippet below the comment `// 3. Core Logic`:

``` rust 
#[near_bindgen]
impl KeyValue {
    pub fn create_update(&mut self, k: String, v: String) {
        env::log(b"created or updated");
        self.pairs.insert(&k, &v);
    }

    pub fn read(&self, k: String) -> Option<String> {
        env::log(b"read");
        return self.pairs.get(&k);
    }

    pub fn delete(&mut self, k: String) {
        env::log(b"delete");
        self.pairs.remove(&k);
    }
}
```
When creating methods, we must have an implementation block defined by the `impl` keyword followed by the name of the `struct` to be implemented. The `pub` keyword makes the methods publicly available, meaning they can be called by anyone with access to the protocol and a means of signing the transaction.

The first method `create_update` is used to create or update particular pair. It takes three arguments `self`, `k`, and `v`. 

We are using `&mut self` to borrow self mutably. You can learn more about borrowing [here](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html). 

`k` and `v` are key-value which we are going to store. 

`env::log(b"created or updated")` is used for logging.

After that, we are calling the method `insert` on `self.pairs`. This will create key-value pair if not present already otherwise it will update the value associated with the given key.

The next two methods have the same working but instead of calling the `insert` method, we call `get` and `remove` methods on `self.pairs` to read and remove key-value pair respectively.

## Testing Smart Contract

Our smart contract is now complete. Let's write some unit tests.

Why should we write unit tests for a smart contract? Writing unit tests is a common pratice in software development. While writing smart contract, units tests are more important. As smart contracts are immutable and sometimes reponsible for managing large sums of money, writing unit tests is the only way to assure that smart contracts is secure and reliable.

Add following snippet below the comment `// 4. Tests`:

```rust
#[cfg(not(target_arch = "wasm32"))]
#[cfg(test)]
mod tests {
    use super::*;
    use near_sdk::MockedBlockchain;
    use near_sdk::{testing_env, VMContext};

    fn get_context(input: Vec<u8>, is_view: bool) -> VMContext {
        VMContext {
            current_account_id: "alice_near".to_string(),
            signer_account_id: "bob_near".to_string(),
            signer_account_pk: vec![0, 1, 2],
            predecessor_account_id: "carol_near".to_string(),
            input,
            block_index: 0,
            block_timestamp: 0,
            account_balance: 0,
            account_locked_balance: 0,
            storage_usage: 0,
            attached_deposit: 0,
            prepaid_gas: 10u64.pow(18),
            random_seed: vec![0, 1, 2],
            is_view,
            output_data_receivers: vec![],
            epoch_height: 0,
        }
    }

    // test 1
    // test 2

}
```
Here we are setting our testing environment with various parameters. You can read about how to setup your testing environment [here](https://docs.rs/near-sdk/3.1.0/near_sdk/struct.VMContext.html).

Let's write our first test for `create_update` and `read` methods. Add following snippet below the comment `// test 1`:

```rust
	#[test]
    fn create_read_pair() {
        let context = get_context(vec![], false);
        testing_env!(context);
        let mut contract = KeyValue::default();
        contract.create_update("first_key".to_string(), "hello".to_string());
        assert_eq!(
            "hello".to_string(),
            contract.read("first_key".to_string()).unwrap()
        );
    }
```
First, we are creating a `context` variable by calling `get_context` function and passing it to `testing_env` macro which creates a testing environment with given parameters. Now, we have to create a `contract` variable which will use the contract that we have written. We can use this variable to call methods. 

Now, let’s call the `create_update` method to set key-value pairs. The key will be `first_key` and the value will be `hello`. After creating pair, we want to check if the right values are stored in storage. For checking, we will use the `assert_eq!` macro. It expects 2 arguments: expected value and actual value.

We will pass the expected value to be `"hello".to_string()` and for actual value, we will call the read method with `first_key` as the argument. The read method returns the value of type `Option<String>` but the type of expected value is `String`. That's why we use `unwrap` to get the value of `String` type out of `Option<String>` type.

Let's assume that key is not present in storage then `None` should be returned when we read that key. In next test we are going to test our assumtion. Add following snippet below the comment `// test 2`:

```rust
	#[test]
    fn read_nonexistent_pair() {
        let context = get_context(vec![], true);
        testing_env!(context);
        let contract = KeyValue::default();
        assert_eq!(None, contract.read("first_key".to_string()));
    }
```

Just like the first test, we will create our testing environment and `contract` variable. But here contract variable is unmutable. As we are not going to change the `contract` variable. To check `None` is returned after accessing nonexistent key using `contract.read("first_key".to_string())`, we'll use `assert_eq`. 

Now, let’s test our code. Run following command in terminal:
```javascript 
cargo test -- --nocapture
```
Tests will pass only if contract is working properly. As our smart contract is working properly we will see the following as the output:
```javascript
Finished test [unoptimized + debuginfo] target(s) in 1m 05s
     Running target/debug/deps/key_value_storage-958f616e81cf3269

running 2 tests
test tests::read_nonexistent_pair ... ok
test tests::create_read_pair ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

   Doc-tests key_value_storage

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```
## Compiling Smart Contract

As we have written and tested the smart contract, we will compile it for deployment. Run following command in terminal:
```javascript
// for linux and mac users
env 'RUSTFLAGS=-C link-arg=-s' cargo build --target wasm32-unknown-unknown --release

// for windows users 
set RUSTFLAGS=-C link-arg=-s
cargo build --target wasm32-unknown-unknown --release
```
You will see the following at the end of execution:
```javascript
Compiling near-sdk v3.1.0
Compiling key_value_storage v0.1.0 (/home/xqc/key_value_storage)
Finished release [optimized] target(s) in 1m 00s
```
We have generated optimized WASM file which we can deploy on NEAR testnet.

## Deploying Smart Contract

First you have to login into your account using `near-cli`. Run 
```javascript
near login
```
This will redirect you to browser and then click on allow button. This will get your credentials in your machine. 

Let's deploy our smart contract. Run following command in terminal:
```javascript
near deploy --wasmFile target/wasm32-unknown-unknown/release/key_value_storage.wasm --accountId YOUR_ACCOUNT_HERE
```
After the deployment is completed, you will see similar output in the terminal:
```javascript
xqc@RecycleBin:~/key_value_storage$ near deploy --wasmFile target/wasm32-unknown-unknown/release/key_value_storage.wasm --accountId 0xnik.testnetStarting deployment. Account id: 0xnik.testnet, node: https://rpc.testnet.near.org, helper: https://helper.testnet.near.org, file: target/wasm32-unknown-unknown/release/key_value_storage.wasm
Transaction Id E4uT8wV5uXSgsJpB73Uox27iPgXztWfL3b5gzxfA3fHo
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/E4uT8wV5uXSgsJpB73Uox27iPgXztWfL3b5gzxfA3fHo
Done deploying to 0xnik.testnet
```
🎉🎉 We have successfully deployed our first Rust smart contract.

## Interacting with Smart Contract

Let's try to call smart contract from the terminal. 

We'll create a key-value pair and then read it. 

Create key-value pair:
```javascript
near call YOUR_ACCOUNT_HERE create_update '{"k": "first_key", "v" : "1"}' --accountId YOUR_ACCOUNT_HERE
```
Output: 
```javascript
Scheduling a call: 0xnik.testnet.create_update({"k": "first_key", "v" : "1"})
Receipt: 6bCmuWuAbdiXWvPaTvidbJP4f3mSG4UVcE52g18ZWMq5
        Log [0xnik.testnet]: created or updated
Transaction Id AQWwThAtXWhU7HJsuD5bvi2FXHpnw5xbj5SEe94Q3MTp
To see the transaction in the transaction explorer, please open this URL in your browser
https://explorer.testnet.near.org/transactions/AQWwThAtXWhU7HJsuD5bvi2FXHpnw5xbj5SEe94Q3MTp
''
```
If you have to provide arguments to a function, then you have to add that argument as a JSON object.

Now, we will read the value of the first key:
```javascript
near call YOUR_ACCOUNT_HERE create_update '{"k": "first_key"}' --accountId YOUR_ACCOUNT_HERE
```
Output:
```javascript
Scheduling a call: 0xnik.testnet.read({"k": "first_key"})
Receipt: 8GJSpWV4Xf1JUh6YfPrHyDDUMwSx4X9214sLemym6vxa
        Log [0xnik.testnet]: read
Transaction Id 2s6j2RPEppaA97nGtUtgQiw1x3qoSZQtwELSKbDFuwXo
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/2s6j2RPEppaA97nGtUtgQiw1x3qoSZQtwELSKbDFuwXo
'1'
```
Lastly, we will delete the key:
```javascript
near call 0xnik.testnet delete '{"k": "first_key"}' --accountId 0xnik.testnet
```
Output:
```javascript
Scheduling a call: 0xnik.testnet.delete({"k": "first_key"})
Receipt: wp8YoFZC7CKUNty66VoYuaHMVej7UqcbLcK2FjpMZzk
        Log [0xnik.testnet]: delete
Transaction Id A4aDmpkfbEP8JwM5KspUiWe1zYnKgUnA5wosCiQBwour
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/A4aDmpkfbEP8JwM5KspUiWe1zYnKgUnA5wosCiQBwour
''
```

## Conclusion

In this tutorial, we have covered the following:
- Basics knowledge of Rust programming 
- Structuring the smart contract 
- Use of few functions and macro offered by NEAR SDK
- How to use on-chain storage
- Testing of smart contract 
- Deploying the smart contract

Thank you for following along with this tutorial, now take this knowledge and build amazing things on NEAR!
