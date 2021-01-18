# NFT Marketplace tutorial

## Introduction

NFTs are digital items that are unique and have provable ownerships on the blockchain. One of the biggest NFTs right now is the Digital Artwork. Projects in Ethereum such as [SuperRare](https://superrare.co) leverage the power of blockchain to create digital artwork that has digital scarcity and true ownership, thus creating a whole new market for artists and collectors in the digital space.

In NEAR, one NFT Marketplace that is already available to use is [Paras: Digital Art Card](https://paras.id) where the artwork minting fees are much cheaper than the one on the ethereum, enabling artists to create more artworks without costing them lots of money.

In this tutorial we will be creating a simple NFT Marketplace similar to [Paras](https://paras.id) and [SuperRare](https://superrare.co) where artists can mint their artworks and sell them directly to collectors. We will be using [NEP4](https://github.com/near/NEPs/pull/4) NFT standard which is based on [ERC721](https://eips.ethereum.org/EIPS/eip-721).

### Prerequisites:

This tutorial requires:

- Install Node.js and NPM [(see Tutorial 1)](https://learn.figment.io/network-documentation/near/tutorials/1.-connecting-to-a-near-node-using-datahub)
- Install the NEAR CLI [(see Tutorial 2)](https://learn.figment.io/network-documentation/near/tutorials/2.-creating-your-first-near-account-using-the-sdk)
- Complete the first NEAR smart contract tutorial [(see Tutorial 5)](https://github.com/figment-networks/datahub-learn/blob/138770526e63fc79c32a60d61650479886cfad06/network-documentation/near/tutorials/5.-writing-and-deploying-your-first-near-smart-contract.md)

## Installing Yarn

If you haven't already, we need to install the `yarn` package manager. The example code we're working with uses `yarn` as its build tool. Run this command to install `yarn`:

```
npm i -g yarn
```

If that all worked, you're ready to develop smart contracts in Rust.

### Cloning the NEAR NFT repo

In this tutorial we'll use the forked NEAR's NFT example code on Github. On Unix, run these commands in the `bash` shell to clone that repo and install its requirements:

```
git clone https://github.com/hdriqi/NFT
cd NFT
yarn install
```

This repo contains NFT examples in both AssemblyScript and Rust, plus support files and documentation. We'll be ignoring Rust and jump straight into AssemblyScript. All the files we need for our smart contract live in the subdirectory `contracts/assemblyscript`

### Run and Test

We can also run all of the included unit tests with this command:

```
yarn test:unit:as
```

The unit test output is messy, but at the end you should see a summary of results.

```
[Result]: ✔ PASS
[Files]: 1 total
[Groups]: 8 count, 8 pass
[Tests]: 13 pass, 0 fail, 13 total
```

If you see something like that then we're good to go.

## Getting to know the NEP4 Contract

Our marketplace will be based on the NEP4 Contract, the first thing we need to do is to understand the base contract and expand it into marketplace. The smart contract that we will modify is at `contracts/assemblyscript/nep4-basic/main.ts`. Open the file and we'll run through the code together.

### Data Types & Storage

There are many built-in data storages that can be used on NEAR. 

```typescript
/**************************/
/* DATA TYPES AND STORAGE */
/**************************/

type AccountId = string
type TokenId = u64

// Note that MAX_SUPPLY is implemented here as a simple constant
// It is exported only to facilitate unit testing
export const MAX_SUPPLY = u64(10)

// The strings used to index variables in storage can be any string
// Let's set them to single characters to save storage space
const tokenToOwner = new PersistentMap<TokenId, AccountId>('a')

// Note that with this implementation, an account can only set one escrow at a
// time. You could make values an array of AccountIds if you need to, but this
// complicates the code and costs more in storage rent.
const escrowAccess = new PersistentMap<AccountId, AccountId>('b')

// This is a key in storage used to track the current minted supply
const TOTAL_SUPPLY = 'c'
```

### Change Methods

These change methods are the ones that mutate the blockchain state. The code itself is self-explanatory and pretty similar to the `ERC721` standard. One thing to note is the `grant_access` function which allows other accounts including smart contracts to have access to your account (usually used to transfer tokens on your behalf).

```typescript
/******************/
/* CHANGE METHODS */
/******************/

// Grant access to the given `accountId` for all tokens the caller has
export function grant_access(escrow_account_id: string): void {
	escrowAccess.set(context.predecessor, escrow_account_id)
}

// Revoke access to the given `accountId` for all tokens the caller has
export function revoke_access(escrow_account_id: string): void {
	escrowAccess.delete(context.predecessor)
}

// Transfer the given `token_id` to the given `new_owner_id`. Account `new_owner_id` becomes the new owner.
// Requirements:
// * The caller of the function (`predecessor`) should have access to the token.
export function transfer_from(
	owner_id: string,
	new_owner_id: string,
	token_id: TokenId
): void {
	const predecessor = context.predecessor

	// fetch token owner and escrow; assert access
	const owner = tokenToOwner.getSome(token_id)
	assert(owner == owner_id, ERROR_OWNER_ID_DOES_NOT_MATCH_EXPECTATION)
	const escrow = escrowAccess.get(owner)
	assert(
		[owner, escrow].includes(predecessor),
		ERROR_CALLER_ID_DOES_NOT_MATCH_EXPECTATION
	)

	// assign new owner to token
	tokenToOwner.set(token_id, new_owner_id)
}

// Transfer the given `token_id` to the given `new_owner_id`. Account `new_owner_id` becomes the new owner.
// Requirements:
// * The caller of the function (`predecessor`) should be the owner of the token. Callers who have
// escrow access should use transfer_from.
export function transfer(new_owner_id: string, token_id: TokenId): void {
	const predecessor = context.predecessor

	// fetch token owner and escrow; assert access
	const owner = tokenToOwner.getSome(token_id)
	assert(owner == predecessor, ERROR_TOKEN_NOT_OWNED_BY_CALLER)

	// assign new owner to token
	tokenToOwner.set(token_id, new_owner_id)
}
```

### View Methods

View methods are the get functionality. It does not cost any gas fee to call but they cannot mutate the blockchain state.

```typescript
/****************/
/* VIEW METHODS */
/****************/

// Returns `true` or `false` based on caller of the function (`predecessor`) having access to account_id's tokens
export function check_access(account_id: string): boolean {
	const caller = context.predecessor

	// throw error if someone tries to check if they have escrow access to their own account;
	// not part of the spec, but an edge case that deserves thoughtful handling
	assert(caller != account_id, ERROR_CALLER_ID_DOES_NOT_MATCH_EXPECTATION)

	// if we haven't set an escrow yet, then caller does not have access to account_id
	if (!escrowAccess.contains(account_id)) {
		return false
	}

	const escrow = escrowAccess.getSome(account_id)
	return escrow == caller
}

// Get an individual owner by given `tokenId`
export function get_token_owner(token_id: TokenId): string {
	return tokenToOwner.getSome(token_id)
}
```

### Minting

Now here's the main function that allow users to mint the NFT. The NFT itself is just a simple ID with owner. The metadata such as image, video or audio is usually stored off-chain on [IPFS], [Sia] or even a centralized file storage such as [AWS S3]. Unlike Ethereum, storing data on NEAR is pretty cheap; you can actually store the whole metadata on chain but it will not be covered in this tutorial, you can try it yourself!

[Paras](https://paras.id) is not storing any metadata on-chain, instead they use an IPFS Hash as the Token ID. There are many designs that you can experiment with when building your NFTs.

```typescript
export function mint_to(owner_id: AccountId): u64 {
	// Fetch the next tokenId, using a simple indexing strategy that matches IDs
	// to current supply, defaulting the first token to ID=1
	//
	// * If your implementation allows deleting tokens, this strategy will not work!
	// * To verify uniqueness, you could make IDs hashes of the data that makes tokens
	//   special; see https://twitter.com/DennisonBertram/status/1264198473936764935
	const tokenId = storage.getPrimitive<u64>(TOTAL_SUPPLY, 1)

	// enforce token limits – not part of the spec but important!
	assert(tokenId <= MAX_SUPPLY, ERROR_MAXIMUM_TOKEN_LIMIT_REACHED)

	// assign ownership
	tokenToOwner.set(tokenId, owner_id)

	// increment and store the next tokenId
	storage.set<u64>(TOTAL_SUPPLY, tokenId + 1)

	// return the tokenId – while typical change methods cannot return data, this
	// is handy for unit tests
	return tokenId
}
```

## Marketplace

We are done with the basic NFT contract; users can mint their NFTs. We're going to focus on the next step, creating the marketplace where the NFT Owner can put up the price and other users that are interested in it can buy it with NEAR coin. We'll be adding unit tests after each function/feature that we build to make sure it works as expected before we deploy it on the blockchain.

### Unit test

Unit tests can be found in the **tests** folder. `As-pect` was used for unit tests in AssemblyScript. You can use `VMContext` to mock some stuff on the NEAR runtime for testing functionality like contract caller, attached deposit, etc.

You can run the tests using:

```
yarn test:unit:as
```

We will be adding more features one by one to the basic NFT contract from NEP-4 and create unit test for every function that we create.

### Data Types & Storage

We need to create a new `PersistentUnorderedMap` that stores the price for all the tokens listed by their owner. We use `u128` as the data types for `Price` because NEAR coin is also in `u128`. You can write it on top of the contract. We use `PersistentUnorderedMap` because we want to create a `key-value` storage that can also be retrieved as a list.

```typescript
type Price = u128

const market = new PersistentUnorderedMap<TokenId, Price>('m')
```

### Add Token to Market

We create a private function called `internal_add_to_market` as the main function to add token and its price to the marketplace. The public function `add_to_market` is basically the wrapper for the `internal_add_to_market` that can be called by users to list their token to the market with validation.

```typescript
export function add_to_market(token_id: TokenId, price: Price): boolean {
	const caller = context.predecessor

	// validate token owner
	const owner = tokenToOwner.getSome(token_id)
	assert(owner == caller, ERROR_TOKEN_NOT_OWNED_BY_CALLER)

	// set the price for sale
	internal_add_to_market(token_id, price)

	return true
}

function internal_add_to_market(token_id: TokenId, price: Price): void {
	market.set(token_id, price)
}
```

Like I mentioned earlier, we'll be adding test after adding new feature for the smart contract. You can add this code at the end of the current unit test. The comment itself is already self-explanatory.

```typescript
describe('add_to_market', () => {
	it('should add nft to market and return true', () => {
		VMContext.setPredecessor_account_id(alice)
		// mint new token that return its id
		const tokenId = nonSpec.mint_to(alice)
		// 1 NEAR
		const price = u128.from('1000000000000000000000000')

		expect(nonSpec.add_to_market(tokenId, price)).toBe(true)
	})

	it('should throw error if called by non owner', () => {
		expect(() => {
			VMContext.setPredecessor_account_id(alice)
			// mint new token that return its id
			const tokenId = nonSpec.mint_to(alice)
			// 1 NEAR
			const price = u128.from('1000000000000000000000000')

			VMContext.setPredecessor_account_id(bob)
			nonSpec.add_to_market(tokenId, price)
		}).toThrow(nonSpec.ERROR_TOKEN_NOT_OWNED_BY_CALLER)
	})
})
```

After creating the market we need to have function fetch the price. We create simple get function `get_market_price` that takes `token_id` and return its price.

```typescript
export function get_market_price(token_id: TokenId): Price {
	return market.getSome(token_id)
}
```

Again, we need to test whether our function works as expected.

```typescript
describe('get_market_price', () => {
	it('return market price for a token', () => {
		VMContext.setPredecessor_account_id(alice)
		// mint new token that return its id
		const tokenId = nonSpec.mint_to(alice)
		// set price to be 1 NEAR
		const price = u128.from('1000000000000000000000000')
		nonSpec.add_to_market(tokenId, price)
		// get the first 5 NFTs listed on the market
		expect(nonSpec.get_market(0, 5)).toHaveLength(1)
	})
})
```

### Remove Token from Market

Removing token from market is pretty much like the previous one. We have private `internal_remove_from_market` that delete the token listing from market and public `remove_from_market` that validate the token ownership and whether the token is in the market or not.

```typescript
export function remove_from_market(token_id: TokenId): boolean {
	const caller = context.predecessor

	// validate token owner
	const owner = tokenToOwner.getSome(token_id)
	assert(owner == caller, ERROR_TOKEN_NOT_OWNED_BY_CALLER)

	assert(market.getSome(token_id), ERROR_TOKEN_NOT_IN_MARKET)

	// remove token from market
	internal_remove_from_market(token_id)

	return true
}

function internal_remove_from_market(token_id: TokenId): void {
	market.delete(token_id)
}
```

Here's the test case that we create for market delete functionality.

```typescript
describe('remove_from_market', () => {
	it('should remove nft from market and return true', () => {
		VMContext.setPredecessor_account_id(alice)
		// mint new token that return its id
		const tokenId = nonSpec.mint_to(alice)
		// 1 NEAR
		const price = u128.from('1000000000000000000000000')
		// add token to market
		expect(nonSpec.add_to_market(tokenId, price)).toBe(true)
		// remove token from market
		expect(nonSpec.remove_from_market(tokenId)).toBe(true)
	})

	it('should throw error for nft available in market', () => {
		expect(() => {
			VMContext.setPredecessor_account_id(alice)
			// mint new token that return its id
			const tokenId = nonSpec.mint_to(alice)
			// throw error when try to remove token id that was not listed
			nonSpec.remove_from_market(tokenId)
		}).toThrow(nonSpec.ERROR_TOKEN_NOT_IN_MARKET)
	})
})
```

### Buy Functionality

This is the main function for the marketplace where users can buy the listed NFTs on the marketplace. Buyer can attach some NEAR as the payment and retrived by `context.attachedDeposit`. The next step is to validate the deposit amount to match the NFT price.

We also create a simple transaction fee or commission for smart contract developer, in this example we take 5% cut for every sales made via the smart contract.

We can transfer the payment to the account by using `ContractPromiseBatch.create(accountId).transfer(amount)`. The first promise `.create` is to create the received account and chained by `.transfer` to transfer the payment amount.

After the payment have been made, we need to remove it from the sales and update the ownership of the token to the buyer. In the end we just return the `tokenId`.

```typescript
const COMMISSION = 5

export function buy(token_id: TokenId): TokenId {
	const caller = context.predecessor

	const amount = context.attachedDeposit
	const price = market.getSome(token_id)

	// check if the amount deposited match with the price
	assert(amount == price, ERROR_DEPOSIT_NOT_MATCH)

	// calculate commission for the smart contract
	const owner = tokenToOwner.getSome(token_id)
	const forOwner: u128 = u128.div(
		u128.mul(amount, u128.from(100 - COMMISSION)),
		u128.from(100)
	)
	const contract = context.contractName
	const forContract: u128 = u128.sub(amount, forOwner)

	// tranfer the deposit to token owner and smart contract
	ContractPromiseBatch.create(owner).transfer(forOwner)
	ContractPromiseBatch.create(contract).transfer(forContract)

	// remove the token from the market
	internal_remove_from_market(token_id)

	// update the token owner
	tokenToOwner.set(token_id, caller)

	return token_id
}
```

As you can see, we call the private `internal_remove_from_market` function so that this `Buy` function can remove the token from market without any validation.

Below is the unit test for the `Buy` function

```typescript
describe('buy', () => {
	it('transfer token and remove it from market', () => {
		VMContext.setPredecessor_account_id(alice)
		// mint new token that return its id
		const tokenId = nonSpec.mint_to(alice)
		// set price to be 1 NEAR
		const price = u128.from('1000000000000000000000000')
		nonSpec.add_to_market(tokenId, price)
		// mock the Buyer id & the deposit amount
		VMContext.setPredecessor_account_id(bob)
		VMContext.setAttached_deposit(price)
		// Buyer (bob) will call this buy function
		nonSpec.buy(tokenId)
		// after successful purchase the owner of the NFT must be bob
		expect(get_token_owner(tokenId)).toBe(bob)
	})
})
```

### Get all the token from marketplace

First create struct `TokenDetail` and add decorator `nearBindgen` for serialize/deserialize the struct in the NEAR runtime, think of it as the required syntax for every struct to run on NEAR protocol. 

The function will return list of `TokenDetail` via view function `get_market`. We use `.entries` from `PersistentUnorderedMap` that takes start and end index from our list, we can use this for pagination later when building the frontend application.

```typescript
@nearBindgen
export class TokenDetail {
	tokenId: TokenId
	price: Price

	constructor(tokenId: TokenId, price: Price) {
		this.tokenId = tokenId
		this.price = price
	}
}

export function get_market(start: i32, end: i32): TokenDetail[] {
	const results: TokenDetail[] = []

	const tokenList = market.entries(start, end)
	for (let i = 0; i < tokenList.length; i++) {
		results.push(new TokenDetail(tokenList[i].key, tokenList[i].value))
	}

	return results
}
```

Let's create the test and see if it's works as expected. We mint 3 new NFTs and add all of it the market, we expect the market to have all 3 listed NFTs.

```typescript
describe('get_market_list', () => {
	it('return market price for a token', () => {
		VMContext.setPredecessor_account_id(alice)

		const price = u128.from('1000000000000000000000000')
		// mint new token that return its id
		const tokenId_1 = nonSpec.mint_to(alice)
		nonSpec.add_to_market(tokenId_1, price)

		const tokenId_2 = nonSpec.mint_to(alice)
		nonSpec.add_to_market(tokenId_2, price)

		const tokenId_3 = nonSpec.mint_to(alice)
		nonSpec.add_to_market(tokenId_3, price)
		// set price to be 1 NEAR
		// get the first 5 NFTs listed on the market
		expect(nonSpec.get_market(0, 5)).toHaveLength(3)
	})
})
```

### Build the contract

Before we build the contract, let's test the newly added code and see if everything goes well.

```
yarn test:unit:as
```

If it returns something like this then we're good to go. If not, make sure you didn't have any typos and use the latest `near-sdk-as`.

```bash
[Result]: ✔ PASS
[Files]: 1 total
[Groups]: 13 count, 13 pass
[]Tests]: 20 pass, 0 fail, 20 total
```

To build our contract into WebAssembly, we simply use the command below:

```
yarn build:as
```

If you have made any errors, the compiler should give you a detailed explanation. Since you've copied and pasted everything from this tutorial, it's probably just typos. 

You can also see the complete code for this tutorial on branch [feat/marketplace](https://github.com/hdriqi/NFT/tree/feat/marketplace).

## Deploying and using the contract

We can use the NEAR CLI to deploy this contract, and to test that it's working. Run this command to deploy the contract you just built:

```
near dev-deploy out/main.wasm
```

The output will show details of the deployment transaction, and the ID of the test NEAR account that the CLI auto-generated for you. It should look something like this:

```
Starting deployment. Account id: dev-1610108148519-8579413, node: https://rpc.testnet.near.org, helper: https://helper.testnet.near.org, file: out/main.wasm
Transaction Id HiGAnagXLY7TaCzmLRBCSzgDPWjSFifaLTQsfARmj1Qy
To see the transaction in the transaction explorer, please open this url in your browser
https://explorer.testnet.near.org/transactions/HiGAnagXLY7TaCzmLRBCSzgDPWjSFifaLTQsfARmj1Qy
Done deploying to dev-1610108148519-8579413
```

Unlike Ethereum, you need to deploy contract into certain account, using `dev-deploy` will create a test account and deploy the contract into that account at once. The test account looks something like `dev-xxxxxxxxxxx-xxxxx`.

From the command above we learn that we create an account with ID `dev-1610108148519-8579413` in which we just deployed the smart contract in it. So the smart contract ID is basically the account ID which is `dev-1610108148519-8579413`. You'll have different ID, so remember to replace these contract ID with yours when following the command below. 

## Interact with Testnet Blockchain

We can use NEAR CLI to interact with the blockchain, there are 2 main commands to `call` and `view`. Call is used to mutate blockchain state and view is only to get data from blockchain. 

First, we need to authenticate few accounts into the NEAR CLI. Go to `https://wallet.testnet.near.org/` and create 2 accounts, you can name it anything you want but in this tutorial we'll refer them as `artist` and `collector`. After that, go back to our terminal and use this command:

```bash
near login
```

It will open your default browser and choose one of the account that you've just created to authenticate it on our terminal. Do it twice so that your 2 new accounts are authenticated on the NEAR CLI. If your browser doesn't automatically open, you can just follow the instruction when using `near login`.

To check whether those accounts already authenticated on our computer, we can use this command:

```bash
ls ~/.near-credentials/default/
```

If you saw at least 2 files with `[account_id].json` similar to the one that you've created on the browser, then we're good to go. Without further ado, let's try try some function that we already created with these new accounts.

### Mint NFT!

We need to use call method to mint new NFT because it will mutate the blockchain state. We will use the function `mint_to` to create mint new NFT and assign its ownership to somebody. You can use the command below:

```bash
near call --accountId [ARTIST_ID] dev-1610108148519-8579413 mint_to '{"owner_id": "[ARTIST_ID]"}'
```

Make sure to change `[ARTIST-ID]` to one of the accounts that you've created before. That command will return the tokenId of the newly minted NFT:

```
'1'
```

### Add to Marketplace

We can add our newly minted NFT to the marketplace to see if there are collectors that are interested to add it into their collections. We can use the function `add_to_market` to do that which takes `token_id` and `price` as parameters. The price must be in yoctoNEAR (10^24), in this case we sell our NFT for 1 N -> 1000000000000000000000000 yoctoNEAR. Make sure to change the parameters `[TOKEN_ID]` into any NFT ID that you own/mint.

```bash
near call --accountId [ARTIST_ID] dev-1610108148519-8579413 add_to_market '{"token_id": "[TOKEN_ID]", "price": "1000000000000000000000000"}'
```

We can validate if our NFT is listed on the marketplace as expected using the view function `get_market_price` that takes `token_id` as the parameter.

```bash
near view --accountId [ARTIST-ID] dev-1610108148519-8579413 get_market_price '{"token_id": "[TOKEN_ID]"}'
```

It should returns the price that we've just set previously using `add_to_market` which is 1 N.

### Buy the NFT

This time, we'll be using another account beside the artist and let's call it the collector `COLLECTOR_ID`. Collector can buy the listed NFT by calling the function `buy` that require `[TOKEN_ID]` as its parameter. This time we add `--amount` to buy the NFT, it is usually called attachedDeposit and we attach 1 N as it is the listed price needed to buy the NFT.

Before we buy the NFT, let's just take a note at the current balance of the artist and collector using NEAR CLI command below:

```bash
near state [ACCOUNT_ID]
```

You can replace the `[ACCOUNT_ID]` with `[ARTIST_ID]` and `[COLLECTOR_ID]`. It will returns something like this:
```
{
  amount: '88126613751512027823390000',
  locked: '0',
  code_hash: '11111111111111111111111111111111',
  storage_usage: 2844,
  storage_paid_at: 0,
  block_height: 32018936,
  block_hash: 'Ffc1kw56xpCV1fPJVHNEtfbDbfPF4wkhQNbvbUbXpH5M',
  formattedAmount: '88.12661375151202782339'
}
```

The formattedAmount is current balance of the account, so just keep that in mind and we'll compare with after buying process. We call the `buy` function with `[COLLECTOR_ID]`.

```bash
near call --accountId [COLLECTOR_ID] dev-1610108148519-8579413 buy '{"token_id": "[TOKEN_ID]"}' --amount 1
```

If everything goes well, we can validate the ownership and balance change. 

```bash
near view --accountId [COLLECTOR_ID] dev-1610108148519-8579413 get_token_owner '{"token_id": "[TOKEN_ID]"}'
```

We can call `near state [ACCOUNT_ID]` to check if the payment is already received by the seller/artist and deducted from buyer balance.

## Conclusion

Alright, so we have deployed the modified NFT smart contract with Marketplace functionality on the testnet. You can do more experiment by calling other function like `delete_from_market` and so on. You can also connect this contract with javascript sdk `near-api-js` and create the frontend side for your marketplace.

Please remember that this code is not intended for production, there are still few other things to consider if you wanted to deploy it on mainnet.
