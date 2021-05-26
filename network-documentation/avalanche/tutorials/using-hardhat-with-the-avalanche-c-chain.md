---
description: Learn how to use Hardhat with the Avalanche C-Chain
---
# Using Hardhat with the Avalanche C-Chain


## Introduction

[Hardhat](https://hardhat.org) is a suite of tools that together provides us with a development environment that helps developers to easily manage and automate the tasks around building smart contracts and dApps. It is very similar to [Truffle](https://www.trufflesuite.com/) in this regard. Hardhat could be used to compile, deploy, test, and debug smart contracts. In this tutorial, we learn how Hardhat can be used with Avalanche C-Chain. Avalanche C-Chain is an instance of the EVM (Ethereum Virtual Machine).

## Prerequisites

Please make sure that you have completed the tutorials:

* [Avash Installation](https://learn.figment.io/network-documentation/avalanche/tutorials/local-avalanche-network-using-avash.md)

## Requirements

For the smooth completion of this tutorial, we need the following software to be already present on your system:

* [NodeJS](https://nodejs.org/) \(12.x+\)

## Creating a Hardhat project

The first step is to create a directory for your project where you will initialize our new Hardhat project. You can do this using the following command:

```bash
npm init -y
```

As a result, you will see a new file package.json created in the current directory:

```text
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

Now we go on to install hardhat as one of our project dependencies with the following command:

```bash
npm install --save-dev hardhat
```

After the installation is done, type the following command to make the current directory a hardhat project:

```bash
npx hardhat
```

You should be greeted with such a prompt by hardhat:

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

Choose to create an empty hardhat.config.js and there you have it, your current directory is now a hardhat project!

## Adding avash network configuration to hardhat

Now, inorder for our hardhat project to communicate with our local Avalanche network bootstrapped by avash, we need to provide hardhat with appropriate network configuration. For that, add the following properties to module.exports block of hardhat.config.js:

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

Now, your hardhat configuration in hardhat.config.js should look something similar to this after the addition of the avash network configuration provided above:

```javascript
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

## Adding and compiling a smart-contract

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

To compile the Storage.sol smart-contract we just added to our project, use the following command:

```bash
npx hardhat compile
```

After the successful execution of the command, the smart-contract should now be compiled, and you should see something like this in the console:

```text
Compiling 1 file with 0.7.3
Compilation finished successfully
```

## Using hardhat console

For the rest of the tutorial, we're going to use the hardhat console to deploy and interact with our smart-contract. To successfully use the hardhat console for our purposes, we need to install a couple of hardhat plugins, namely [`hardhat-ethers`](https://www.npmjs.com/package/@nomiclabs/hardhat-ethers) and [`hardhat-waffle`](https://www.npmjs.com/package/@nomiclabs/hardhat-waffle). Install them using the following commands:

```bash
npm install --save-dev @nomiclabs/hardhat-ethers 'ethers@^5.0.0'
npm install --save-dev @nomiclabs/hardhat-waffle 'ethereum-waffle@^3.0.0' @nomiclabs/hardhat-ethers 'ethers@^5.0.0'
```

After these plugins are installed, we need to tell hardhat to use them by requiring `hardhat-waffle` at the beginning of the hardhat configuration file we created earlier:

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

Now, we're ready to deploy our smart-contract and to interact with it!

## Deploying smart-contracts using hardhat console

To deploy our smart-contract and to interact with it, we're gonna be using an interactive JavaScript console provided by hardhat, which is called hardhat console. Hardhat console is geared more towards providing us with quick local development and testing environment.

{% hint style="info" %}
Make sure that the Lua script we created earlier in the `Avash Installation` tutorial (listed under ``Prerequisites`) is running inside Avash console as mentioned in the `Setup a local Avalanche network using Avash` section of the tutorial. This creates the temporary local blockchain hardhat console will be connected to.
{% endhint %}

To fire up the hardhat console, we use:

```
npx hardhat console
```

We'll be greeted with an interactive console environment that looks like this:

```text
Welcome to Node.js v14.16.1.
Type ".help" for more information.
>
```

Now we go on and type in the following snippets of code to deploy and interact with our Storage smart-contract. After you input each line of code, press "Enter" to execute the same.

Inorder to get an instance of Storage.sol smart-contract, we type in:

```javascript
const Storage = await hre.ethers.getContractFactory("Storage");
```

Now we go on to deploy the Storage contract we just retrieved, using:

```
const storage = await Storage.deploy();
```

Before we start interacting with the smart-contract, we wait for the contract to be deployed successfully using:

```javascript
await storage.deployed();
```

## Interacting with smart-contracts using hardhat console

To store our favorite magical number into the blockchain, we call the store function of the deployed contract:

```javascript
await storage.store(333);
```

Now, we're all set to read the number we just stored into our contract and print it out, using:

```javascript
let i = await storage.retrieve();
console.log(i.toNumber());
```

You should now see 333 printed back to the console.

## Conclusion

In this tutorial, we've successfully created a hardhat project. Within the hardhat project, we managed to add a new smart-contract, compile it using hardhat, and finally deployed the contract and interacted with it using the hardhat console.

Congratulations on making it to the end of this tutorial!

> “No great thing is created suddenly, any more than a bunch of grapes or a fig.
> If you tell me that you desire a fig, I answer that there must be time. Let it
> first blossom, then bear fruit, then ripen.”
>
> -- <cite>Epictetus</cite>

So, keep learning and keep building and I'm sure you're on your way to building something great! Good luck!


If you had any difficulties following this tutorial or simply want to discuss Avalanche tech with us you can join [**our community**](https://discord.gg/fszyM7K) today!

## About the author

This tutorial is authored by **Kevin Madhu** who is currently working as a Technical Architect in [BREATHE BINARY OÜ](https://github.com/breathebinary). **BREATHE BINARY** is an Estonian-based software company that specializes in building amazing Web2 and Web3 applications. DeFi and dApps in general are a newfound passion for the author and so feel free to reach out to him on his [Figment Forum Profile](https://community.figment.io/u/kevin) for any queries regarding this tutorial or anything related to blockchain or technology in general.
