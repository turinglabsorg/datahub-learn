# 6. Deploy a program

A _program_ is to Solana what a _smart contract_ is to other protocols. Once a program has been deployed, any app can interact with it by sending a transaction to a Solana cluster that will pass it to the program.

Solana programs can be written in C or in Rust. You can learn more about Solana's programs [here](https://docs.solana.com/developing/on-chain-programs/overview).

So far we've been using Solana's JS API to interact with the blockchain. In this chapter we're going to deploy a Solana program using another Solana developer tool: their CLI. We'll install it and use it through our terminal.

## Install Rust and configure the Solana CLI

{% hint style="danger" %}
There are known compatibility issues with Microsoft Windows and also Apple M1 products.

Windows users need to install [Docker Desktop](https://learn.figment.io/network-documentation/extra-guides/docker-setup-for-windows) and [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10) - then install Rust and the Solana CLI inside of the WSL filesystem. It is also important to make sure your PATH includes the location of the Solana release you have installed, such as :  
`PATH="~/.local/share/solana/install/active_release/bin:$PATH"`

macOS users with M1 products may need to build from source. Refer to this GitHub PR for more information : [https://github.com/solana-labs/solana/pull/16346/](https://github.com/solana-labs/solana/pull/16346/)
{% endhint %}

* Install the latest Rust stable from [https://rustup.rs/](https://rustup.rs/)
* Install Solana v1.6.6 or later from [https://docs.solana.com/cli/install-solana-cli-tools](https://docs.solana.com/cli/install-solana-cli-tools)

Set the CLI config URL to the devnet cluster by running this command in your Terminal:

```text
solana config set --url https://api.devnet.solana.com
```

If this is your first time using the Solana CLI, you will need to generate a new keypair. It will be written to `~/.config/solana/id.json` and will be used every time you use the CLI. Run the following command in your Terminal:

```text
solana-keygen new
```

## Understanding the hello-world program

There is a `program` folder at the app's root. It contains the Rust program `src/lib.rs` and some configuration files to help us compile and deploy it.

**It's a simple program, all it does is increment a number every time it's called.**

Let’s dissect what each part does.

```rust
use borsh::{BorshDeserialize, BorshSerialize};
use solana_program::{
    account_info::{next_account_info, AccountInfo},
    entrypoint,
    entrypoint::ProgramResult,
    msg,
    program_error::ProgramError,
    pubkey::Pubkey,
};

#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct GreetingAccount {
    pub counter: u32,
}

entrypoint!(process_instruction);
```

We start by using the borsh crate \(borsh stands for _Binary Object Representation Serializer for Hashing_\), then doing some standard includes from the solana\_program crate, one of which is the macro we use next, to declare an entry point - the `process_instruction` function :

```rust
pub fn process_instruction(
    program_id: &Pubkey, 
    accounts: &[AccountInfo],
    _instruction_data: &[u8],
```

The program\_id is the public key where the contract is stored and the accountInfo is the appAccount to say hello to.

```rust
) -> ProgramResult {
    msg!("Hello World Rust program entrypoint");
    let accounts_iter = &mut accounts.iter();
    let account = next_account_info(accounts_iter)?;
```

The return value `ProgramResult` is where the magic happens. We can print a message to the Program Log with the `msg!()` macro and then select the accountInfo by looping through using an iterator although in practice there is likely only one value.

```rust
if account.owner != program_id {
  msg!("Greeted account does not have the correct program id");
  return Err(ProgramError::IncorrectProgramId);
}
```

Security check to see if the account owner has permission. If the account owner does not equal the `program_id` we will return an error.

```rust
let mut greeting_account = GreetingAccount::try_from_slice(&account.data.borrow())?;
greeting_account.counter += 1;
greeting_account.serialize(&mut &mut account.data.borrow_mut()[..])?;

msg!("Greeted {} time(s)!", greeting_account.counter);

Ok(())
```

Finally we get to the good stuff where we “borrow” the existing account data, increase the value of `counter` by one and write it back to storage. We can show in the Program Log how many times the count has been incremented by using the `msg!()` macro. 

### Overview

```rust
use borsh::{BorshDeserialize, BorshSerialize};
use solana_program::{
    account_info::{next_account_info, AccountInfo},
    entrypoint,
    entrypoint::ProgramResult,
    msg,
    program_error::ProgramError,
    pubkey::Pubkey,
};

// Define the type of state stored in accounts
#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct GreetingAccount {
    // number of greetings
    pub counter: u32,
}

// Declare and export the program's entrypoint
entrypoint!(process_instruction);

// Program entrypoint's implementation
pub fn process_instruction(
    program_id: &Pubkey, // Public key of the account the hello world program was loaded into
    accounts: &[AccountInfo], // The account to say hello to
    _instruction_data: &[u8], // Ignored, all helloworld instructions are hellos
) -> ProgramResult {
    msg!("Hello World Rust program entrypoint");

    // Iterating accounts is safer then indexing
    let accounts_iter = &mut accounts.iter();

    // Get the account to say hello to
    let account = next_account_info(accounts_iter)?;

    // The account must be owned by the program in order to modify its data
    if account.owner != program_id {
        msg!("Greeted account does not have the correct program id");
        return Err(ProgramError::IncorrectProgramId);
    }

    // Increment and store the number of times the account has been greeted
    let mut greeting_account = GreetingAccount::try_from_slice(&account.data.borrow())?;
    greeting_account.counter += 1;
    greeting_account.serialize(&mut &mut account.data.borrow_mut()[..])?;

    msg!("Greeted {} time(s)!", greeting_account.counter);

    Ok(())
}
```

## Testing the program

{% hint style="warning" %}
Apple M1 users may encounter an issue here. This step is not a requirement, but testing programs before deployment is a good practice. Refer to the comments on this Pull Request for more information : [https://github.com/solana-labs/solana/pull/16346](https://github.com/solana-labs/solana/pull/16346)
{% endhint %}

To ensure that the program code passes any tests defined in the sourcefile, it is good to run the tests before building and deploying.  
  
Simply run the command `cargo test` inside of the `learn-solana-dapp/program` subdirectory. The first time you do this, Cargo will need to compile a lot of related crates \(libc, borsh, the Solana crates, even the program we are testing\). This process can take a few minutes but future tests will occur much more rapidly once everything is compiled. The output from a successful `cargo test` will look like this :

```bash
running 1 test
test test::test_sanity ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

     Running tests/lib.rs (target/debug/deps/lib-560d97a774ffe546)

running 1 test
[2021-06-22T16:43:02.687502000Z INFO  solana_program_test] "helloworld" program loaded as native code
[2021-06-22T16:43:02.836044000Z DEBUG solana_runtime::message_processor] Program log: Hello World Rust program entrypoint
[2021-06-22T16:43:02.836100000Z DEBUG solana_runtime::message_processor] Program log: Greeted 1 time(s)!
[2021-06-22T16:43:03.039766000Z DEBUG solana_runtime::message_processor] Program log: Hello World Rust program entrypoint
[2021-06-22T16:43:03.039812000Z DEBUG solana_runtime::message_processor] Program log: Greeted 2 time(s)!
test test_helloworld ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.54s

   Doc-tests helloworld

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00S
```

## Building the program

The first thing we're going to do is compile the Rust program to prepare it for the CLI. To do this we're going to use a custom script that's in `package.json`:

{% code title="package.json" %}
```javascript
scripts: {
  ...
  "build:program-rust": "cargo build-bpf --manifest-path=program/Cargo.toml --bpf-out-dir=dist/program"
  ...
}
```
{% endcode %}

* `cargo` is Rust’s build system and package manager \([docs](https://doc.rust-lang.org/book/ch01-03-hello-cargo.html)\), like what `npm` is to Javascript.
* `build-bpf` is the cargo command we're going to run to build/compile the program. This is installed with the Solana CLI. We pass it a manifest file and the desired output directory.

Let's build the program by running the following command in the terminal \(from the project root directory\):

```text
yarn run build:program-rust
```

{% hint style="warning" %}
This step can take 5 or 10 minutes!
{% endhint %}

If it's successful you should see a new folder in your app which contains the compiled contract: `hello-world.so`.

{% hint style="info" %}
The `.so` extension does not stand for Solana! It stands for "shared object". The helloworld program we wrote is a Rust program compiled to Berkeley Packet Format \(BPF\) and stored as an Executable and Linkable Format \(ELF\) shared object.

You can read more about Solana Programs [here](https://docs.solana.com/developing/on-chain-programs/overview).
{% endhint %}

### Potential errors

An error like `no such subcommand` indicates that there was an issue with the installation of the Solana CLI or that it is installed, but not in the PATH. So if you see this error and exit code 101 :

```text
$ cargo build-bpf --manifest-path=program/Cargo.toml --bpf-out-dir=dist/program
error: no such subcommand: `build-bpf`
error Command failed with exit code 101.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

Be sure to set your PATH according to your installed Solana release, such as :

`PATH="~/.local/share/solana/install/active_release/bin:$PATH"`

## Deploying the program

Next we're going to deploy the program to the devnet cluster. The CLI provides a very simple interface for this:

```bash
solana program deploy dist/program/helloworld.so
```

## Potential issues deploying

### Insufficient Funds

The first time you run this, the CLI should error out because the account trying to deploy this program **does not have enough funds to spend**. You can fix this by going back a few steps in the React app to the "Fund" step and pasting in the input the public key that you see in the Terminal error. Click "Fund" a few times to get enough devnet tokens to be able to deploy the program

![](../../../.gitbook/assets/screen-shot-2021-06-14-at-8.20.30-pm.png)

### Custom program error 0x1

If you see

> Error: Deploying program failed: Error processing Instruction 1: custom program error: 0x1

simply re-run the deploy command until it succeeds. If you run out of funds, go back to the "Fund" step and get more!

### Program's authority X does not match authority provided Y

This can be due to how your Solana keypair was generated. You can generate a fresh one by running :

```bash
solana-keygen new --force
```

Then go through the tutorial steps again \(fund, build, deploy\).

## After successfully deploying the program

On success, the CLI will print the programId of the deployed contract.

```bash
Program Id: DwpsLw56wmAr3FMZiWHHK47vwZx9LYreT9r32Sn9tBZ5
```

If you visit [https://explorer.solana.com](https://explorer.solana.com/), change the cluster to devnet and paste this Program Id string, you should see a page like this:

{% hint style="warning" %}
Make sure you select Devnet on the Solana Explorer!
{% endhint %}

![](../../../.gitbook/assets/screen-shot-2021-06-14-at-3.52.29-pm.png)

Notice that the field `Executable` is now set to `Yes` because the address we're looking up is for a program which can be called and executed.

## Save the program and its author secret keys

Before we move to the next step we need to save two important variables

1. In your terminal run `cat ~/.config/solana/id.json` and copy its output. In `src/components/Call.jsx` assign it to the constant `PAYER_SECRET_KEY`. This is the Keypair information of the author of the program \(you!\). We will need to pass it to transactions we make to the program  to authenticate ourselves as the owner of the program. 
2. In the directory, find `dist/program/helloworld-keypair.json` and copy its contents. In `/src/components/Call.jsx` assign it to the constant `PROGRAM_SECRET_KEY`. This is the Keypair information of the program itself. We will need it to generate the program's public key that will be used to call the program.

## Next

So at this point, we've deployed our dummy smart contract to Solana's devnet cluster. We're finally ready for the big moment: Interacting with the program by calling its functionality from the UI!

