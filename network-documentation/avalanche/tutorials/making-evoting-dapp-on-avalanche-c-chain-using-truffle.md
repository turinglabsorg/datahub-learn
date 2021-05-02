---
description: Learn how to use Truffle with the C-Chain
---

# Setting up our Truffle project

[**The original tutorial can be found in the AVA Labs documentation here**](https://docs.avax.network/build/tutorials/smart-contracts/using-truffle-with-the-avalanche-c-chain). 

## Introduction

[Truffle Suite](https://www.trufflesuite.com) is a toolkit for launching decentralized applications \(dapps\) on the EVM. With Truffle you can write and compile smart contracts, build artifacts, run migrations and interact with deployed contracts. This tutorial illustrates how Truffle can be used with Avalanche's C-Chain, which is an instance of the EVM.

## Requirements

You've created an [Avalanche DataHub](https://datahub.figment.io/sign_up?service=avalanche) account and are familiar with [Avalanche's architecture](https://docs.avax.network/learn/platform-overview). You've also performed a cross-chain swap via the [Transfer AVAX Between X-Chain and C-Chain](https://docs.avax.network/build/tutorials/platform/transfer-avax-between-x-chain-and-c-chain) tutorial to get funds to your C-Chain address.

## Dependencies

* [NodeJS](https://nodejs.org/en) v8.9.4 or later.
* Truffle, which you can install with `npm install -g truffle`
* Express.js, dotenv and truffle-hdwallet-provider

## Create truffle directory and install dependencies

Open a new terminal tab to so we can create a `evoting` directory and install some further dependencies.

First, navigate to the directory within which you intend to create your `evoting` working directory:

```javascript
cd /path/to/directory
```

Create and enter a new directory named `evoting`:

```javascript
mkdir evoting; cd evoting
```

Use `npm` to install other dependencies

```text
npm install express dotenv truffle-hdwallet-provider
```

Lastly, create a boilerplace truffle project:

```text
truffle init
```

## Update truffle-config.js

One of the files created when you ran `truffle init` is `truffle-config.js`. Replace all of it with the following.

```javascript
require('dotenv').config();
const HDWalletProvider = require("truffle-hdwallet-provider");

//Account credentials from which our contract will be deployed
const mnemonic = process.env.MNEMONICS;

//API key of your Datahub account for Avalanche Fuji test network
const APIKEY = process.env.APIKEY;

module.exports = {
    fuji: {
      provider: function() {
            return new HDWalletProvider(mnemonic, `https://avalanche--fuji--rpc.datahub.figment.io/apikey/${APIKEY}/ext/bc/C/rpc`)
      },
      network_id: "*",
      gas: 3000000,
      gasPrice: 470000000000
    }
  },
  solc: {
    optimizer: {
      enabled: true,
      runs: 200
    }
  }
}
```

Note that we're setting the `gasPrice` and `gas` to the appropriate values for the Avalanche C-Chain.

## Add .env file

* First of all, we need to create an account on Avalanche network. Please visit [Avalanche Wallet](https://wallet.avax.network/) to create your account and save your mnemonics in the .env file.

* Now copy your Datahub's Avalanche Fuji testnet API key in the .env file as shown below.

* Never share or commit your `.env` file. It contains your credentials like `mnemonics` and `API` key.

```javascript
MNEMONIC="<avalanche-wallet-mnemonic>"
APIKEY=<your-api-key>
```

## Add Election.sol

In the `contracts` directory add a new file called `Election.sol` and add the following block of code:

```javascript
pragma solidity >=0.4.21 <0.6.0;

contract Election {
  string public candidate;

  //Structure of candidate standing in the election
  struct Candidate {
    uint id;
    string name;
    uint voteCount;
  }

  //Storing candidates in a map
  mapping(uint => Candidate) public candidates;

  //Storing address of those voters who already voted
  mapping(address => bool) public voters;

  //Number of candidates in standing in the election
  uint public candidatesCount;

  //Adding 2 candidates during the deployment of contract
  constructor () public {
    addCandidate("Candidate 1");
    addCandidate("Candidate 2");
  }

  //Private function to add a candidate
  function addCandidate (string memory _name) private {
    candidatesCount ++;
    candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
  }

  //Public vote function for voting a candidate
  function vote (uint _candidate) public {
    require(!voters[msg.sender], "Voter has already Voted!");
    require(_candidate <= candidatesCount && _candidate >= 1, "Invalid candidate to Vote!");
    voters[msg.sender] = true;
    candidates[_candidate].voteCount++;
  }
}
```

`Election` is a solidity smart contract which lets us view candidates standing in an election and voting them. This is a basic contract for election and we will advance to more sophisticated smart contract, in which we can even create new elections, add new candidates etc. in the next tutorial of this series.

## Add new migration

Create a new file in the `migrations` directory named `2_deploy_contracts.js`, and add the following block of code. This handles deploying the `Election` smart contract to the blockchain.

```javascript
const Election = artifacts.require("Election");

module.exports = function (deployer) {
  deployer.deploy(Election);
};
```

## Compile Contracts with Truffle

Any time you make a change to `Election.sol` you need to run `truffle compile`.

```text
truffle compile
```

You should see:

```javascript
Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/Election.sol
> Artifacts written to /path/to/build/contracts
> Compiled successfully using:
   - solc: 0.5.16+commit.9c3226ce.Emscripten.clang
```

## Fund the account and run migrations on the C-Chain

When deploying smart contracts to the C-Chain, it will require some deployment cost. As you can see inside `truffle-config.js`, HDWallet Provider will help us in deploying on Fuji C-chain and deployment cost will be managed by account whose mnemonic has been stored in the `.env` file. Therefore we need to fund the account.

### Fund your account

Follow the steps in the [Transfer AVAX Between X-Chain and C-Chain]() tutorial to fund the newly created account. You'll need to send at least `135422040` nAVAX to the account to cover the cost of contract deployments.

## Run Migrations

Now everything is in place to run migrations and deploy the `Election` contract:

```javascript
truffle migrate --network fuji
```

You should see:

```javascript
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.

Migrations dry-run (simulation)
===============================
> Network name:    'development-fork'
> Network id:      1
> Block gas limit: 99804786 (0x5f2e672)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > block number:        4
   > block timestamp:     1607734632
   > account:             0x34Cb796d4D6A3e7F41c4465C65b9056Fe2D3B8fD
   > balance:             1000.91683679
   > gas used:            176943 (0x2b32f)
   > gas price:           470 gwei
   > value sent:          0 ETH
   > total cost:          0.08316321 ETH

   -------------------------------------
   > Total cost:          0.08316321 ETH

2_deploy_contracts.js
=====================

   Deploying 'Election'
   -------------------
   > block number:        6
   > block timestamp:     1607734633
   > account:             0x34Cb796d4D6A3e7F41c4465C65b9056Fe2D3B8fD
   > balance:             1000.8587791
   > gas used:            96189 (0x177bd)
   > gas price:           470 gwei
   > value sent:          0 ETH
   > total cost:          0.04520883 ETH

   -------------------------------------
   > Total cost:          0.04520883 ETH

Summary
=======
> Total deployments:   2
> Final cost:          0.13542204 ETH
```

If you didn't create an account on the C-Chain you'll see this error:

```javascript
Error: Expected parameter 'from' not passed to function.
```

If you didn't fund the account, you'll see this error:

```javascript
Error:  *** Deployment Failed ***

"Migrations" could not deploy due to insufficient funds
   * Account:  0x090172CD36e9f4906Af17B2C36D662E69f162282
   * Balance:  0 wei
   * Message:  sender doesn't have enough funds to send tx. The upfront cost is: 1410000000000000000 and the sender's account only has: 0
   * Try:
      + Using an adequately funded account
```

The information and ABI of the deployed contract is present in the `/build/contract` directory as `Election.json`. Inforamation like contract address, network info etc. could be found here.

## Building user interface for interacting with the blockchain

* Make `src` directory where we will keep all our `web2` files, for interacting with the blockchain.

* Go to the `src` directory using `cd src`

* Make a new file `server.js` . Put the following code inside the file.

```javascript
var express = require('express');
var app = express();

//JSON file for deployed contract and network information
const electionJSON = require('../build/contracts/Election.json')

require("dotenv").config();

app.use(express.static("./"));

app.get('/', (req,res) => {
    res.send('index.html');
});

app.get('/electionJSON', (req,res) => {
    res.send(electionJSON);
});

app.listen(process.env.PORT || 3000, () => {
    console.log('Server started at 3000');
});
```

* Now make new file `index.html` and put the following code inside the file.

```html
<!DOCTYPE html>

<html lang="en">
  <head>
    <title>Election</title>
  </head>

  <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" rel="stylesheet">

  <body>
    <div style="width: 40%; margin: 50px auto" class="card">
            <!-- Account address will be rendered here -->
            <center id="account" style="margin-top: 20px"></center>

            <!-- Loading will appear until blockchain data is loaded -->
            <center id='loader' style="margin:20px;">Loading...</center>
            
            <br><br>
            
            <!-- Blockchain data would appear here -->
            <div id="content" style="display:none" class="container" style="margin-top:30px;">
                <!-- Table for fetching election data of the candidates -->
                <table class="table table-bordered">
                    <tr>
                        <td>#</td>
                        <td>Name</td>
                        <td>Votes</td>
                    </tr>
                    <tbody id="candidateResults">
    
                    </tbody>
                </table>
    
                <!-- Form to submit vote to a candidate -->
                <form onSubmit="App.castVote(); return false;" style="display:none">
                    <div class="form-group">
                        <label>Select Candidate</label>
                        <center>
                            <select class="form-control" id="candidatesSelect">
                                <option>Select here...</option>
                            </select><br>
                            <input type="submit" class="btn btn-primary" value="Vote">
                        </center>
                    </div>
                </form>
    
                <!-- This would appear and form will be hidden if the address has already voted -->
                <div id="hasVoted" style="display:none; text-align: center">
                    <b>Thank you for voting !!!</b>
                </div>
            </div>
        </div>
  </body>

  <!--jQuery CDN-->
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
  
  <!--web3 module for interacting with blockchain-->
  <script language="javascript" type="text/javascript" src="https://cdn.jsdelivr.net/gh/ethereum/web3.js@1.0.0-beta.34/dist/web3.js"></script>

  <!--Truffle Contract module for interacting with smart contract in javascript-->
  <script src="https://cdn.jsdelivr.net/gh/rajranjan0608/ethereum-electionVoting/src/contract.js"></script>

  <!--Our custom javascript code for interaction-->
  <script type="module" language="javascript" src="index.js"></script>
</html>
```

* Now make new file `index.js` and put the following code inside the file. The code is well commented for you to understand.

```javascript
//App would contain all the necessary functions for interaction
var App = {
  loading: false,
  contracts: {},

  //Main function to be called first
  load: async () => {
    await App.loadWeb3();
    await App.loadAccount(); 
    await App.loadContract();
    await App.render();
  },

  //Loading web3 on the browser
  loadWeb3: async () => {
    if(typeof web3 !== 'undefined') {
      web3 = new Web3(web3.currentProvider);
      App.web3Provider = web3.currentProvider;
    }else {
      window.alert("Please connect to Metamask");
    }

    if(window.ethereum) {
      window.web3 = new Web3(ethereum);
      try {
        await ethereum.enable();
      }catch (error) {
        console.log(error);
      }
    }else if(window.web3) {
      App.web3Provider = web3.currentProvider;
      window.web3 = new Web3(web3.currentProvider);
    }else{
      console.log('Non-Ethereum Browser detected');
    }
  },

  //This function would load account from Metamask to our dApp
  loadAccount: async() => {
    await web3.eth.getAccounts().then((result)=>{
      App.account = result[0];
      console.log(App.account);
    });
  },

  //This function would help in loading contract to App.election
  loadContract: async () => {
    //Static pre-deployed contracts should be handled like this
    const election = await $.getJSON('/electionJSON');
    App.contracts.election = TruffleContract(election);
    App.contracts.election.setProvider(App.web3Provider);
    App.election = await App.contracts.election.deployed();
  },

  //This function will be called after the browser is ready for blockchain interaction
  render: async() => {
    if(App.loading) {
      return;
    }
    App.setLoading(true);
    $('#account').html(App.account);
    App.renderCandidates();
    App.setLoading(false);
  },

  //This will render blockchain data to the frontend.
  renderCandidates: async() => {
    var candidatesCount = await App.election.candidatesCount();

    $("#candidateResults").html("");
    $("#candidatesSelect").html("");
      
    for(var i=1; i <= candidatesCount; i++) {
      const candidate = await App.election.candidates(i);

      const id = candidate[0];
      const name = candidate[1];
      const voteCount = candidate[2];

      var candidateTemplate1 = "<tr>"+
                                  "<td>" + id + "</td>" +
                                  "<td>" + name + "</td>" +
                                  "<td>" + voteCount + "</td>" +
                              "</tr>";      
      $("#candidateResults").append(candidateTemplate1);

      var hasVoted = await App.election.voters(App.account);
      if(!hasVoted) {
        $("form").show();
        $("#hasVoted").hide();
      }else {
        $("#hasVoted").show();
        $("form").hide();
      }

      var candidateTemplate2 = "<option value='"+i+"'>" + name + "</option>";
      $("#candidatesSelect").append(candidateTemplate2);
    }
  },

  //This function will call vote() on Fuji testnet
  castVote: async() => {
    const candidateID = $("#candidatesSelect").val();
    await App.election.vote(candidateID, { from: App.account });
    App.renderCandidates();
  },

  setLoading: (boolean) => {
    App.loading = boolean;
    const loader = $('#loader');
    const content = $('#content');
    if(boolean) {
      loader.show();
      content.hide();
    }else {
      loader.hide();
      content.show();
    }
  }
};

//Driver function to initiate the blockchain interaction
$(() => {
  window.addEventListener('load', ()=>{
    App.load();
  });
});

window.App = App;
```

* Now run `node server.js` in the `src` directory. This should run till you want to interact with the contract. Your output would look like this 
```
Server started at 3000
```

* Visit http://localhost:3000 to interact with built dApp.

![](https://j.gifs.com/A6qrxj.gif)

* Don't forget to setup Metamask with `Fuji` testnet and also fund the account with Fuji c-chain test tokens in order to vote. Please refer to this tutorial on [Connecting Datahub to Metamask](https://learn.figment.io/network-documentation/avalanche/tutorials/connect-datahub-to-metamask).

## Congratulations!

You have successfully built a full fledged `dApp` and deployed the smart contract on `Fuji` test network using `Trufflesuite`. 
  
If you had any difficulties following this tutorial or simply want to discuss Avalanche tech with us you can [**join our community today**](https://discord.gg/fszyM7K)!
