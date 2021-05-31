---
description: Learn how to use Hardhat with the Avalanche C-Chain
---
# Using Hardhat with the Avalanche C-Chain


## Introduction

[Hardhat](https://hardhat.org) is a suite of tools that together provides us with a development environment that helps developers to easily manage and automate the tasks around building smart contracts and dApps. Hardhat could be used to compile, deploy, test, and debug smart contracts. It is very similar to [Truffle](https://www.trufflesuite.com/) in this regard. But, what differentiates Hardhat from Truffle is that it provides developers with a seamless experience developing and debugging complex Solidity smart contracts with the help of powerful extensibility of the ecosystem with the help of a rich collection of plugins as well as by providing many tools like Hardhat Network, Hardhat Runner, etc.

In this tutorial, we learn how Hardhat can be used with Avalanche C-Chain. Avalanche C-Chain is an instance of the EVM (Ethereum Virtual Machine). Being an instance of the EVM makes the Avalanche C-Chain very attractive as it could instantly be home to the millions of smart contract projects already written in Solidity.

## Prerequisites

Please make sure that you have completed the tutorials:

* [Avash Installation](https://learn.figment.io/network-documentation/avalanche/tutorials/local-avalanche-network-using-avash.md)

## Requirements

For the smooth completion of this tutorial, we need the following software to be already present on your system:

* [NodeJS](https://nodejs.org/) \(12.x+\)

## Creating a Hardhat project

The first step is to create a directory for your project where you will initialize our new Hardhat project. You can do this using the following command:

```bash
mkdir avalanche-hardhat-tutorial && cd avalanche-hardhat-tutorial
npm init -y
```

You should see a newly created directory named `avalanche-hardhat-tutorial` with a file `package.json` inside it:

```json
{
  "name": "avalanche-hardhat-tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

Now we go on to install Hardhat as one of our project dependencies with the following command:

```bash
npm install --save-dev hardhat
```

After the installation is done, type the following command to make the current directory a Hardhat project:

```bash
npx hardhat
```

You should be greeted with such a prompt by Hardhat:

```text
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

Welcome to Hardhat v2.2.1

? What do you want to do? …
Create a sample project
▸ Create an empty hardhat.config.js
Quit
```

Choose to create an empty hardhat.config.js and there you have it, your current directory is now a Hardhat project!

## Adding Avash network configuration to Hardhat

Now, in order for our Hardhat project to communicate with our local Avalanche network bootstrapped by Avash, we need to provide Hardhat with appropriate network configuration. For that, add the following properties to module.exports block of hardhat.config.js:

```javascript
defaultNetwork: "avash",
networks: {
  avash: {
    url: 'http://localhost:9650/ext/bc/C/rpc',
    gasPrice: 225000000000,
    chainId: 43112,
    accounts: [
      "0x56289e99c94b6912bfc12adc093c9b51124f0dc54ac7a766b2bc5ccf558d8027",
      "0x7b4198529994b0dc604278c99d153cfd069d594753d471171a1d102a10438e07",
      "0x15614556be13730e9e8d6eacc1603143e7b96987429df8726384c2ec4502ef6e",
      "0x31b571bf6894a248831ff937bb49f7754509fe93bbd2517c9c73c4144c0e97dc",
      "0x6934bef917e01692b789da754a0eae31a8536eb465e7bff752ea291dad88c675",
      "0xe700bdbdbc279b808b1ec45f8c2370e4616d3a02c336e68d85d4668e08f53cff",
      "0xbbc2865b76ba28016bc2255c7504d000e046ae01934b04c694592a6276988630",
      "0xcdbfd34f687ced8c6968854f8a99ae47712c4f4183b78dcc4a903d1bfe8cbf60",
      "0x86f78c5416151fe3546dece84fda4b4b1e36089f2dbc48496faf3a950f16157c",
      "0x750839e9dbbd2a0910efe40f50b2f3b2f2f59f5580bb4b83bd8c1201cf9a010a"
    ]
  },
},
```

* The network url we provided points to the url of the network launched by Avash.

* The gas price of 225000000000 is the current gas price of the network - anything less than that, the transactions will fail stating that the transaction is underpriced.

* 43112 is the id for the chain created by Avash in it's local Avalanche network. This is hardcoded in Avash.  

* The list of accounts we provided above are some randonly generated addresses on the network. When it comes to Avash, unlike mainnet and fuji testnet, these addresses come loaded with lots AVAX and so we don't need to fund them.

## Adding and compiling a smart contract

Hardhat looks for contracts to compile within the `contracts` directory. So let's go ahead and create this directory:

```bash
mkdir contracts
```

In the `contracts` directory we just created, create a new file called `Storage.sol` and add the following solidity code to it:

```javascript
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.8.0;

/**
* @title Storage
* @dev Store & retrieve the value in a variable
*/
contract Storage {

  uint256 number;

  /**
  * @dev Store value in variable
  * @param num value to store
  */
  function store(uint256 num) public {
    number = num;
  }

  /**
  * @dev Return value
  * @return value of 'number'
  */
  function retrieve() public view returns (uint256){
    return number;
  }
}
```

`Storage` is a solidity smart contract that lets us write a number to the blockchain via the `store` function and then read the number back from the blockchain via the `retrieve` function.

To compile the Storage.sol smart contract we just added to our project, use the following command:

```bash
npx hardhat compile
```

After the successful execution of the command, the smart contract should now be compiled, and you should see something like this in the console:

```text
Compiling 1 file with 0.7.3
Compilation finished successfully
```

## Using Hardhat console

For the rest of the tutorial, we're going to use the Hardhat console to deploy and interact with our smart contract. To successfully use the Hardhat console for our purposes, we need to install a couple of Hardhat plugins, namely [`hardhat-ethers`](https://www.npmjs.com/package/@nomiclabs/hardhat-ethers) and [`hardhat-waffle`](https://www.npmjs.com/package/@nomiclabs/hardhat-waffle). Install them using the following commands:

```bash
npm install --save-dev @nomiclabs/hardhat-ethers 'ethers@^5.0.0'
npm install --save-dev @nomiclabs/hardhat-waffle 'ethereum-waffle@^3.0.0' @nomiclabs/hardhat-ethers 'ethers@^5.0.0'
```

After these plugins are installed, we need to tell Hardhat to use them by requiring `hardhat-waffle` at the beginning of the Hardhat configuration file we created earlier:

```javascript
require("@nomiclabs/hardhat-waffle");
```

Now, the hardhat.config.js file should look something similar to this:

```javascript
require("@nomiclabs/hardhat-waffle");

/**
* @type import('hardhat/config').HardhatUserConfig
*/
module.exports = {
  solidity: "0.7.3",
  defaultNetwork: "avash",
    networks: {
      avash: {
        url: 'http://localhost:9650/ext/bc/C/rpc',
        gasPrice: 225000000000,
        chainId: 43112,
        accounts: [
          "0x56289e99c94b6912bfc12adc093c9b51124f0dc54ac7a766b2bc5ccf558d8027",
          "0x7b4198529994b0dc604278c99d153cfd069d594753d471171a1d102a10438e07",
          "0x15614556be13730e9e8d6eacc1603143e7b96987429df8726384c2ec4502ef6e",
          "0x31b571bf6894a248831ff937bb49f7754509fe93bbd2517c9c73c4144c0e97dc",
          "0x6934bef917e01692b789da754a0eae31a8536eb465e7bff752ea291dad88c675",
          "0xe700bdbdbc279b808b1ec45f8c2370e4616d3a02c336e68d85d4668e08f53cff",
          "0xbbc2865b76ba28016bc2255c7504d000e046ae01934b04c694592a6276988630",
          "0xcdbfd34f687ced8c6968854f8a99ae47712c4f4183b78dcc4a903d1bfe8cbf60",
          "0x86f78c5416151fe3546dece84fda4b4b1e36089f2dbc48496faf3a950f16157c",
          "0x750839e9dbbd2a0910efe40f50b2f3b2f2f59f5580bb4b83bd8c1201cf9a010a"
        ]
    },
  },
};
```

Now, we're ready to deploy our smart contract and interact with it!

## Deploying smart contracts using Hardhat console

To deploy our smart contract and to interact with it, we're gonna be using an interactive JavaScript console provided by Hardhat, which is called Hardhat console. Hardhat console is geared more towards providing us with quick local development and testing environment than writing scripts to do the same.

{% hint style="info" %}
Ensure that the local Avalanche network outlined in the `Setup a local Avalanche network using Avash` tutorial is running properly. Follow that other tutorial to completion, however, do not exit the Avash console after running the Lua script. Leave it running & in a separate terminal window, follow these instructions.
{% endhint %}

To fire up the Hardhat console, we use:

```
npx hardhat console
```

We'll be greeted with an interactive console environment that looks like this:

```text
Welcome to Node.js v14.16.1.
Type ".help" for more information.
>
```

Now we go on and type in the following snippets of code to deploy and interact with our Storage smart contract. After you input each line of code, press "Enter" to execute the same.

Inorder to get an instance of Storage.sol smart contract, we type in:

```javascript
const Storage = await hre.ethers.getContractFactory("Storage");
```

This returns:

```text
undefined
```

Here, `undefined` being printed does not mean that the instruction we executed has failed. Instead, it's `undefined` because we stored the return value into the Storage constant and there's nothing to be printed out into the console. We could verify that the command executed successfully by logging the contents of the Storage constant:

```javascript
console.log(Storage)
```

{% hint style="info" %}
What you see below is a Javascript representation of the compiled smart contract - you don't need to understand everything you see in the logged output. We print it out to demonstrate that the instruction was executed successfully.
{% endhint %}

![](../../../.gitbook/assets/storage-console-log.png)

Now we go on to deploy the Storage contract we just retrieved, using:

```
const storage = await Storage.deploy();
```

Again, this returns:

```text
undefined
```

Before we start interacting with the smart contract, we wait for the contract to be deployed successfully using:

```javascript
await storage.deployed();
```

This returns a very big chunk of output, which is the internal representation of the smart contract in JavaScript (truncated for brevity):

```text
ref *1> Contract {
  interface: Interface {
    fragments: [ [FunctionFragment], [FunctionFragment] ],
    _abiCoder: AbiCoder { coerceFunc: null },
    functions: {
      'retrieve()': [FunctionFragment],
      'store(uint256)': [FunctionFragment]
    },
    errors: {},
    events: {},
    structs: {},
    deploy: ConstructorFragment {
      name: null,
      type: 'constructor',
      inputs: [],
      payable: false,
      stateMutability: 'nonpayable',
      gas: null,
      _isFragment: true
    },
    _isInterface: true
  },
...
...
```

## Interacting with smart contracts using Hardhat console

To store our favorite magical number into the blockchain, we call the store function of the deployed contract:

```javascript
await storage.store(333);
```

This outputs the Javascript representation of the transaction details of writing to the blockchain:

```text
{
  hash: '0xaae491304254fce18fc3ff77f5891feef69035d05eb93c6b542823f948426ac4',
  type: 0,
  accessList: null,
  blockHash: null,
  blockNumber: null,
  transactionIndex: null,
  confirmations: 0,
  from: '0x8db97C7cEcE249c2b98bDC0226Cc4C2A57BF52FC',
  gasPrice: BigNumber { _hex: '0x34630b8a00', _isBigNumber: true },
  gasLimit: BigNumber { _hex: '0xaa26', _isBigNumber: true },
  to: '0x4Ac1d98D9cEF99EC6546dEd4Bd550b0b287aaD6D',
  value: BigNumber { _hex: '0x00', _isBigNumber: true },
  nonce: 5,
  data: '0x6057361d000000000000000000000000000000000000000000000000000000000000014d',
  r: '0x2be3e63b57eb1dd045361317e5380af910527a03b8e00cbde6226350d908d698',
  s: '0x6c45b5adabfd6cabde520a8f4a649b639f59e1e9fe3234713bb8152515bcf9d0',
  v: 86259,
  creates: null,
  chainId: 43112,
  wait: [Function (anonymous)]
}
```

Now, we're all set to read the number we just stored into our contract and print it out, using:

```javascript
let i = await storage.retrieve();
console.log(i.toNumber());
```

You should now see 333 printed back to the console.

## Conclusion

In this tutorial, we've successfully created a Hardhat project. Within the Hardhat project, we managed to add a new smart contract, compile it using Hardhat, and finally deployed the contract and interacted with it using the Hardhat console.

Congratulations on making it to the end of this tutorial!

> “No great thing is created suddenly, any more than a bunch of grapes or a fig.
> If you tell me that you desire a fig, I answer that there must be time. Let it
> first blossom, then bear fruit, then ripen.”
>
> -- <cite>Epictetus</cite>

So, keep learning and keep building and I'm sure you're on your way to building something great! Good luck!


If you had any difficulties following this tutorial or simply want to discuss Avalanche tech with us you can join [**our community**](https://discord.gg/fszyM7K) today!
