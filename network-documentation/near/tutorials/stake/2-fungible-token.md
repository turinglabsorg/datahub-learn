# The New and Improved NEAR Fungible Token Standard (NEP-141) Has Arrived

> "Money makes the world go round."

The simplest way to think of fungible tokens is to think of them as money. How does money make the world go round? The 
answer is simple ... because it enables the flow of capital through the global economy. Bitcoin introduced us all to the 
concept of money on the blockchain. The proof of concept was successful. Bitcoin is a fantastic store of value, but
it simply cannot scale to meet the demands of today's global economy - or local economy for that matter. 

This is where NEAR can step in and bridge the gap. NEAR shines in terms of speed, scalability, and cost. NEAR's first
attempt to bootstrap the ecosystem with an FT (**F**ungible **T**oken) standard was [NEP-21](https://nomicon.io/Standards/Tokens/FungibleToken.html).
NEP-21 was designed as a port of Ethereum's first attempt [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) 
token standard. However, after living in the wild for a while, ERC-20 problems were exposed:
- [What's Wrong With ERC-20 Token](https://ihodl.com/analytics/2018-10-04/whats-wrong-erc-20-token/)
- [Critical problems of ERC20 token standard](https://medium.com/@dexaran820/erc20-token-standard-critical-problems-3c10fd48657b)

> Hindsight is 20/20

Long story short, NEAR can do much better than ERC-20 / NEP-21. The NEAR community is coming together to design the next 
generation token standards that are needed to make the dream a reality. The first token standard produced is 
[Fungible Token Core Standard NEP-141](https://github.com/near/NEPs/discussions/146). Yours truly has been actively 
involved in the process, and in this tutorial I will present the new and improved FT core standard - hence forth NEP-141.
As a reference implementation, I will show you how I implemented NEP-41 for the [STAKE token](https://github.com/oysterpack/oysterpack-near-stake-token).
- NOTE: as of this writing, NEP-141 official documentation has not yet been published. I will be sharing with you my 
  knowledge and understanding of NEP-141 based on the discussions and meetings I have personally been involved in.

# Fungible Token Core Standard (NEP-141)
NEP-141 defines the "core" standard API that centers around token transfer. It is not the full and final solution to the 
problem. At a minimum, NEP-141 will be dependent on 2 future coming standards:
1. account registration
   - prevents tokens being lost by transferring to non-existent accounts
   - prevents to
2. contract metadata
   - makes it simpler for wallet and application integration
   - used for presentation
   - used to discover supported extensions and interfaces

In addition, standards need to be defined to support token burning and minting. That falls outside the scope of this discussion.
The overall standard design strategy was to use a divide and conquer approach. Instead of designing a single huge standard
that covers all use cases and edge cases, the strategy is to divide the standard into multiple smaller and separate APIs 
following a separation of concerns philosophy. This will enable token functionality and tooling to be rolled out using a
phased approach.

It probably is obvious why standardizing contract metadata is needed to make integrations with wallets and applications 
smoother. What is less obvious and what I will dive a little deeper into is how account registration fits into the picture ...

### What does account registration have to do with fungible tokens?
This is a trick question. The key issue that account registration addresses is not specific to FT contracts. Account
registration is needed to address who will pay for [storage staking](https://docs.near.org/docs/concepts/storage#how-much-does-it-cost). 
This is an issue that needs to be addressed by any multi-user contract that allocates storage for the user on the blockchain.
Storage staking costs are the most expensive costs to consider for the contract. If storage costs are not managed properly,
then they can [break the bank](https://docs.near.org/docs/concepts/storage#the-million-cheap-data-additions-attack), or 
contract in this case.

Thus, FT contracts, and multi-user contracts in general, should be designed to require accounts to pay for their own account 
storage when the account registers with the contract. The FT contract should escrow the storage staking fees and refund 
the fees when the account unregisters. "There's no free lunch" and everyone must pay for their own way.
- NOTE: account registration has other benefits, but we can save that for the  
  [account registration standard discussion](https://github.com/near/NEPs/discussions/145)
  
## What Does the FT Core Standard Support?
![](../../../../.gitbook/assets/oysterpack-near-stake-token-nep-41.png)
... a picture says a thousand words ...
- simple transfers
- transfer calls to contract recipient with support for refunds
- account token balance queries
- token total supply queries

### NOTES
- the API functions are specified using the lowest common denominator with the goal of being programming language 
neutral (as much as possible)
- string type is used as the de facto platform neutral type - but we will be leveraging Rust's type system in the smart
contract implementation
  