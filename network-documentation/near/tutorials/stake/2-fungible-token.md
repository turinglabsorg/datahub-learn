# Show Me the Money: The New and Improved Fungible Token Standard (NEP-141) Has Arrived

## _"Money makes the world go round."_
The simplest way to explain fungible tokens is money. If you know money then you already know fungible tokens. NEAR is an
example of a fungible token, i.e., a crypto**CURRENCY**. How does money make the world go round? The answer is simple ... 
because money enables the ease of flow of capital and trade. Flowing capital and trade results in a healthy economy that 
results in wealth creation and prosperity. The opposite happens when capital flow is hampered. Cryptocurrency is the latest 
technological breakthrough for money. Bitcoin was the first successful proof of concept for cryptocurrency. Bitcoin 
is a fantastic store of value, but it simply cannot scale to meet the demands of today's global economy - or local economy 
for that matter. 

This is where NEAR can step in and bridge the gap. NEAR shines in terms of speed, scalability, and cost. NEAR's first
attempt to bootstrap the ecosystem with an FT (**F**ungible **T**oken) standard was [NEP-21](https://nomicon.io/Standards/Tokens/FungibleToken.html).
NEP-21 was designed as a port of Ethereum's first attempt [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) 
token standard. However, after living in the wild for a while, ERC-20 problems were exposed:
- [What's Wrong With ERC-20 Token](https://ihodl.com/analytics/2018-10-04/whats-wrong-erc-20-token/)
- [Critical problems of ERC20 token standard](https://medium.com/@dexaran820/erc20-token-standard-critical-problems-3c10fd48657b)

The bottom line always comes down to money. The key to NEAR's success will be its ability to attract capital flows into
NEAR through tokens. We need to come together as a community to design and build the best token solution and experience 
on NEAR.

> Hindsight is 20/20

Long story short, NEAR can do much better than ERC-20 / NEP-21. The NEAR community has been coming together to design the 
next generation token standards that are needed to make the dream a reality. The first result of that collective effort 
has produced [Fungible Token Core Standard NEP-141](https://github.com/near/NEPs/discussions/146). Yours truly has been 
actively involved in the process, and in this tutorial lesson I will do a deep dive into [NEP-141]((https://github.com/near/NEPs/discussions/146))
and walk you through the new API. The discussion had been lengthy, and my goal here is to sum it all up for you here.
- **NOTE**: as of this writing, NEP-141 official documentation has not yet been published. I will be sharing with you my 
  knowledge and understanding of NEP-141 based on the discussions and meetings I have personally been involved in. I invite
  you to join in on the discussion.

# Fungible Token Core Standard (NEP-141)
NEP-141 defines the "core" standard API that centers around token transfer. It is not the full and final FT solution. 
It is the core, but at a minimum, NEP-141 will be dependent on 2 future standards to be developed:
1. account registration
2. contract metadata

In addition, standards need to be defined to support token burning and minting. That falls outside the scope of this discussion.
The overall standard design strategy was to use a divide and conquer approach. Instead of designing a single huge standard
that covers all use cases and edge cases, the strategy is to divide the standard into multiple smaller and separate APIs 
following a separation of concerns philosophy. This will enable token functionality and tooling to be rolled out using a
phased approach.

It probably is obvious why standardizing contract metadata is needed:
- makes it simpler and smoother for wallet and application integration
- used for presentation
- used to discover supported extensions and interfaces
  
What is less obvious and what I will dive a little deeper into is how account registration fits into the picture ...

### What does account registration have to do with fungible tokens?
This is a trick question. The key issue that account registration addresses is not specific to FT contracts. Account
registration is needed to address who will pay for [storage staking](https://docs.near.org/docs/concepts/storage#how-much-does-it-cost). 
This is an issue that needs to be addressed by any multi-user contract that allocates storage for the user on the blockchain.
Storage staking costs are the most expensive costs to consider for the contract. If storage costs are not managed properly,
then they can [break the bank](https://docs.near.org/docs/concepts/storage#the-million-cheap-data-additions-attack) for 
the contract.

On NEAR, the contract is responsible to pay for its long term persistent storage. Thus, FT contracts, and multi-user 
contracts in general, should be designed to pass on storage costs to its user accounts: 
- contracts should require accounts to pay for their own account storage when the account registers with the contract. 
- contracts should escrow the storage staking fees and refund the fees when the account unregisters. 

> There's no free lunch ... everyone must pay for their own way

**NOTE**: account registration has other benefits, but we can save that for the 
[account registration standard discussion](https://github.com/near/NEPs/discussions/145)
  
## What Does the FT Core Standard Support?
![](../../../../.gitbook/assets/oysterpack-near-stake-token-nep-41.png)
... a picture says a thousand words ... FT core functionality provides:
- simple transfers
- reactive transfer calls to contract recipient with support for refunds
- account token balance queries
- token total supply queries

### NOTES
- the API functions are specified using the lowest common denominator with the goal of being programming language 
neutral (as much as possible)
- string type is used as the de facto platform neutral type - but we will be leveraging Rust's type system in the smart
contract implementation
- all FT API functions are namespaced using a prefix naming convention (`ft_`). On NEAR, smart contracts are deployed
as [WASM](https://webassembly.org/) binaries. At the WASM low level, all functions are effectively in the global namespace 
and function names must be unique (function overloading is not permitted). Using function prefix names is a trade-off. 
It enables global functions to be namespaced using a naming convention to avoid function name collisions. For example, a
contract may want to implement both FT and NFT token transfer interfaces. 
- `#[payable]` implies that the function supports NEAR to be attached to the function call. I will explain why this is 
needed below
  
## Fungible Token API
This API is implemented by the FT contract. It provides the core fungible token functionality. Key aspects to call out are:

#### Token transfer functions require exactly 1 yoctoNEAR to be attached to address the following security concern:
> Due to the nature of function-call permission access keys on NEAR protocol, the method that requires an attached deposit
can't be called by the restricted access key. If the token contract requires an attached deposit of at least 1
yoctoNEAR on transfer methods, then the function-call restricted access key will not be able to call them without going
through the wallet confirmation. This prevents some attacks like fishing through an authorization to a token contract.

#### Token transfers support optional memo
> Similarly to bank transfer and payment orders, the memo argument enables to reference the transfer to another event 
(on-chain or off-chain). It is a schema less, so user can use it to reference an external document, invoice, order ID, 
ticket ID, or other on-chain transaction. With memo you can set a transfer reason, often required for compliance.
This is also useful and very convenient for implementing FATA (Financial Action Task Force) 
[guidelines](http://www.fatf-gafi.org/media/fatf/documents/recommendations/RBA-VA-VASPs.pdf) (section 7(b)). 
Especially a requirement for VASPs (Virtual Asset Service Providers) to collect and transfer customer information during 
transactions. VASP is any entity which provides to a user token custody, management, exchange or investment services.
With ERC-20 (and NEP-21) it is not possible to do it in atomic way. With memo field, we can provide such reference in 
the same transaction and atomically bind it to the money transfer.

### Functions
```javascript
#[payable]
function ft_transfer(receiver_id: string, amount: string, memo: string|null)
```
_change method_

Enables simple transfer between accounts.
- transfers specified `amount` of tokens from the function call's predecessor account to the account specified by `receiver_id`.
  - the predecessor account is the token sender
- both accounts must be registered with the contract for transfer to succeed.
- sender account is required to attach exactly 1 yoctoNEAR to the function call
  - the purpose to require 1 yoctoNEAR is to address the following security concern explained above
  - attached yoctoNEAR will be credited to the sender account. Most FT contracts will likely deposit the NEAR into the 
    account's storage escrow. However, the NEAR can be deposited using a different approach as long as the NEAR can be 
    made available to be withdrawn from the contract. Even though it's only 1 yoctoNEAR, it's still not **zero** yoctoNEAR.

Arguments:
- `receiver_id` - the receiver NEAR account ID
- `amount` - the amount of tokens to transfer. Amount must be a positive number in decimal string representation.
- `memo` - an optional string field in a free form to associate a memo with this transfer.

Failures:
- if the attached deposit does not equal 1 yoctoNEAR
- if either sender or receiver accounts are not registered
- if transfer `amount` is zero
- if the sender account has insufficient funds to fulfill the transfer request

```javascript
#[payable]
function ft_transfer_call(receiver_id: string, amount: string, msg: string, memo: string|null):  Promise
```

## Upcoming ...
As a reference implementation, I will show you how I implemented NEP-41 for the [STAKE token](https://github.com/oysterpack/oysterpack-near-stake-token).
  