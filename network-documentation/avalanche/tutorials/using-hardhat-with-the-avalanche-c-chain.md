---
description: Learn how to use Hardhat with the C-Chain
---
# Using Hardhat with the Avalanche C-Chain 
<sub>Contributed By [BREATHE BINARY OÜ](https://github.com/breathebinary)</sub>


## Introduction

[Hardhat](https://hardhat.org) is a development environment to compile, deploy, test, and debug your Ethereum software. It helps developers manage and automate the recurring tasks that are inherent to the process of building smart contracts and dApps, as well as easily introducing more functionality around this workflow. This means compiling, running and testing smart contracts at the very core. This tutorial illustrates how Hardhat can be used with Avalanche's C-Chain, which is an instance of the EVM.

## Prerequisites

Before we dig into the tutorial, let's check if you have the right environment:

* Download and setup [Golang](https://golang.org/) \(1.15.10\)
* Download the latest version of NodeJS \(12.x+\)
* Downoad the latest version of [Avash](https://github.com/ava-labs/avash)

## Setup a local Avalanche network using Avash

[Avash](https://github.com/ava-labs/avash) is a temporary stateful shell execution environment used to deploy networks locally, manage their processes, and run network tests. It allows you to spin up private test network deployments with up to 15 AvalancheGo nodes out-of-the-box and supports automation of regular tasks via lua scripts. This enables rapid testing against a wide variety of configurations. The first time you use avash you'll need to [install and build it](https://github.com/ava-labs/avash#quick-setup). It's similar to [Hardhat Network](https://hardhat.org/hardhat-network/).

After you're done with setting up avash as described in quick setup, you should be in a position to run Avalanche nodes on your machine using avash.

Firstly, you need to add two files to your avash installation into the scripts directory.

***scripts/config/staking\_node\_config.json***

```
{
    "db-enabled": false,
    "staking-enabled": true,
    "log-level": "debug",
    "coreth-config": {
        "snowman-api-enabled": false,
        "coreth-admin-api-enabled": false,
        "net-api-enabled": true,
        "rpc-gas-cap": 2500000000,
        "rpc-tx-fee-cap": 100,
        "eth-api-enabled": true,
        "tx-pool-api-enabled": true,
        "debug-api-enabled": true,
        "web3-api-enabled": true,
        "personal-api-enabled": true
    }
}
```

***scripts/five\_node\_staking\_with\_config.lua***

```javascript
cmds = {
    "startnode node1 --config-file=scripts/config/staking_node_config.json --http-port=9650 --staking-port=9651 --bootstrap-ips= --staking-tls-cert-file=certs/keys1/staker.crt --staking-tls-key-file=certs/keys1/staker.key",
    "startnode node2 --config-file=scripts/config/staking_node_config.json --http-port=9652 --staking-port=9653 --bootstrap-ips=127.0.0.1:9651 --bootstrap-ids=NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg --staking-tls-cert-file=certs/keys2/staker.crt --staking-tls-key-file=certs/keys2/staker.key",
    "startnode node3 --config-file=scripts/config/staking_node_config.json --http-port=9654 --staking-port=9655 --bootstrap-ips=127.0.0.1:9651 --bootstrap-ids=NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg --staking-tls-cert-file=certs/keys3/staker.crt --staking-tls-key-file=certs/keys3/staker.key",
    "startnode node4 --config-file=scripts/config/staking_node_config.json --http-port=9656 --staking-port=9657 --bootstrap-ips=127.0.0.1:9651 --bootstrap-ids=NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg --staking-tls-cert-file=certs/keys4/staker.crt --staking-tls-key-file=certs/keys4/staker.key",
    "startnode node5 --config-file=scripts/config/staking_node_config.json --http-port=9658 --staking-port=9659 --bootstrap-ips=127.0.0.1:9651 --bootstrap-ids=NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg --staking-tls-cert-file=certs/keys5/staker.crt --staking-tls-key-file=certs/keys5/staker.key",
}

for key, cmd in ipairs(cmds) do
    avash_call(cmd)
end
```


To start a five node local Avalanche network, follow these steps:

```console
cd $GOPATH/src/github.com/ava-labs/avash
$ ./avash
```

If everything goes well, you must be looking at the avash prompt as shown below:

```console
avash>
```

Now you need to run the script we just created above which will start a five node staking network on your machine.

```console
runscript scripts/five_node_staking_with_config.lua
```

If the nodes started successfully, the console should look similar to what you see below:

```console
avash> runscript scripts/five_node_staking_with_config.lua
RunScript: Running scripts/five_node_staking_with_config.lua
RunScript: Successfully ran scripts/five_node_staking_with_config.lua
```

Viola! A five node Avalanche network is running on your machine. Keep the avash terminal running for now. To stop these nodes and exit avash after you're done with this tutorial,type `exit` into the avash prompt and hit enter.

## Creating Hardhat project

The first step is to create a directory for your project where you will initialize the new Hardhat project. You can do this using the following command:

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
▸ Create a sample project
  Create an empty hardhat.config.js
  Quit
```

Choose create an empty hardhat.config.js and there you have it, your current directory is now a hardhat project.

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

Hardhat looks for contracts to compile in `contracts`. So let's go ahead and create this directory:

```bash
mkdir contracts
```

In the `contracts` directory we just created, create a new file called `Storage.sol` and add the following solidity code to it:

```javascript
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.8.0;

/**
 * @title Storage
 * @dev Store & retrieve value in a variable
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

`Storage` is a solidity smart contract which lets us write a number to the blockchain via the `store` function and then read the number back from the blockchain via the `retrieve` function.

To compile the Storage.sol smart-contract we just added to our project, use the following command:

```bash
npx hardhat compile
```

If the smart-contract was successfully compiled, you should see something like this in the console:

```text
Compiling 1 file with 0.7.3
Compilation finished successfully
```

If you managed to get this far without any errors, you've successfully compiled the smart-contract using hardhat!

## Using hardhat console

For the rest of the tutorial we're going to use hardhat console to deploy and interact with our smart-contract. To successfully use hardhat console for our purposes, we need to install couple of hardhat plugins, namely [`hardhat-ethers`](https://www.npmjs.com/package/@nomiclabs/hardhat-ethers) and [`hardhat-waffle`](https://www.npmjs.com/package/@nomiclabs/hardhat-waffle). Install them using the following commands:

```bash
npm install --save-dev @nomiclabs/hardhat-ethers 'ethers@^5.0.0'
npm install --save-dev @nomiclabs/hardhat-waffle 'ethereum-waffle@^3.0.0' @nomiclabs/hardhat-ethers 'ethers@^5.0.0'
```

After these plugins are installed, we need to tell hardhat to use them by adding requiring `hardhat-waffle` in the beginning of the hardhat configuration file we created earlier:

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
    hardhat: {
    },
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

To fire up hardhat console, we use:

```
npx hardhat console
```

Inorder to get Storage.sol smart-contract, we use:

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

To store our favourite magical number into the blockchain, we call the store function of the deployed contract:

```javascript
await storage.store(333);
```

Now, we're all set to read the number we just stored into our contract and print it out, using:

```javascript
let i = await storage.retrieve();
console.log(i.toNumber());
```

If everything went on smoothly, you should see 333 printed back to the console.

Congratulations, you have successfully deployed and interacted with our smart-contract using hardhat!

## Summary

In this tutorial, we've successfully launched a local Avalanche network using avash,  and created a hardhat project. Within the hardhat project, we managed to add a new smart-contract, compile it using hardhat and finally deployed the contract and interacted with it using hardhat console.

If you had any difficulties following this tutorial or simply want to discuss Avalanche tech with us you can join [**our community**](https://discord.gg/fszyM7K) today!
