---
description: >-
  In this installment of the secretNFT series you will deploy your very own snip721 token contract and learn how to interact with it.
---

# Deploy your very own secretNFT

## About the Author

This tutorial was created by [Florian Uhde](https://twitter.com/florianuhde), a software engineer and game developer with a passion for blockchain, creativity and systemic design.

## Introduction

For a high level introduction to Non-Fungible-Tokens see the [first installment of this series](https://learn.figment.io/network-documentation/secret/tutorials/create-your-first-secret-nft). In this tutorial we will download and compile the [snip721 reference implementation](https://github.com/baedrik/snip721-reference-impl), deploy it onto the secret testnet and interact with the contract, minting your own secret NFTs. Contrary to the [previous tutorial](https://learn.figment.io/network-documentation/secret/tutorials/create-your-first-secret-nft) we will configure the contract ourself and learn about access-right management of secret contracts and tokens on the way.

### Prerequisites

This tutorial assumes that you have completed the [Secret Learn Pathway](https://learn.figment.io/network-documentation/secret/secret-pathway) already, as we will be building upon that foundation of knowledge and skill. If you have not already done so, you would be wise to take the time to complete the Pathway. We will start with the same project folder as in section 5 of the Pathway.

#### Requirements to successfully complete this tutorial

* The latest version of NodeJS installed \(use of nvm, the node version manager, is _encouraged_ for web3 developers\)
* A code editor like VSCode, Theia, Atom, _etc_.
* Required JavaScript packages –
  * secretjs - for the Secret Network JavaScript API
  * dotenv - for working with environment variables
* Rust + docker toolchain to compile secret contracts

For the latest you may want to refer to {% page-ref page="intro-pathway-secret-basics/5.-writing-and-deploying-your-first-secret-contract.md" %} which will help you setting up everything from a developer side.

## Generate and compile the snip721 contract

### Generate the contract

```text
cargo generate --git https://github.com/baedrik/snip721-reference-impl --name my-snip721
```

This git project is a reference implementation for tokens based on the [snip721 standard](https://github.com/SecretFoundation/SNIPs/blob/master/SNIP-721.md), which creates tokens that are loosely based on [ERC-721](https://eips.ethereum.org/EIPS/eip-721) and are a superset of [CW-721](https://github.com/CosmWasm/cosmwasm-plus/blob/master/packages/cw721/README.md), making them compatible with the Ethereum and Cosmos equivalents.

You can have a look at the generated files by stepping into the folder using:

```text
cd my-snip721
```

Most of the files in here should look very familiar from what you saw in part 5 of the secret pathway, with the main difference that the `scr` folder contains more files, due to the complexity of the contract

```text
Cargo.lock    Developing.md    LICENSE        Publishing.md    examples    schema        tests
Cargo.toml    Importing.md    NOTICE        README.md    rustfmt.toml    src
```

While we will keep most of the files as is during this tutorial we will visit some of the in the course of this tutorial to deepen your understanding what is happening behind the scenes. For now your goals is to compile the contract and upload it to the secret testnet.

### Compile the contract

To achieve this use the following command to compile the smart contract which produces the wasm contract file.

```text
cargo wasm
```

Before deploying or storing the contract on the testnet, you need to run the [secret contract optimizer](https://hub.docker.com/r/enigmampc/secret-contract-optimizer).

#### Optimize compiled wasm

```text
docker run --rm -v "$(pwd)":/contract \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  enigmampc/secret-contract-optimizer
```

This will output an optimized build file, `contract.wasm.gz`, ready to be stored on the Secret Network.

While `secretcli` supports uploading the compressed file, we'll be using SecretJS which expects wasm, so lets unpack the optimized `contract.wasm`

```bash
gunzip contract.wasm.gz
```

You should be able to find a newly created file `contract.wasm` in the current directory.

For uploading the compiled contract we will reuse the knowledge you gained during part 5 of the secret pathway.

### Uploading the contract

Start by creating a new file `deploy-nft.js` in the project directory and add the code below:

```javascript
const {
  EnigmaUtils, Secp256k1Pen, SigningCosmWasmClient, pubkeyToAddress, encodeSecp256k1Pubkey,
} = require('secretjs');

const fs = require('fs');

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
  const signingPen = await Secp256k1Pen.fromMnemonic(mnemonic)
    .catch((err) => { throw new Error(`Could not get signing pen: ${err}`); });

  // Get the public key
  const pubkey = encodeSecp256k1Pubkey(signingPen.pubkey);

  // get the wallet address
  const accAddress = pubkeyToAddress(pubkey, 'secret');

  // 1. Initialize client

  // 2. Upload the contract wasm

  const initMsg = { 
  // 3. Create an instance of the NFT contract init msg

   };
  const contract = await client.instantiate(codeId, initMsg, `My Snip721${Math.ceil(Math.random() * 10000)}`)
    .catch((err) => { throw new Error(`Could not instantiate contract: ${err}`); });
  const { contractAddress } = contract;
  console.log('contract: ', contract, 'address:', contractAddress);
};

main().catch((err) => {
  console.error(err);
});
```

#### Initialize client

In the `deploy-ft.js` file under the comment `// 1. Initialize client` add the following code snippet below:

```javascript
  const txEncryptionSeed = EnigmaUtils.GenerateNewSeed();

  const client = new SigningCosmWasmClient(
    httpUrl,
    accAddress,
    (signBytes) => signingPen.sign(signBytes),
    txEncryptionSeed, customFees,
  );
  console.log(`Wallet address=${accAddress}`);
```

#### Upload the contract wasm

Under the comment `// 2. Upload the contract wasm` add the following code snippet below:

```javascript
  const wasm = fs.readFileSync('contract.wasm');
  console.log('Uploading contract');
  const uploadReceipt = await client.upload(wasm, {})
    .catch((err) => { throw new Error(`Could not upload contract: ${err}`); });
```

### Instantiating the nft contract

Similar to what you have seen before we first got the `codeId` from the upload receipt and then defined the `initMsg` to finally instantiated the contract.
In this case the initMsg is more complex that for a simple counter and allows us to configure the secret NFT to our liking.

Open up the `mgs.rs` file withing the `scr` folder of the contract code. You should see two struct: `InitMsg` and `InitConfig` in there, which describe the settings object we can pass to our contract on initialization. For most values a sensible default is predefined, giving the most privacy-preserving behavior.

Lets have a look at the different fields and what part of the contract they control:

#### **InitMsg**

| Name               | Type                                                   | Description                                                         | Optional | Value If Omitted   |
|--------------------|--------------------------------------------------------|---------------------------------------------------------------------|----------|--------------------|
| name               | string                                                 | Name of the token contract                                          | no       |                    |
| symbol             | string                                                 | Token contract symbol                                               | no       |                    |
| admin              | string (HumanAddr)                                     | Address to be given admin authority                                 | yes      | env.message.sender |
| entropy            | string                                                 | String used as entropy when generating random viewing keys          | no       |                    |
| config             | [Config (see below)](#config)                          | Privacy configuration for the contract                              | yes      | defined below      |
| post_init_callback | [PostInitCallback (see below)](#postinitcallback)      | Information used to perform a callback message after initialization | yes      | nothing            |

#### **InitConfig**

| Name                          | Type | Description                                                         |Optional | Value If Omitted |
|-------------------------------|------|---------------------------------------------------------------------|---------|------------------|
| public_token_supply           | bool | This config value indicates whether the token IDs and the number of tokens controlled by the contract are public.  If the token supply is private, only minters can view the token IDs and number of tokens controlled by the contract | yes      | false            |
| public_owner                  | bool | This config value indicates whether token ownership is public or private by default.  Regardless of this setting a user has the ability to change whether the ownership of their tokens is public or private | yes      | false            |
| enable_sealed_metadata        | bool | This config value indicates whether sealed metadata should be enabled.  If sealed metadata is enabled, the private metadata of a newly minted token is not viewable by anyone, not even the owner, until the owner calls the Reveal message.  When Reveal is called, the sealed metadata is irreversibly unwrapped and moved to the public metadata (as default).  If `unwrapped_metadata_is_private` is set to true, the sealed metadata will remain as private metadata after unwrapping, but the owner (and anyone the owner has whitelisted) will now be able to see it.  Anyone will be able to query the token to know whether it has been unwrapped.  This simulates buying/selling a wrapped card that no one knows which card it is until it is unwrapped. If sealed metadata is not enabled, all tokens are considered unwrapped when minted | yes      | false            |
| unwrapped_metadata_is_private | bool | This config value indicates if the Reveal message should keep the sealed metadata private after unwrapping.  This config value is ignored if sealed metadata is not enabled | yes      | false            |
| minter_may_update_metadata    | bool | This config value indicates whether a minter is permitted to update a token's metadata | yes      | true             |
| owner_may_update_metadata     | bool | This config value indicates whether the owner of a token is permitted to update a token's metadata | yes      | false            |
| enable_burn                   | bool | This config value indicates whether burn functionality is enabled | yes      | false            |

For this tutorial we will keep most of the default values and just change the name, symbol and the default ownership visibility of the token.
Insert this piece of code below `// 3. Create an instance of the NFT contract init msg` and add some values for name and symbol, as well as some random string for the entropy

```javascript
  /// name of token contract
  name: '',
  /// token contract symbol
  symbol: '',
  /// entropy used for prng seed
  entropy: '',
  /// optional privacy configuration for the contract
  config: {
      public_owner: true
  },
```

Let's run the code:

```text
node deploy-nft.js
```

If it went well, you should see something like this;

```bash
Uploading contract
contract:  {
  contractAddress: 'secret1dsv9t7s6nn73xy3n3gfa7akham4td447vwfd5m',
  logs: [ { msg_index: 0, log: '', events: [Array] } ],
  transactionHash: 'E201BBC45316985C6FAED52BA02102734AA597EA9A54E5B4DD99B65D74AACBF2',
  data: '6C1855FA1A9CFD1312338A13DF76D7EEEAB6D6BE'
}
address:  'secret1dsv9t7s6nn73xy3n3gfa7akham4td447vwfd5m'
```

After this executed successfully you can take the program you created in the first installment, change the contract address to the one of your contract and interact with it in the same way!

{% page-ref page="create-your-first-secret-nft.md" %}


## Wrap up

Congratulations! We have made it to the end of the first installment of this Secret NFT series. We have covered a lot of information, and I feel you can really be proud of what you have achieved. Just to recap:

* You created and compiled your very own secretNFT contract, based on snip721
* You explored different parameters to configure the contract to your liking
* You created an instance of your contract on the secret testnet, ready to be interacted with

This is a solid foundation to play with and build upon!
