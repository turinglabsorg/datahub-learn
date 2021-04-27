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


[1]: https://learn.figment.io/network-documentation/near/tutorials/1-project_overview/7-stake-vision
[2]: https://github.com/oysterpack/oysterpack-smart
[3]: https://github.com/near/core-contracts/tree/master/staking-pool