---
description: OysterPack SMART STAKE Pool Contract - For Validators
---

# OysterPack SMART STAKE Pool Guide for Validators

This tutorial is meant to serve as a guide for validators for managing and operating the **next generation** [OysterPack SMART
STAKE Pool][1] contract. If you wonder why I call it the "**next generation**" staking pool, then I refer you back to my previous
tutorial. In the last tutorial I showed you how easy it was to deploy the STAKE pool contract using the STAKE Pool Factory
contract and how to get started. As a validator, it is fundamental to know your staking pool contract in depth because
it is core to running your validator business because it impacts your bottom line. 

# The Big Picture

![](../../../../.gitbook/assets/oysterpack-smart-stake-deployment.png)

![](../../../../.gitbook/assets/oysterpack-smart-stake-operator-usecases.png)

The contract is composed of 4 OysterPack SMART components:

1. Account Management
2. Contract
3. Fungible Token
4. Staking Pool

Each component in turn provides 1 or more contract API interfaces. We will be using a divide and conquer approach and 
step through each component and API interface. The contract is implemented in Rust. Thus, I will review the contract API
in Rust, and I will also show how to invoke the contract APIs using the [NEAR CLI][2]. All of the contract [source code][3] 
is available on GitHub.

## Component Dependency Graph

![](../../../../.gitbook/assets/oysterpack-smart-stake-comps.png)

Notice how everything depends on **Account Management**. That makes sense because ultimately all STAKE Pool contract 
functionality derives from accounts staking funds into the pool. This is where we will get started. 

## Account Management Component

### Storage Management API

The storage management API implements the NEAR standard NEP-145, which I have covered in depth my prior [Account Storage Standard][4]
tutorial. Proper storage management is crucial to safegaurd any multi-user contract from what I call a "Denial of Storage"
attack. Storage management closes a big security vulnerability in the first generation staking pool contracts
that are currently in use. The attack is very simple based on the facts:

1. storage is orders of magnitude more expensive than transaction gas cost
2. the contract is ultimately responsible for paying for storage usage on the blockchain

Thus, in order to prevent malicious behavior, contract should pass along the storage costs to the accounts. Otherwise,
malicious actors can easily attack a contract by allocating expensive account storage on the contract using cheap transaction
gas cost. This reason should motivate current validators to migrate off the current first generation staking pool as soon 
as possible because it is not a question of will it happen, it's only a question of when. History has taught us that vulnerabilities
will always eventually be attacked and exploited because the reality is we live in a "dog eat dog" world. 

#### Key Points

1. Accounts must register with the contract before they can stake using the contract.
2. The account storage balance can be used to collect deposits and stake them as a batch later on. When an account stakes,
   any account storage available balance will be staked.

![](../../../../.gitbook/assets/oysterpack-smart-storage-management-api.png)

```shell
DATAHUB_APIKEY=<DATAHUB_APIKEY>
NEAR_NODE_URL=https://near-testnet--rpc.datahub.figment.io/apikey/$DATAHUB_APIKEY

NEAR_ACCOUNT=<YOUR-NEAR-ACCOUNT.testnet>
NEAR_ENV=testnet
STAKE_FACTORY=<STAKE-FACTORY-ACCOUNT.testnet>
STAKE=<STAKE-FT-SYMBOL>
CONTRACT=$STAKE.$STAKE_FACTORY

# VIEW METHODS
near --node_url $NEAR_NODE_URL view $CONTRACT storage_balance_bounds 
near --node_url $NEAR_NODE_URL view $CONTRACT storage_balance_of --args '{"account_id":"oysterpack.testnet"}'

# deposits NEAR for predecessor account
near --node_url $NEAR_NODE_URL call $CONTRACT storage_deposit --accountId $NEAR_ACCOUNT --amount 1
# deposits funds into specified "account_id" for account registration purposes only
near --node_url $NEAR_NODE_URL call $CONTRACT storage_deposit --accountId $NEAR_ACCOUNT --amount 0.00393 --args '{"account_id":"oysterpack.testnet", "registration_only":true}' 
# deposits funds for account self registration, i.e, for the predecessor account
near --node_url $NEAR_NODE_URL call $CONTRACT storage_deposit --accountId $NEAR_ACCOUNT --amount 1 --args '{"registration_only":true}'
```


[1]: https://learn.figment.io/network-documentation/near/tutorials/1-project_overview/8-stake-pool-contract#how-to-operate-the-stake-pool-contract
[2]: https://docs.near.org/docs/tools/near-cli
[3]: https://github.com/oysterpack/oysterpack-smart
[4]: https://learn.figment.io/network-documentation/near/tutorials/1-project_overview/5-account-storage