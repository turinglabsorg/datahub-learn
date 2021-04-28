---
description: OysterPack SMART STAKE Pool Contract - NEAR Staking Game Changer
---

# It's Time to Put a STAKE Pool in the Ground!

In my last tutorial, [It's Time to Put a STAKE in the Ground!][1], I shared with you my STAKE vision. That time has arrived. 
Since then, I have been busy building the next generation staking pool contract as a gift for all in the NEAR community.
The new OysterPack SMART STAKE Pool contract is ready for use on the NEAR testnet network. The project is fully open source
and freely available on GitHub: [OysterPack SMART][2]. The new OysterPack SMART STAKE Pool contract also showcases the
**OysterPack SMART NEAR Component Based Framework**. The agenda for this tutorial is:

1. Show you the benefits that the OysterPack SMART STAKE pool provides 
2. Show you how to deploy and use the STAKE pool contract as a validator
3. Show you how to use the STAKE pool contract as a delegator

## OysterPack SMART STAKE Benefits

Today there only lives a single staking pool contract in the NEAR wild. All validators on mainnet are using the [staking pool][3] 
built by the NEAR core DEV team to bootstrap staking on NEAR's PoS blockchain. Staking on NEAR is permissionless, which
enables staking to evolve. This illustrates the power and beauty of decentralization on the blockchain. There is nothing
stopping any person with new ideas to build and create on the blockchain. The current staking pool on NEAR works, but there's
always room for improvement - I call it the first generation staking pool. Here's the list of improvements for the next
generation OysterPack SMART STAKE Pool and how it compares to the first generation staking pool:

### Locked staked NEAR is made mobile through **STAKE fungible tokens** provided by the STAKE pool 

This enables staked NEAR value to be transferred while still being staked. This benefit opens the door to many new DeFi use cases for staked NEAR. 

### Staking Done Right - Maximizing Yield

The first generation staking pool will only stake deposited funds and restake earnings once every epoch. Earnings are composed
of staking rewards plus any contract rewards earned from transaction gas fees. Because earnings are only restaked once per
epoch, you lose some potential yield from less compounding. The compounding yield opportunity is lost for earnings outside staking
rewards because the NEAR protocol only issues staking rewards once per epoch. Thus, today some yield is left on the table
for contract earnings received from transaction gas fees, which impacts long term stakers the most because of less compounding.
The next generation STAKE pool maximizes yield by checking for earnings in each pool transaction and restakes earnings as 
soon as they are received to maximize the power of the compounding yield effect.

### Unstaking Done Right

The following 2 improvements enable accounts to withdraw unstaked NEAR sooner:

1. Unstaked NEAR is always available for withdrawal in **at most** 4 epochs per the NEAR protocol. 
Unstaked NEAR is locked for 4 epochs before it becomes available to be withdrawn, but is tracked per epoch. Thus, more 
funds can be unstaked without affecting funds that were unstaked in previous epochs. Compare this to the NEAR provided staking pool, 
where each time you unstake, it resets the lockup period to 4 epochs for the total unstaked NEAR balance. For example, 
if 100 NEAR is unstaked in EPOCH 1 and 10 NEAR is unstaked in EPOCH 3. Then 100 NEAR is available for withdrawal in 
EPOCH 5 and 10 NEAR in EPOCH 7. In the current NEAR provided staking pool implementation, unstaking in the 10 NEAR in 
EPOCH 3 would reset the lock period for the total unstaked, i.e., you would not be able to withdraw the 100 NEAR that 
was unstaked in EPOCH 1 until EPOCH 7.
2. Staking adds **liquidity** for withdrawing unstaked NEAR that is locked on a first come, first withdraw basis.
For example, if you unstake 100 NEAR in EPOCH 1, normally you would not be able to withdraw the unstaked NEAR out of the 
pool until EPOCH 5. However, when other accounts stake while there are locked unstaked funds in the STAKE pool, then the 
new staked funds effectively add liquidity and unlock the unstaked funds. Think of it as the unstaked funds are being restaked.
Thus, higher staking activity automatically provides more liquidity. 
   
### Enhanced Financial Model

The goal is to provide more financial levers to validators in order to promote competition for staker business. 

__More Flexible Fee Model__
1. **Earnings based fee**
The staking pool owner takes a percentage of the STAKE pool earnings. This matches the current commercial fee model implemented
by the first generation staking pool. This fee keeps the financial incentives and interests aligned with all stakers. 
Owner earnings are directly aligned with the STAKE pool earnings.
2. **Staking fee**
This fee type is not supported by the first generation staking pool. The staking fee is a percentage of the amount staked.
OysterPack SMART STAKE pools provide more commercial levers and can be configured to use a combination of earnings based 
fees and staking fees. For example, validators may choose to charge only a staking fee and pass on all earnings to delegators.
   
__Enables External Revenue Sources for Boosting EPS__
1. **External revenue distributions**
External sources of revenue can be deposited into the STAKE pool and distributed to all current stakers simply by staking
the funds. This immediately distributes the revenue earnings via the STAKE token. 
2. **External revenue can be deposited into the treasury to distribute dividends**
Dividends are distributed by the treasury by burning STAKE for earnings it receives. Thus, when STAKE is burned, the validator
is effectively buying back shares funded by treasury earnings, which boosts the STAKE token value.

STAKE is modeled as a **dividend stock**. STAKE links the dividend yield directly to EPS (earnings per share or earnings per STAKE). 
When EPS increases, so does the dividend, automatically paid out and governed by the contract (and not a board of directors). 
The STAKE pool contract enables validators to compete on EPS on more than just staking rewards provided by NEAR PoS.

![](../../../../.gitbook/assets/oysterpack-smart-stake-earnings.png)

## STAKE High Level Component Based Architecture

![](../../../../.gitbook/assets/oysterpack-smart-stake-deployment.png)

### STAKE Pool Factory Contract

The factory contract makes it easy for anyone to deploy a new instance of the STAKE pool contract. We'll see how to deploy
new STAKE pool contracts using the NEAR CLI below. 

It currently costs a little over 5 NEAR to deploy the STAKE pool contract to pay for contract storage usage. Thus, if you 
attach 6 NEAR, then you should be safe. Any extra will be transferred over to the owner's account storage balance. If deployment
fails for any reason, then the factory contract is designed to refund the attached deposit.

### STAKE Pool Contract

The STAKE Pool Contract is composed of 4 components, which are depicted on the right-hand side of the diagram. Each component
provides multiple interfaces which are paired up in the diagram by the coloring scheme. The diagram also depicts the main 
actors and the key interfaces they depend on. In this tutorial, we will just be scratching the surface and focus on the 
core staking functionality to get started.

> The STAKE pool contract is built using a component based architecture. If you happen to wander into to the source code,
> you will probably notice that it follows a completely different design approach to build contracts on NEAR compared to what
> you are probably used to seeing. There's nothing special besides applying software engineering best practices. A component
> based approach enables component reuse across contracts - and I have plans for building many. My plan is to eventually publish
> them all to https://crates.io to make it easy for developers to use them. Until then, if you are interested in using them,
> you'll need to pull them in from the GitHub project. I'll end the discussion on the benefits of a component based architecture
> here by putting it into context for web developers. If you prefer building web apps using React components, then you should
> also prefer building contracts using OysterPack SMART components for the same reasons.

> You might also notice that I have built components that implement the NEAR standard APIs that I have covered in prior 
> tutorials for account storage management and fungible tokens, but there's much more ...

## How to Get Started as a Validator

I will not be covering on how to setup and run your own validator node. If you are interested in running your own
validator node, then I refer you to the NEAR [staking][4] docs. 

### How to deploy the STAKE pool contract

> To earn NEAR rewards for exercising the NEAR CLI commands, you will need to submit the NEAR requests through [DataHub][6] 
> using your DataHub access key. If you have earned NEAR on previous NEAR tutorials, then you should already be set. 
> Otherwise, follow the instructions in the following link on [how to obtain your DataHub access key][7].
> We will use the NEAR CLI to submit the transactions. Plugin your DataHub API Key and NEAR account at the top, and then you should be all set to go.

Below is a NEAR CLI example which deploys a new STAKE contract on testnet:
```shell
DATAHUB_APIKEY=<DATAHUB_APIKEY>
NEAR_NODE_URL=https://near-testnet--rpc.datahub.figment.io/apikey/$DATAHUB_APIKEY

NEAR_ACCOUNT=<YOUR-NEAR-ACCOUNT.testnet>
NEAR_ENV=testnet
STAKE_FACTORY=dev-1619461926372-2886358
ACCOUNT_ID=oysterpack.testnet

STAKE_PUBLIC_KEY=ed25519:GTi3gtSio5ZYYKTT8WVovqJEob6KqdmkTi8KqGSfwqdm
STAKE_SYMBOL=PEARL3
STAKING_FEE=1
EARNINGS_FEE=50

near call $STAKE_FACTORY deploy --accountId $ACCOUNT_ID --amount 6 --gas 300000000000000 --node_url $NEAR_NODE_URL --args \
"{\"stake_symbol\":\"$STAKE_SYMBOL\",\"stake_public_key\":\"$STAKE_PUBLIC_KEY\",\"earnings_fee\":$EARNINGS_FEE,\"staking_fee\":$STAKING_FEE}"
```
If you successfully run the command, then the output will look like:
```text
Scheduling a call: dev-1619461926372-2886358.deploy({"stake_symbol":"PEARL3","stake_public_key":"ed25519:GTi3gtSio5ZYYKTT8WVovqJEob6KqdmkTi8KqGSfwqdm","earnings_fee":50,"staking_fee":1}) with attached 6 NEAR
Receipt: 8DxtrFzCzW6iz98NHwEFy7mB9W4qgT8PZUwkZL3zd5b9
        Log [dev-1619461926372-2886358]: [INFO] [DEPLOYMENT] ContractOwnershipComponent
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(97)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(184)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(-97)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(-184)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(97)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(184)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(8)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(-105)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(-184)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(104)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(-104)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(97)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] Registered(StorageBalance { total: YoctoNear(3930000000000000000000), available: YoctoNear(0) })
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(8)
        Log [dev-1619461926372-2886358]: [INFO] [DEPLOYMENT] AccountManagementComponent
        Log [dev-1619461926372-2886358]: [INFO] [DEPLOYMENT] locked balance for 10K contract storage
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] Deposit(YoctoNear(1088320000000000000000000))
        Log [dev-1619461926372-2886358]: [INFO] [DEPLOYMENT] owner balance = 1088320000000000000000000
        Log [dev-1619461926372-2886358]: [INFO] [DEPLOYMENT] FungibleTokenComponent {
  "spec": "ft-1.0.0",
  "name": "STAKE",
  "symbol": "PEARL3",
  "decimals": 24,
  "icon": null,
  "reference": null,
  "reference_hash": null
}
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] StorageUsageChange(97)
        Log [dev-1619461926372-2886358]: [INFO] [ACCOUNT_STORAGE_CHANGED] Registered(StorageBalance { total: YoctoNear(3930000000000000000000), available: YoctoNear(0) })
        Log [dev-1619461926372-2886358]: [INFO] [DEPLOYMENT] StakingPoolComponent
Receipt: Fi9DoMaxtKfyWg4couRP8WCtytr48RCPGjLcjD28Z6P5
        Log [dev-1619461926372-2886358]: [INFO] [STAKE_POOL_DEPLOY_SUCCESS] 
Transaction Id Yhrda9sutT4jhGMpwTuUZCZYm5vfH8RLCkvJzgnZj28
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/Yhrda9sutT4jhGMpwTuUZCZYm5vfH8RLCkvJzgnZj28

```

> Log records use the following standard format: `[LOG_LEVEL] [EVENT] msg`
> 
> where LOG_LEVEL -> INFO | WARN | ERR

The logs tell the story about what's happening during the deployment, which is illustrated in the below diagram:
![](../../../../.gitbook/assets/oysterpack-smart-stake-factory-deploy.png)

#### Notes
- **AccountManagementComponent** is designed to track and log (**ACCOUNT_STORAGE_CHANGED**) all account storage changes
- The STAKE pool contract implements the NEAR standard [account storage management][8] interface. It measures dynamically
  how much account storage is required by the contract and configures the minimum storage usage bound accordingly.
- **AccountManagementComponent** is also designed to track all contract NEAR balances including account storage balances.
- The **ContractOperator** interface provides the ability to lock a portion of the contract account balance to ensure it 
  cannot be transferred out. This feature is used to lock enough account balance to pay for 10K of contract storage that 
  is reserved for the STAKE pool to operational.
- The **STAKE Factory Contract** is designed to create and initialize the STAKE Pool contract using NEAR's batch transaction 
  feature. This guarantees that either all actions in the batch transaction succeed or fail atomically. If the batch transaction 
  fails for any reason, the factory contract is designed to refund the attached deposit.
- In the above NEAR CLI example, the owner account was specified implicitly using the predecessor account. However note 
  that the factory deploy function supports an optional `owner` argument that can be used to specify the owner account explicitly. 
- Fees are specified in basis points ([BPS][5]). An easy way to remember the unit conversion is 100 BPS = 1%. In the above
  example, the staking fee is 0.01%, and the earnings fee is 0.5%. At least one of the fees must be non-zero and the max fee
  is currently hard coded to be 1000 BPS (10%). Fees are configurable and can be changed after the STAKE pool is deployed
  by accounts that have the operator permission.
- The **STAKE Pool Contract** implements the NEAR standard [fungible token][9] interfaces for the provided STAKE token.

### How to operate the STAKE pool contract

![](../../../../.gitbook/assets/oysterpack-smart-stake-operator-usecases.png)

The above diagram shows the role and responsibilities for the operator. In this tutorial, I will review the key APIs to be 
familiar with to get started. The rest is out of scope and will be covered in future tutorials and workshops.

### Starting / Stopping Staking
When the STAKE pool contract is deployed, it's initial status is offline. You can check the pool status using the following 
NEAR CLI command:

```shell
STAKE=pearl
near view  $STAKE.stake-v1.oysterpack.testnet ops_stake_status
```
The status results look like:
```shell
{ Offline: 'Stopped' }

'Online'
```
If the pool is offline, then it also displayes the offline reason. There are 2 reason why the pool would be offline:
- Stopped - means the pool was explicitly stopped an operator
- StakeActionFailed - means that a NEAR stake action failed, which will take the pool offline automatically

Staking can be started and stopped using the following NEAR CLI commands:
```shell
near call  $STAKE.stake-v1.oysterpack.testnet ops_stake_operator_command --args '{"command":"StartStaking"}' --accountId $NEAR_ACCOUNT

near call  $STAKE.stake-v1.oysterpack.testnet ops_stake_operator_command --args '{"command":"StopStaking"}' --accountId $NEAR_ACCOUNT
```

Current fees can be queried via:
```shell
near view  $STAKE.stake-v1.oysterpack.testnet ops_stake_fees
```
which will return output like:
```text
{ staking_fee: 80, earnings_fee: 0 }
```

Fees can be changed with the following NEAR CLI command:
```shell
near call  $STAKE.stake-v1.oysterpack.testnet ops_stake_operator_command --args '{"command":{"UpdateFees":{"staking_fee":1,"earnings_fee":50}}}' --accountId $NEAR_ACCOUNT
```
- both fees must be specified and at least 1 must be non-zero

The staking public key, i.e., the validator key, can be viewed and changed using the following NEAR CLI commands:
```shell
near view  $STAKE.stake-v1.oysterpack.testnet ops_stake_public_key

near call  $STAKE.stake-v1.oysterpack.testnet ops_stake_operator_command --args '{"command":{"UpdatePublicKey":"ed25519:GTi3gtSio5ZYYKTT8WVovqJEob6KqdmkTi8KqGSfwqdm"}}' --accountId $NEAR_ACCOUNT
```

## How to Get Started as a Staker


[1]: https://learn.figment.io/network-documentation/near/tutorials/1-project_overview/7-stake-vision
[2]: https://github.com/oysterpack/oysterpack-smart
[3]: https://github.com/near/core-contracts/tree/master/staking-pool
[4]: https://docs.near.org/docs/validator/staking
[5]: https://www.investopedia.com/terms/b/basispoint.asp
[6]: https://datahub.figment.io/
[7]: https://learn.figment.io/network-documentation/near/tutorials/intro-pathway-write-and-deploy-your-first-near-smart-contract/1.-connecting-to-a-near-node-using-datahub#configure-environment
[8]: https://nomicon.io/Standards/StorageManagement.html
[9]: https://nomicon.io/Standards/FungibleToken/README.html