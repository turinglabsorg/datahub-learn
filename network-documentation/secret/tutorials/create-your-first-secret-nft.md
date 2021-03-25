---
description: This is the first installment of a series about the newly arrived NFT feature on the secret network. In this first tutorial you will learn about the idea of NFTs, the difference between 'classical' and secret NFTs and how to interact with an deployed contract to mint, read and send your NFTs. At the end of this tutorial you will have a good understanding of the concepts and a small toolbox, which serves as a foundation for upcoming articles, to explore NFTs on secret network in greater depth.
---

# Create your first secretNFT

<!-- This is the first installment of a series about the newly arrived NFT feature on the secret network. In this first tutroail you will learn about the idea of NFTs, the difference between 'classical' and secret NFTs and how to interact with an deployed contract to mint, read and send your NFTs. At the end of this tutorial you will have a good understanding of the concepts and a small toolbox, which serves as a foundation for upcoming articles, to explore NFTs on secret network in greater depth. -->

## About the Author

This tutorial was create by [Florian Uhde](https://twitter.com/florianuhde), a software engineer and game developer with a passion for blockchain, creativity and systemic design.

## Introduction

[Non-Fungible Tokens](https://en.wikipedia.org/wiki/Non-fungible_token) implement the idea of uniqueness in the blockchain world. With classical, fungible tokens the only important characteristic is how *many* of them you own. You can think of those as currency, or , to make a more exotic example, carrots. It is interesting to know how many kilograms of carrots you, but no body is going to be really into you telling them which carrots you own precisely. For non-fungible tokens on the other hand, the number you are holding is not as important as *which* token you hold. Given an example: If I tell you that I own three paintings it won't give you any information about my worth as an art collector. If I tell you on the other hand that I own the Mona Lisa it will tell you quite a bit.

On a high level a NFT has a number of important properties. First we are interested in the current owner of a single token, to decide who is allowed to interact with it. Secondly we need a way to store the uniqueness, which is the data that makes this NFT different from all the others on the token.

### What is different between ERC721 and snip721

As mentioned above each NFT contains unique data, or at least a way to identify the unique data. Due to the open ledger nature of most blockchains this means that anyone can read this data. So even if you bought an expensive painting as an NFT no one is stopping me from finding the NFT on a blockchain explorer, having a look at the data and downloading that image for myself.

Secret Network is focussed on privacy by default and this paradigm extends to its implementation of NFT. The snip721 standard mimics the functionality to the corresponding ERC721 standard of the ethereum blockchain, but provides fine control about what information you want to be private, rather than publicly available. When a new snip721 token contract is deployed to the secret network you can define if the total supply and the owners of tokens should be publicly available. Furthermore for each token minted you decide what data is readable by everyone and what data only by the current owner.

## Getting started with secretNFTs

In this section we will connect to an already deployed instance of the [NFT reference implementation](https://github.com/baedrik/snip721-reference-impl): The *SecretFigment token*. We will build the functionality you need to interact with a secret NFT, namely the ability to mint new tokens, to set the viewing key on tokens and to query your private metadata.

### Prerequisites

This article assumes that you have completed the secret pathway already, as we are reusing some of the scripts you wrote back there. If you haven't done so until now you should take a few minutes and go through the pathway. We will start in the same project folder as you left off after section 5.

{% page-ref page="intro-pathway-secret-basics/5.-writing-and-deploying-your-first-secret-contract.md" %}

#### Requirements in this tutorial are

* The latest version of NodeJS
* Any Code editor like VSCode etc.
* Required packages –
  * secretjs - for the Secret Network JavaScript API
  * dotenv - for working with environment variables

You do not need docker and rust *yet*, as we are using a pre-deployed contract for this first tutorial. A future installment will guide you through writing and compiling your own variant of a snip721 token.

### Minting

As we will communicate a number of times with the contract the first think you want to do is adding the contract address to your `.env` file:

```javascript
SECRET_NFT_CONTRACT='secret15gs5mnm8hfw5n3r8602tmy5z736yzv33qsa8q8'
```

Next create a new file in your project folder from the pathway called `mint.js` and paste the following code into it:

```javascript
const {
  EnigmaUtils,
  Secp256k1Pen,
  SigningCosmWasmClient,
  pubkeyToAddress,
  encodeSecp256k1Pubkey,
} = require("secretjs");

// Load environment variables
require("dotenv").config();

const customFees = {
  upload: {
    amount: [{ amount: "2000000", denom: "uscrt" }],
    gas: "2000000",
  },
  init: {
    amount: [{ amount: "500000", denom: "uscrt" }],
    gas: "500000",
  },
  exec: {
    amount: [{ amount: "500000", denom: "uscrt" }],
    gas: "500000",
  },
  send: {
    amount: [{ amount: "80000", denom: "uscrt" }],
    gas: "80000",
  },
};

const main = async () => {
  const httpUrl = process.env.SECRET_REST_URL;

  // Use key created in tutorial #2
  const mnemonic = process.env.MNEMONIC;

  // A pen is the most basic tool you can think of for signing.
  // This wraps a single keypair and allows for signing.
  const signingPen = await Secp256k1Pen.fromMnemonic(mnemonic).catch((err) => {
    throw new Error(`Could not get signing pen: ${err}`);
  });

  // Get the public key
  const pubkey = encodeSecp256k1Pubkey(signingPen.pubkey);

  // get the wallet address
  const accAddress = pubkeyToAddress(pubkey, "secret");

  // initialize client
  const txEncryptionSeed = EnigmaUtils.GenerateNewSeed();

  const client = new SigningCosmWasmClient(
    httpUrl,
    accAddress,
    (signBytes) => signingPen.sign(signBytes),
    txEncryptionSeed,
    customFees
  );
  console.log(`Wallet address=${accAddress}`);

  // 1. Define your meta data

  // 2. Mint a new token to yourself

};

main().catch((err) => {
  console.error(err);
});

```

The first part of this script should look familiar to what you have done section 5 of the pathway. We are getting out account data from the `.env` file and generate a secretjs client from it that we can use further down the line to sign our transactions. The next step is to define the metadata that we want to attach to our nft. In this example we will set two different strings, one will be visible to everyone and another that will be private and only accessible to the owner of the nft. Under  `// 1. Define your meta data` add both declarations and feel free to change the string to whatever you like.

```javascript
  const publicMetadata = "<public metadata>";

  const privateMetadata = "<private metadata>";
```

As a final step we will construct a minting message, fill it with data and send it of to our contract. Paste the snippet below under `// 2. Mint a new token to yourself`.

```javascript
  const handleMsg = {
    mint_nft: {
      owner: accAddress,
      public_metadata: {
        name: publicMetadata,
      },
      private_metadata: {
        name: privateMetadata,
      },
    },
  };

  console.log("Minting yourself a nft");
  const response = await client
    .execute(process.env.SECRET_NFT_CONTRACT, handleMsg)
    .catch((err) => {
      throw new Error(`Could not execute contract: ${err}`);
    });
  console.log("response: ", response);
```

  We define handleMsg to mint a new token for our own address and assign the values we created above as public and private metadata. We pass and execute the object to the contract and read the response. If everything worked like intended you should see something along the lines of:

```javascript
  response:  {
  logs: [ { msg_index: 0, log: '', events: [Array] } ],
  transactionHash: '644332C51EEB404E68B0B73BDEFAD36209A2C78C532DDFF03F83AD3AD68EE5F13',
  data: Uint8Array(256) [
    123, 34, 109, 105, 110, 116,  95, 110, 102, 116, 34, 58,
    123, 34, 116, 111, 107, 101, 110,  95, 105, 100, 34, 58,
     34, 48,  34, 125, 125,  32,  32,  32,  32,  32, 32, 32,
     32, 32,  32,  32,  32,  32,  32,  32,  32,  32, 32, 32,
     32, 32,  32,  32,  32,  32,  32,  32,  32,  32, 32, 32,
     32, 32,  32,  32,  32,  32,  32,  32,  32,  32, 32, 32,
     32, 32,  32,  32,  32,  32,  32,  32,  32,  32, 32, 32,
     32, 32,  32,  32,  32,  32,  32,  32,  32,  32, 32, 32,
     32, 32,  32,  32,
    ... 156 more items
  ]
}
```

Congratulations! You have just minted yourself your first nft on the secret network!

### Querying of your tokens

In this section we want to query information about our token from the contract. For this create a new file called `query_token.js` and paste the following code into it:

```javascript
const {
  EnigmaUtils,
  Secp256k1Pen,
  SigningCosmWasmClient,
  pubkeyToAddress,
  encodeSecp256k1Pubkey,
} = require('secretjs');

// Load environment variables
require('dotenv').config();

const customFees = {
  upload: {
    amount: [{ amount: '2000000', denom: 'uscrt' }],
    gas: '2000000',
  },
  init: {
    amount: [{ amount: '500000', denom: 'uscrt' }],
    gas: '500000',
  },
  exec: {
    amount: [{ amount: '500000', denom: 'uscrt' }],
    gas: '500000',
  },
  send: {
    amount: [{ amount: '80000', denom: 'uscrt' }],
    gas: '80000',
  },
};

const main = async () => {
  const httpUrl = process.env.SECRET_REST_URL;

  // Use key created in tutorial #2
  const mnemonic = process.env.MNEMONIC;

  // A pen is the most basic tool you can think of for signing.
  // This wraps a single keypair and allows for signing.
  const signingPen = await Secp256k1Pen.fromMnemonic(mnemonic).catch((err) => {
    throw new Error(`Could not get signing pen: ${err}`);
  });

  // Get the public key
  const pubkey = encodeSecp256k1Pubkey(signingPen.pubkey);

  // get the wallet address
  const accAddress = pubkeyToAddress(pubkey, 'secret');

  // initialize client
  const txEncryptionSeed = EnigmaUtils.GenerateNewSeed();

  const client = new SigningCosmWasmClient(
    httpUrl,
    accAddress,
    (signBytes) => signingPen.sign(signBytes),
    txEncryptionSeed,
    customFees
  );
  console.log(`Wallet address=${accAddress}`);

  // 1. Get a list of all tokens

  if (response.token_list.tokens.length == 0)
    console.log(
      'No token was found for you account, make sure that the minting step completed successfully'
    );
  const token_id = response.token_list.tokens[0];

  // 2. Query the public metadata

  // 3. Query the token dossier
  
  // 4. Set our viewing key
  
  // 5. Query the dossier again

};

main().catch((err) => {
  console.error(err);
});

```

The overall structure of this file looks familia by now and contains the setup for the client and signing structures that we will to interact with the contract. The fist thing we want to do, is getting a list of all our tokens. For this you should add the following code under `// 1. Get a list of all tokens`:

```javascript
 let queryMsg = {
    tokens: {
      owner: accAddress,
    },
  };

  console.log("Reading all tokens");
  let response = await client
    .queryContractSmart(process.env.SECRET_NFT_CONTRACT, queryMsg)
    .catch((err) => {
      throw new Error(`Could not execute contract: ${err}`);
    });
  console.log("response: ", response);
```

We will ask the contract for a list of all token ids, which are currently assigned to your address.
_**Note -**_ The deployed contract is configured to have public ownership for the sake of this tutorial. If you would deploy the contract yourself you can also decide to make this private, so that nobody but you will be able to see which tokens you own. We will do this in a later installment of this series.

Next we will take the first token id and ask for the details of this nft. There are different queries that are supported by the reference implementation. After `// 2. Query the public metadata` insert this code block:

```javascript
  queryMsg = {
    nft_info: {
      token_id: token_id,
    },
  };

  console.log(`Query public data of token #${token_id}`);
  response = await client
    .queryContractSmart(process.env.SECRET_NFT_CONTRACT, queryMsg)
    .catch((err) => {
      throw new Error(`Could not execute contract: ${err}`);
    });
  console.log("response: ", response);
```

This will retrieve the message you added as `publicMetadata` during the mint process. Next add this code block after `// 3. Query the token dossier`:

```javascript
 queryMsg = {
    nft_dossier: {
      token_id: token_id,
    },
  };

  console.log(`Query dossier of token #${token_id}`);
  response = await client
    .queryContractSmart(process.env.SECRET_NFT_CONTRACT, queryMsg)
    .catch((err) => {
      throw new Error(`Could not execute contract: ${err}`);
    });
  console.log("response: ", response);
```

The token dossier return all the data that we have access to, including public- and private-metadata, as well as some overall configuration of this token.
If you run this file using `node query-token.js` you should receive similar output to this:

```javascript
Reading all tokens
response:  { token_list: { tokens: [ '0' ] } }
Query public data of token #0
response:  {
  nft_info: {
    name: '<your public meta data>',
    description: null,
    image: null
  }
}
Query dossier of token #0
response:  {
  nft_dossier: {
    owner: '<your address>',
    public_metadata: {
      name: '<your public meta data>',
      description: null,
      image: null
    },
    private_metadata: null,
    display_private_metadata_error: 'You are not authorized to perform this action on token 0',
    owner_is_public: true,
    public_ownership_expiration: 'never',
    private_metadata_is_public: false,
    private_metadata_is_public_expiration: null,
    token_approvals: null,
    inventory_approvals: null
  }
}
```

One particular interesting part is this line: `display_private_metadata_error: 'You are not authorized to perform this action on token 0',`. Why are we not able to see our private metadata even if we *are* owning the nft?

To answer this we need to take a small detour through the inner workings of secret contract interactions.

Interaction with a contract fall into one of two distinct categories, you either *execute* a contract or you *query* a contract. The main difference here is that execution modifies the internal state of the contract and incurs a gas fee for doing so, while a query only reads from the internal state and it usually free.

_**Note -**_ Queries also have a calculated gas cost but are executed for free by the secret nodes. Node runners can define how expensive a qu7ery might be until they refuse to execute it. Also if you run a query during an execution call, its gas cost will be added to the total cost of the query.

In regards of the [privacy model](https://build.scrt.network/dev/privacy-model-of-secret-contracts.html#outputs) of secret contract whenever we send a query the sending address is not verified, as a query does not happen on chain, but rather just between the client and the contract. This means ultimately that we need to execute on a contract if we want to make sure who the contract is talking to. This is not optimal because of gas costs.

This leads to the problem above: We do not want to execute on the contract, because we are not changing its inner state. As a query is not recorded on chain we have no way of making sure *who* is sending that query. So even if we tell the contract that the query was send by us, it rejects our request for the private data, as there is no tamperproof way to make *sure* it is us.

The concept of Viewing keys is a clever solution for this problem. Instead of executing every time you will execute a single call and set a secret phrase, the so called key, for the contract. Whenever you send a query in the future you include this key with your query and the contract can look it up and validate it is actually you it is talking to. As secret network transmits fully encrypted messages no one will ever know your viewing keys.

To use this mechanic to get access to our private metadata add a string for your viewing key to the `.env` file, so that we can reuse it in our code.

```javascript
SECRET_VIEWING_KEY = "<random phrase>"
```

We will use this phrase in the following code, that you should add after `// 4. Set our viewing key`, as a kind of passphrase, uniquely identifying our address.

```javascript
 const handleMsg = {
    set_viewing_key: {
      key: process.env.SECRET_VIEWING_KEY,
    },
  };

  console.log('Set viewing key');
  response = await client
    .execute(process.env.SECRET_NFT_CONTRACT, handleMsg)
    .catch((err) => {
      throw new Error(`Could not execute contract: ${err}`);
    });
  console.log('response: ', response);
```

After we set our viewing key you also need to pass it with the query for the token dossier, so add the following code after `// 5. Query the dossier again`.

```javascript
queryMsg = {
    nft_dossier: {
      token_id: token_id,
      viewer: {
        address: accAddress,
        viewing_key: process.env.SECRET_VIEWING_KEY,
      },
    },
  };

  console.log(`Query dossier of token #${token_id} with viewing key`);
  response = await client
    .queryContractSmart(process.env.SECRET_NFT_CONTRACT, queryMsg)
    .catch((err) => {
      throw new Error(`Could not execute contract: ${err}`);
    });
  console.log('response: ', response);
```

Take note of how we augmented the query message to specify who is viewing the contract information, passing our own address and the secret key we told the contract in step 4. If you run the script again using `node query_token.js` you should find two results of query token in your output. The first one, that you already saw above has no access to the private metadata. The second entry however, where you passed the information about the viewer, will give you the private metadata along with all the other info and should look like this:

```javascript
response:  {
  nft_dossier: {
    owner: '<your address>',
    public_metadata: {
      name: '<your public meta data>',
      description: null,
      image: null
    },
    private_metadata: {
      name: '<your private meta data>',
      description: null,
      image: null
    },
    display_private_metadata_error: null,
    owner_is_public: true,
    public_ownership_expiration: 'never',
    private_metadata_is_public: false,
    private_metadata_is_public_expiration: null,
    token_approvals: [],
    inventory_approvals: []
  }
}
```

## Wrap up

Congratulations you have made it to the end of the first installment of the NFT on secret series. You have learned quite a few things on the way and I fell you can really be proud of what you achieved. Just to recap:

* You have minted your own, personalized nft
* You learned about the privacy model and how viewing keys help us to save gas, while making sure we can validate who we are talking to
* You had a close look into your own nft, learning about public, private and extended metadata.

This is a solid foundation to play with and build upon!

## What is coming next

Of course we are not yet at the end of our journey. In the coming tutorial we will have a look together into interesting and more complex examples of nft and their possibilities.
You can look forward to:

* Customizing your nft with a custom deployed contract
* Sending nfts around between different accounts
* Building a simple nft viewer
* Adding complex logic to your nfts, making them much more than *just* a store of secret data

Lets keep on building!
