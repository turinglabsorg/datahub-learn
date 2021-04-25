---
description: Learn how to write a Vault for Celo, and interface with it using web3.
---

# Write, deploy, and interact using web3 with your first Celo Vault Smart Contract

## Introduction

In this tutorial, we will create, deploy, and interact with our first Vault Smart Contract on the Celo Ecosystem.

What's a Vault Smart Contract, and the deployment, and interaction thing?

A Vault as defined in this tutorial proofs that your contributed funds are locked through inviolable smart contracts.

First, we will be deploying a Vault Smart Contract to the Alfajores Testnet using DataHub and Truffle.

Second, we will interface with the deployed Vault Smart Contract and demostrate its functionalities using a frontend with React and Web3.

Ready, set, go!

### Prerequisites

Go get a connection to Celo Testnet through DataHub below (Figment Docs are made with love):
1. [Connect to Celo node with DataHub](https://learn.figment.io/network-documentation/celo/tutorial/intro-pathway-celo-basics/1.connect#configuring-environment)

Come back when you get your API KEY, this is required for next steps in tutorial.

Please make sure that you completed the tutorials:

1. [Connect to Celo node with DataHub](https://learn.figment.io/network-documentation/celo/tutorial/1.connect)
2. [Query the Celo Network](https://learn.figment.io/network-documentation/celo/tutorial/3.query)
3. [Submit your first transactions](https://learn.figment.io/network-documentation/celo/tutorial/4.transactions)
4. [Write and deploy your first Celo smart contract](https://learn.figment.io/network-documentation/celo/tutorial/intro-pathway-celo-basics/5.smart-contract)

## Creating Project (Central Base)

Since Celo runs the Ethereum Virtual Machine, we can use Truffle to compile our Smart Contract. But just to do things right, let's first initialize a node project (silently...).

```bash
$ mkdir vault-dapp 
$ cd vault dapp
$ npm init -y --silent
```

Adding our dependencies (for now):
```bash
$ npm install --save @openzeppelin/contracts truffle @celo/contractkit dotenv web3
```

Initialize a barebone truffle project, by running the following command.

```bash
$ npx truffle init
```

Set (datahub url + api-key, see Prerequisites for the api-key) to an enviroment variable . For this create a file called `./.env`

```
DATAHUB_NODE_URL=https://celo-alfajores--rpc.datahub.figment.io/apikey/<YOUR API KEY>/
```

Make sure our `./.env` does not get pushed by mistake (our key is inside). For this create a new file called `./.gitignore` 
```
node_modules
.env
```

Lastly, initialize git.
```
$ git init
```

OK, I'd love to congratulate you, we just initlized a node, and a truffle project. We also added git, and secured our key like a pro. Your directory tree should look like this:

```bash
04/17/2021  05:03 AM    <DIR>          .
04/17/2021  05:03 AM    <DIR>          ..
04/17/2021  05:03 AM    <DIR>          contracts
04/17/2021  05:03 AM    <DIR>          migrations
04/17/2021  05:00 AM    <DIR>          node_modules
04/17/2021  05:00 AM           678,735 package-lock.json
04/17/2021  05:00 AM               395 package.json
04/17/2021  05:03 AM    <DIR>          test
10/26/1985  04:15 AM             4,598 truffle-config.
04/17/2021  05:00 AM               395 .env
               3 File(s)        683,728 bytes
               6 Dir(s)  231,304,970,240 bytes free
```

## Get Account and Funds
Our next step is to get an Account (Address / PrivateKey) from the Celo Alfajores Network. We are going to use Truffle Console to get one real quick.

Copy and paste the following into your `./truffle-config.js`

```js
// LOAD ENV VAR
require('dotenv').config();

// INIT PROVIDER USING CONTRACT KIT
const Kit = require('@celo/contractkit')
const kit = Kit.newKit(process.env.DATAHUB_NODE_URL)

// AWAIT WRAPPER FOR ASYNC FUNC
async function awaitWrapper() {
    let account = kit.connection.addAccount(process.env.PRIVATE_KEY)
}

awaitWrapper()

// TRUFFLE CONFIG OBJECT
module.exports = {
    networks: {
        alfajores: {
            provider: kit.connection.web3.currentProvider, // CeloProvider
            network_id: 44787 // latest Alfajores network id
        },
    },
    // Configure your compilers
    compilers: {
        solc: {
            version: "0.8.3", // Fetch exact version from solc-bin (default: truffle's version)
        }
    },
    db: {
        enabled: false
    }
};
```

Here we initliazed a provider using our enviroment variable `DATAHUB_NODE_URL`.

Now we can connect to Alfajores using `truffle console` . Run the following on your console.

```bash
$ npx truffle console --network alfajores
```

Once connected, initialize and print an account as follows:
```js
let account = web3.eth.accounts.create()
console.log(account)
```
This is my output:

```bash
$ truffle console --network alfajores
web3-shh package will be deprecated in version 1.3.5 and will no longer be supported.
web3-bzz package will be deprecated in version 1.3.5 and will no longer be supported.
truffle(alfajores)> let account = web3.eth.accounts.create()
undefined
truffle(alfajores)> console.log(account)
{
  address: '0x7cdf6c19E5491EA23aB14132f8a76Ff1C74ccAFC',
  privateKey: '0x167ed276fb95a17de53c6b0fa4737fc2f590f3e6c5b9de0793d9bcdf63140650',
  signTransaction: [Function: signTransaction],
  sign: [Function: sign],
  encrypt: [Function: encrypt]
}
```
you can exit from console with `ctrl+C` or `ctrl+D`.

From here we need the address, and `privateKey` as I mentioned.

Success! We got and account, before we forget let's save it into our `.env` as we will use it later. Your `.env` should look like this.

```bash
DATAHUB_NODE_URL=https://celo-alfajores--rpc.datahub.figment.io/apikey/<YOUR API KEY>/
ADDRESS=0x7cdf6c19E5491EA23aB14132f8a76Ff1C74ccAFC # your address
PRIVATE_KEY=0x167ed276fb95a17de53c6b0fa4737fc2f590f3e6c5b9de0793d9bcdf63140650 # your private key
```

Let's add funds to our account using the Alfajores Faucet, go there with you address. (Do you also wish to have one but for the Mainet):

[Alfajores Faucet](https://celo.org/developers/faucet)

You will get:
```
cGLD => 5
cUSD => 10
cEUR => 10
```
you can verify your balance in [Alfajores Blockscout](https://alfajores-blockscout.celo-testnet.org/) searching you `address`.

We are finally done with this section. We are getting closer by the minute.

## Deploy the Vault Smart Contract

Now that we have an account and funds, let's add a Smart Contract to our Truffle project. From the console, run the following:

```bash
$ npx truffle create contract Vault
```

The above command will create a new Smart Contract in the following location: 

```bash
$ ls -la vault-dapp/contracts # Listing directory: vault-dapp/contracts:

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/17/2021  6:12 AM            114 Vault.sol
```

The code for the Vault Smart Contract its been cooked for us already. Copy, paste, read:

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
import "@openzeppelin/contracts/utils/math/SafeMath.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract HelpiLocker is Ownable{
    using SafeMath for uint256;
    using SafeERC20 for IERC20;

    struct Items {
        IERC20 token;
        address withdrawer;
        uint256 amount;
        uint256 unlockTimestamp;
        bool withdrawn;
        bool deposited;
    }
    
    uint256 public depositsCount;
    mapping (address => uint256[]) public depositsByTokenAddress;
    mapping (uint256 => Items) public lockedToken;
    mapping (address => mapping(address => uint256)) public walletTokenBalance;
    
    address public helpiMarketingAddress;
    
    event Withdraw(address withdrawer, uint256 amount);
    
    constructor() {
      
    }
    
    function lockTokens(IERC20 _token, address _withdrawer, uint256 _amount, uint256 _unlockTimestamp) external returns (uint256 _id) {
        require(_amount > 500, 'Token amount too low!');
        require(_unlockTimestamp < 10000000000, 'Unlock timestamp is not in seconds!');
        require(_unlockTimestamp > block.timestamp, 'Unlock timestamp is not in the future!');
        require(_token.allowance(msg.sender, address(this)) >= _amount, 'Approve tokens first!');
        _token.safeTransferFrom(msg.sender, address(this), _amount);
        
        walletTokenBalance[address(_token)][msg.sender] = walletTokenBalance[address(_token)][msg.sender];
        
        _id = ++depositsCount;
        lockedToken[_id].token = _token;
        lockedToken[_id].withdrawer = _withdrawer;
        lockedToken[_id].amount = _amount;
        lockedToken[_id].unlockTimestamp = _unlockTimestamp;
        lockedToken[_id].withdrawn = false;
        lockedToken[_id].deposited = true;
        
        depositsByTokenAddress[address(_token)].push(_id);
    }
    
    function withdrawTokens(uint256 _id) external {
        require(block.timestamp >= lockedToken[_id].unlockTimestamp, 'Tokens are still locked!');
        require(msg.sender == lockedToken[_id].withdrawer, 'You are not the withdrawer!');
        require(lockedToken[_id].deposited, 'Tokens are not yet deposited!');
        require(!lockedToken[_id].withdrawn, 'Tokens are already withdrawn!');
        
        lockedToken[_id].withdrawn = true;
        
        walletTokenBalance[address(lockedToken[_id].token)][msg.sender] = walletTokenBalance[address(lockedToken[_id].token)][msg.sender].sub(lockedToken[_id].amount);
        
        emit Withdraw(msg.sender, lockedToken[_id].amount);
        lockedToken[_id].token.safeTransfer(msg.sender, lockedToken[_id].amount);
    }
    
    function getDepositsByTokenAddress(address _token) view external returns (uint256[] memory) {
        return depositsByTokenAddress[_token];
    }
    
    function getTokenTotalLockedBalance(address _token) view external returns (uint256) {
       return IERC20(_token).balanceOf(address(this));
    }
}
```

Let's go through the main sections of the our Vault Smart Contract:

Structure to store our deposits
```solidity
struct Items {
        IERC20 token;
        address withdrawer;
        uint256 amount;
        uint256 unlockTimestamp;
        bool withdrawn;
        bool deposited;
    }
```

`lockTokens` function accepts the following parameters when being invoked:
```solidity
lockTokens(IERC20, address, amount, time) 
```
and it will lock an amount of ERC20 tokens in the contract for an arbitrary time.

```solidity
IERC20 _token, => An ERC20 Token.
address _withdrawer => The Address which can withdraw (usually same as deposting address).
uint256 _amount => Amount the ERC20 Token.
uint256 _unlockTimestamp => When to unclock deposit.
```

The `withdrawTokens` function accepts an address and checks it's existance as a `_withdrawer` in our Structure. The function also checks if funds are past the `_unlockTimestamp`.
```solidity
withdrawTokens(address) 
```

```
address _withdrawer => The address which was registered in our contract when the deposit was made _withdrawer
```

We are now ready to compile our solidity code using Truffle. From your terminal run the following:
```bash
$ npx truffle compile
```

This is my output, all good if yours matches:

```bash
$ npx truffle compile
web3-shh package will be deprecated in version 1.3.5 and will no longer be supported.
web3-bzz package will be deprecated in version 1.3.5 and will no longer be supported.

Compiling your contracts...
===========================
> Compiling @openzeppelin/contracts/access/Ownable.sol
> Compiling @openzeppelin/contracts/token/ERC20/IERC20.sol
> Compiling @openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol
> Compiling @openzeppelin/contracts/utils/math/SafeMath.sol
> Compiling @openzeppelin\contracts\token\ERC20\IERC20.sol
> Compiling @openzeppelin\contracts\utils\Address.sol
> Compiling @openzeppelin\contracts\utils\Context.sol
> Compiling .\contracts\Migrations.sol
> Compiling .\contracts\Vault.sol
> Artifacts written to C:\Users\aglamadrid19\vault-dapp\build\contracts
> Compiled successfully using:
   - solc: 0.8.3+commit.8d00100c.Emscripten.clang
```

Truffle would automatically place our Vault Smart Contract bytecode and ABI inside the following json file. Make sure you can see it.
```bash
$ ls -la  vault-dapp\build\contracts #Directory Listing: vault-dapp\build\contracts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/17/2021  7:12 AM         870362 Vault.json
```

I hope you are ready for deployment, because that's next!

Before deploying the contract using Truffle, we need to add our funded account to our ContractKit instance inside `truffle-config.js`
```js
// LOAD ENV VAR
require('dotenv').config();

// INIT PROVIDER USING CONTRACT KIT
const Kit = require('@celo/contractkit')
const kit = Kit.newKit(DATAHUB_NODE_URL)

// AWAIT WRAPPER FOR ASYNC FUNC
async function awaitWrapper() {
    let account = kit.connection.addAccount(process.env.PRIVATE_KEY) // ADDING ACCOUNT HERE
}

awaitWrapper()

// TRUFFLE CONFIG OBJECT
module.exports = {
    networks: {
        alfajores: {
            provider: kit.connection.web3.currentProvider, // CeloProvider
            network_id: 44787 // latest Alfajores network id
        },
    },
    // Configure your compilers
    compilers: {
        solc: {
            version: "0.8.3", // Fetch exact version from solc-bin (default: truffle's version)
        }
    },
    db: {
        enabled: false
    }
};
```

With this we are ready to run `truffle migrate --network alfajores`, this will hopefully deploy our Vault Smart Contract to the Celo Alfajores Network. In your console run.
```bash
npx truffle migrate --network alfajores
```

A succesful deployment looks like this:

```
Starting migrations...
======================
> Network name:    'alfajores'
> Network id:      44787
> Block gas limit: 0 (0x0)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0x9223481ec81ab8efe26c325a61bf87369fa451210f2be6a08237df769952af45
   > Blocks: 0            Seconds: 0
   > contract address:    0xC58c6144761DBBE7dd7633edA98a981cb73169Df
   > block number:        4661754
   > block timestamp:     1618659322
   > account:             0x7cdf6c19E5491EA23aB14132f8a76Ff1C74ccAFC
   > balance:             4.93890836
   > gas used:            246292 (0x3c214)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00492584 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00492584 ETH


2_vault_deployment.js
=====================

   Deploying 'Vault'
   -----------------
   > transaction hash:    0x8b112defb0ed43eee6009445f452269e18718094cbd949e6ff7f51ef078abd84
   > Blocks: 0            Seconds: 0
   > contract address:    0xB017aD96e31B43AFB670dAB020561dA8E2154C5B
   > block number:        4661756
   > block timestamp:     1618659332
   > account:             0x7cdf6c19E5491EA23aB14132f8a76Ff1C74ccAFC
   > balance:             4.89973486
   > gas used:            1916762 (0x1d3f5a)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.03833524 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.03833524 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.04326108 ETH
```

Awesome!, our Vault Smart Contract is now in Alfajores, and we can deposit funds into it, lock them, and withdraw them.

Let's build an interface in React for our Vault Smart Contract next.

## Interface with the Vault Smart Contract

First, let's initialize our react app, we can do this within our node/truffle project directory we have been working on:

```bash
npx create-react-app my-vault-interface
cd my-vault-interface
```

We need to add the following dependencies to our new react project:

```bash
npm install @celo/contractkit web3 dotenv
```

Let's also transfer our .env file over to this new project, with a quick fix:

```
REACT_APP_DATAHUB_NODE_URL=https://alfajores-forno.celo-testnet.org
REACT_APP_ADDRESS=0x7cdf6c19E5491EA23aB14132f8a76Ff1C74ccAFC
REACT_APP_PRIVATE_KEY=0x167ed276fb95a17de53c6b0fa4737fc2f590f3e6c5b9de0793d9bcdf63140650
REACT_APP_VAULT_ADDRESS=0xB017aD96e31B43AFB670dAB020561dA8E2154C5B
```

This is how mine looks like, as you can see we still have the same variables, juts added the REACT_APP prefix.

Next, let's transfer the json file where our contract bytecode and abi resides, and paste inside a new `contract` folder into the root of our new React Project, see below:
```
Original Location (Truffle Compile)
Directory: C:\Users\aglamadrid19\vault-dapp\build\contracts\Vault.json

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/17/2021  7:35 AM         870803 Vault.json

React App Location
Directory: C:\Users\aglamadrid19\vault-dapp\my-vault-interface\src\contract\Vault.json

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/17/2021  7:35 AM         870803 Vault.json
```
With this we complete the requirements of our frontend, to recap, this is how the React Project directory tree looks like:
```
    Directory: C:\Users\aglamadrid19\vault-dapp\my-vault-interface

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----           4/17/2021  9:03 AM                node_modules
d----           4/17/2021  7:51 AM                public
d----           4/17/2021  9:46 AM                src
-a---           4/17/2021  9:59 AM            287 .env
-a---           4/17/2021  7:51 AM            310 .gitignore
-a---           4/17/2021  9:03 AM         766889 package-lock.json
-a---           4/17/2021  9:03 AM            903 package.json
-a---           4/17/2021  7:51 AM           3362 README.md
-a---           4/17/2021  7:51 AM         507434 yarn.lock
```

```
    Directory: C:\Users\aglamadrid19\vault-dapp\my-vault-interface\src

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----           4/17/2021  9:46 AM                contract
-a---           4/17/2021  7:51 AM            564 App.css
-a---           4/17/2021 10:01 AM           1520 App.js
-a---           4/17/2021  7:51 AM            246 App.test.js
-a---           4/17/2021  7:51 AM            366 index.css
-a---           4/17/2021  7:51 AM            500 index.js
-a---           4/17/2021  7:51 AM           2632 logo.svg
-a---           4/17/2021  7:51 AM            362 reportWebVitals.js
-a---           4/17/2021  7:51 AM            241 setupTests.js
```

```
    Directory: C:\Users\aglamadrid19\vault-dapp\my-vault-interface\src\contract

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/17/2021  7:35 AM         870803 Vault.json
```

Our next step is to copy and paste some code to the App React Component `App.js`.
```
import React, { useState } from 'react';
import { newKit } from '@celo/contractkit'
import dotenv from 'dotenv'
import Vault from './contract/Vault.json'

// LOAD ENV VAR
dotenv.config()

const kit = newKit(process.env.REACT_APP_DATAHUB_NODE_URL)
const connectAccount = kit.addAccount(process.env.REACT_APP_PRIVATE_KEY)
// CONTRACT INSTANCE
const VaultO = new kit.web3.eth.Contract(Vault.abi, process.env.REACT_APP_VAULT_ADDRESS)

const lock = async () => {
  
  // TIMESTAMP
  const lastBlock = await kit.web3.eth.getBlockNumber()
  let {timestamp} = await kit.web3.eth.getBlock(lastBlock)
  var timestampObj = new Date(timestamp * 1000)
  // TIME TO LOCK + 10 MINS
  var unlockTime = timestampObj.setMinutes(timestampObj.getMinutes() + 10) / 1000
  // GAS ESTIMATOR
  const gasEstimate = kit.gasEstimate
  // AMMOUNT TO LOCK
  const amount = kit.web3.utils.toWei("0.3", 'ether')
  // ERC20 TO LOCK
  const goldtoken = await kit._web3Contracts.getGoldToken()
  
  // TX OBJECT AND SEND
  const txo = await VaultO.methods.lockTokens(goldtoken._address, process.env.REACT_APP_ADDRESS, amount, unlockTime)
  const tx = await kit.sendTransactionObject(txo, {from: process.env.REACT_APP_ADDRESS, gasPrice: gasEstimate})

  // PRINT TX RESULT
  const receipt = await tx.waitReceipt()
  console.log(receipt)
}

const approve = async () => {
  // MAX ALLOWANCE
  const allowance = kit.web3.utils.toWei('1000000', 'ether')
  // GAS ESTIMATOR
  const gasEstimate = kit.gasEstimate
  // ASSET TO ALLOW
  const goldtoken = await kit._web3Contracts.getGoldToken()
  // TX OBJECT AND SEND
  const approveTxo = await goldtoken.methods.approve(process.env.REACT_APP_VAULT_ADDRESS, allowance)
  const approveTx = await kit.sendTransactionObject(approveTxo, {from: process.env.REACT_APP_ADDRESS, gasPrice: gasEstimate})

  const receipt = await approveTx.waitReceipt()
  // PRINT TX RESULT
  console.log(receipt)
}

const withdraw = async () => {
  const txo = await VaultO.methods.withdrawTokens("12")
  const tx = await kit.sendTransactionObject(txo, {from: process.env.REACT_APP_ADDRESS})
  const receipt = tx.waitReceipt()
  console.log(receipt)
}

function App() {

  const [CELOBal, setCELOBal] = useState(0);
  const [cUSDBal, setcUSDBal] = useState(0);
  const [vaultBal, setVaultBal] = useState(0)
  const [vaultBalAddress, setVaultBalAddress] = useState(0)

  const getBalanceHandle = async () => { 
    const totalLockedBalance = await VaultO.methods.getTokenTotalLockedBalance(process.env.REACT_APP_CELO_TOKEN_ADDRESS).call()
    const totalBalance = await kit.getTotalBalance(process.env.REACT_APP_ADDRESS)

    const {CELO, cUSD} = totalBalance
    setCELOBal(kit.web3.utils.fromWei(CELO.toString()))
    setcUSDBal(kit.web3.utils.fromWei(cUSD.toString()))
    setVaultBal(kit.web3.utils.fromWei(totalLockedBalance.toString()))
  } 

  return (
    <div>
      <h1>ACTIONS</h1>
      <button onClick={approve}>APPROVE</button>
      <button onClick={lock}>LOCK</button>
      <button onClick={withdraw}>WITHDRAW</button>
      <button onClick={getBalanceHandle}>GET BALANCE</button>
      <h1>DATA WALLET</h1>
      <ul>
        <li>
          CELO BALANCE: {CELOBal}
        </li>
        <li>
          cUSD BALANCE: {cUSDBal}
        </li>
      </ul>
      <h1>DATA VAULT SMART CONTRACT</h1>
      <ul>
        <li>
          TOTAL CELO BALANCE: {vaultBal}
        </li>
      </ul>
    </div>
  );
}

export default App;

```

Let's go over our App Component, much fun here I promise...

We will be using useState to display our balances (wallet and vault contract), contractKit will help us interact with the Celo Blockchain effectively and efficiently using web3 under the hood. Envrioment variables are present, see the use of dotenv. Finally we import our json representing the contract (recently I learned this json with bytecode, abi its called contract/truffle artifact)

```
import React, { useState } from 'react';
import { newKit } from '@celo/contractkit'
import dotenv from 'dotenv'
import Vault from './contract/Vault.json'

// LOAD ENV VAR
dotenv.config()
```

Next, we initialize our instance of contractKit, using our DataHub Node URL, we also add to the kit our test account using its private key. Finanly the Contract Object is instanciated for later use.
```
const kit = newKit(process.env.REACT_APP_DATAHUB_NODE_URL)
const connectAccount = kit.addAccount(process.env.REACT_APP_PRIVATE_KEY)

// CONTRACT INSTANCE
const VaultO = new kit.web3.eth.Contract(Vault.abi, process.env.REACT_APP_VAULT_ADDRESS)
```

Before we can interact with our new Vault Smart Contract, we need to approve the use of this smart contract by our wallet, setting an `allowance`.
The approve function creates and sends a `Transaction Object` indicating we are approving, though also setting a max allowance for this smart contract to use. After we `console.log` the receipt
```
const approve = async () => {
  // MAX ALLOWANCE
  const allowance = kit.web3.utils.toWei('1000000', 'ether')
  // GAS ESTIMATOR
  const gasEstimate = kit.gasEstimate
  // ASSET TO ALLOW
  const goldtoken = await kit._web3Contracts.getGoldToken()
  // TX OBJECT AND SEND
  const approveTxo = await goldtoken.methods.approve(process.env.REACT_APP_VAULT_ADDRESS, allowance)
  const approveTx = await kit.sendTransactionObject(approveTxo, {from: process.env.REACT_APP_ADDRESS, gasPrice: gasEstimate})

  const receipt = await approveTx.waitReceipt()
  // PRINT TX RESULT
  console.log(receipt)
}
```

`
If everything went according to plan, we should see the following logs printed on the js console of our react project:
```
{blockHash: "0x2b7bc8c0435fca4dcdbd8de532bd6f8cfdec554e9cf7797bed7081022e86cf8a", blockNumber: 4664409, contractAddress: null, cumulativeGasUsed: 26802, from: "0x5987ffa26150c13caa37d631659db503dfbaf283", …}blockHash: "0x2b7bc8c0435fca4dcdbd8de532bd6f8cfdec554e9cf7797bed7081022e86cf8a"blockNumber: 4664409contractAddress: nullcumulativeGasUsed: 26802events: {Approval: {…}}from: "0x5987ffa26150c13caa37d631659db503dfbaf283"gasUsed: 26802logsBloom: "0x00000000000000000000010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000004000001000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000020000000000000000000004000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000000000000010000000000000010000000000000000100000000000000000000020000000"status: trueto: "0xf194afdf50b03e69bd7d057c1aa9e10c9954e4c9"transactionHash: "0xaed96e7e5c8b392785f39613420d1cde026a67dc57c915334902c0f7760f152d"transactionIndex: 0__proto__: Object

{blockHash: "0xf35037968147a2af98b2dde361bece175739fd0ef21d558106f5866c4591fa1a", blockNumber: 4664410, contractAddress: null, cumulativeGasUsed: 396134, from: "0x5987ffa26150c13caa37d631659db503dfbaf283", …}
blockHash: "0xf35037968147a2af98b2dde361bece175739fd0ef21d558106f5866c4591fa1a"
blockNumber: 4664410
contractAddress: null
cumulativeGasUsed: 396134
events: {0: {…}}
from: "0x5987ffa26150c13caa37d631659db503dfbaf283"
gasUsed: 197422
logsBloom: "0x00000000000000000000010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000008000000004000001000000000000000000000000000000000000000000000000000000000000000000000000000000010000000004000000000000000000000000000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000003000000000000000000000000000000000000000000000000000000000000000000000000010000000000000000100000000000000000000020000000"
status: true
to: "0xb017ad96e31b43afb670dab020561da8e2154c5b"
transactionHash: "0x7151531afbcde553d9d858ffb4af9c01355bbac6e2507449f5f1d94c3e5c1879"
transactionIndex: 1
__proto__: Object
```

Let's take a look at the lock async function which follows. 
Here, we get our unlock timestamp (10 minutes after transaction is sent), we estimate gas, specify we will lock 1 celo, instanciate our contract object using it's abi and address. Our transaction object (txo) will use the `lockTokens` method available in our contract object, and pass our collected / required paremeters. Finally the transaction object will be included in a new transaction (tx).

We await our receipt after, and console.log it.
```
const lock = async () => {
  // TIMESTAMP
  const lastBlock = await kit.web3.eth.getBlockNumber()
  const {timestamp} = await kit.web3.eth.getBlock(lastBlock)
  const timestampObj = new Date(timestamp * 1000)
  // TIME TO LOCK + 10 MINS
  const unlockTime = timestampObj.setMinutes(timestampObj.getMinutes() + 10) / 1000
  // GAS ESTIMATOR
  const gasEstimate = kit.gasEstimate
  // AMMOUNT TO LOCK
  const amount = kit.web3.utils.toWei("1", 'ether')
  // ERC20 TO LOCK
  const goldtoken = await kit._web3Contracts.getGoldToken()
  // CONTRACT INSTANCE
  const VaultO = new kit.web3.eth.Contract(Vault.abi, process.env.REACT_APP_VAULT_ADDRESS)
  // TX OBJECT AND SEND
  const txo = await VaultO.methods.lockTokens(goldtoken._address, process.env.REACT_APP_ADDRESS, amount, unlockTime)
  const tx = await kit.sendTransactionObject(txo, {from: process.env.REACT_APP_ADDRESS, gasPrice: gasEstimate})

  // PRINT TX RESULT
  const receipt = await tx.waitReceipt()
  console.log(receipt)
}
```

Our next stop is the withdraw function, at this time we wont be covering it as we recently discovered a bug possibly in our the Vault Smart Contract pretenting us from withdrawing. Really happy to further investigate, sorry we have to end here for now.

The code repository is here:
[Link](https://github.com/helpicelo/vault-dapp)

## Conclusion

This tutorial was aimed to provide a barebone implementation of a dapp in the Celo Ecosystem. We covered the Vault Smart Contract development and deployment, together with the bootstrapping of a React Application to interact with it's basic functions (approve, lock, withdraw). We hope to continue extending this documentation.

The tutorial was a team effort by [Celo Helpi](https://github.com/helpicelo).
