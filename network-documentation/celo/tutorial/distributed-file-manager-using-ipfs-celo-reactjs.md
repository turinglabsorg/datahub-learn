# Distributed File Manager (DFM) using IPFS, Celo and ReactJS

## Introduction

In this tutorial we will be making a **Distributed File Manager** using the **IPFS** protocol for storing our files, **Celo** network for storing the file references of each address to their uploaded files and **ReactJS** for building the client side application. For compiling and deploying our smart contracts, we will be using **Truffle Suite**.

For your information, [Truffle Suite](https://www.trufflesuite.com) is a toolkit for launching decentralized applications dApps on the EVM. With Truffle you can write and compile smart contracts, build artifacts, run migrations and interact with deployed contracts. This tutorial illustrates how Truffle can be used with Celo network, which is an instance of the EVM.

## Prerequisites

* Basic familarity with [Celo's architecture](https://docs.celo.org/) and smart contracts.
* Basic familarity with [ReactJS](https://reactjs.org)

## Requirements

* [NodeJS](https://nodejs.org/en). You’ll need to have Node >= 10.16 and npm >= 5.6 on your machine.
* [Truffle](https://www.trufflesuite.com/truffle), which you can install with `npm install -g truffle`
* Metamask extension added to the browser, which you can add from [here](https://metamask.io/download.html)

## Initializing the working directory

Our application's client-side is made using **ReactJS**. Smart contracts will be made using **Solidity** language and will be deployed on the **Celo** network with **Trufflesuite**. Therefore, we need to set up our working directory according to ReactJS and Trufflesuite, for making our development process smoother.

Open the terminal and navigate to the directory where you intend to create your application.
```bash
cd /path/to/directory
```

### **Setting up the ReactJS project**
Create a new react app using npx. npx is a npm package runner (x probably stands for eXecute). The typical use is to download and run a package temporarily or for trials.
```bash
npx create-react-app dfm-celo-react
```

Move to the newly created directory and install the basic dependencies.
```bash
cd dfm-celo-react
npm install --save web3 ethers @truffle/contract @celo/contractkit
```
Have a look at the project structure. As you can see, there are lots of files in our project. To clear the clutter, let us first remove the unwanted files from `src` and `public` directory.

Remove the contents of `src` and `public` folder.
```bash
rm src/*
rm public/*
```
After running the above commands, your project structure would look like the image shown below.

![](https://imgur.com/gyDzKtJ.png)

Make a `index.html` file in `public` folder of current directory and put the following `HTML` code.
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Distributed File Manager</title>
</head>

<link  rel="stylesheet"  href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css">

<body>
    <div id="root"></div>
</body>

</html>
```
Move out of the public folder and make a file named `App.js` inside `src` folder and put the following code inside.
```javascript
import  React  from  'react';

// 1. Importing other modules

class  App  extends  React.Component {
	constructor(props) {
		super(props);
		this.state = {
			web3:  null,
			account:  null,
			contract:  null
		}
	}

	componentDidMount() {
		this.init();
	}

	async  init() {
		// 2. Load web3

		// 3. Load Account

		// 4. Load Smart-Contract instance
	}

	render() {
		return (
			<div>
				Distributed File Manager
				{/* 5. Navbar */}
				
				{/* 6. IPFS Viewer component */}

				{/* 7. IPFS Uploader component */}
			</div>

		)
	}
}
export  default  App;
```
This `App` component will maintain a state with `web3` instance of the `Metamask` provider for interacting with the Celo network, `account` address and instance of the deployed smart contract.

Make a new file named `index.js` inside the same `src` folder, with the following code inside it.
```javascript
import  React  from  'react';
import  ReactDOM  from  'react-dom';
import  App  from  './App';

ReactDOM.render(
	<React.StrictMode>
		<App/>
	</React.StrictMode>,
	document.getElementById('root')
);
```

React project setup is now complete and your folder structure should look like this.

![](https://imgur.com/G29oiUW.png)

### **Setting up the Truffle project**

Run the following command in the `root`  directory, to create a boilerplate for the `Truffle` project. This will set up Truffle's initial project structure. Smart contracts will be stored in the `contracts` folder, deployment functions for migrating smart contracts to the network will be stored in the `migrations` folder. By default, `/build/contracts` folder would contain information about the compiled and deployed contract, ABI etc in the `.json` format and these files are known as `artifiacts`.
```bash
truffle init
```
There is also 1 **config** file created by the above command, which is, **truffle-config.js**. In this file, there is a lot of information regarding how to deploy contracts, how to choose a network to deploy them, and many others. Therefore, we should preserve this file for reference. So, use the below command to make a copy of this file. This would create a copy named `truffle-config-default.js`.
```bash
cp truffle-config.js truffle-config-default.js
```
Now update the `truffle-config.js` file, with the following code to deploy the smart contract on the `Celo's Alfajores` network. This file would help us in connecting to the remote node of Celo Alfajores network and hence would be using your Celo account's mnemonic for deploying the contract on the network.

```javascript
const Web3 = require('web3');
const ethers = require('ethers');
const ContractKit = require('@celo/contractkit');
require('dotenv').config();

const RPCURL = `https://alfajores-forno.celo-testnet.org`;
const web3 = new Web3(RPCURL);
const kit = ContractKit.newKitFromWeb3(web3);

async function awaitWrapper(){
  let account = ethers.Wallet.fromMnemonic(process.env.MNEMONIC, "m/44'/52752'/0'/0/0")
  let celoToken = await kit.contracts.getGoldToken();

  let celoBalance = await celoToken.balanceOf(account.address);
  console.log("Account address: ", account.address);
  console.log(`CELO Balance: ${celoBalance / (10**18)}`);
  
  if(celoBalance / (10**18) < 0.4) {
    console.log("Balance too low to deploy contracts. Please fund your account here at https://celo.org/developers/faucet\n");
  }
  kit.connection.addAccount(account.privateKey)
}

awaitWrapper();

module.exports = {
  contracts_build_directory: './src/build/contracts'
  networks: {
    development: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*"
    },
    alfajores: {
      provider: kit.connection.web3.currentProvider,
      network_id: 44787
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
Here, you can see that, we have used `contracts_build_directory` to change defaut location of `artificats` from `root` directory to the `src` folder. This is because, React cannot access files which are present outside the `src` folder.

### **Get Celo credentials**
For deploying smart contracts we need two things viz. A node connected to the **Celo** network and an account with few **CELO**. The good news is, **Celo** provides a remote node, which is already connected to the network. Its RPC url is https://alfajores-forno.celo-testnet.org and all our blockchain requests would go there, and this node would make transactions on the **Celo** network.

Now we need a **Celo** wallet, where we would keep our funds, required for all the transactions on the network. So, visit [here](https://celowallet.app/) and create an account. While creating your account you will see your public key and mnemonic. This public address will be required to transfer funds. Also, save the **mnemonic** in a secure place (we would need it later). Instructions to add funds will be provided later in the tutorial.

### **Add .env file**

Create a `.env` file in the `root` folder. Please take a note that dot (.) is necessary for the `.env` file name. Now copy your Celo wallet's mnemonic in the .env file as shown below. In the .env file, **MNEMONIC** should be enclosed within double-quotes (" ").

```javascript
MNEMONIC="<avalanche-wallet-mnemonic>"
```

{% hint style="warning" %} Never share or commit your `.env` file. It contains your credentials like `mnemonics`. Therefore, it is advised to add `.env` to your `.gitignore` file. {% endhint %}

Our project setup is now complete and the folder structure should look like this.

![](https://imgur.com/eRatMMO.png)

To confirm that we are on the same page, run the following command in the `root` folder.

```bash
npm start
```
It might take few seconds, to show an output as in the image below.

![](https://imgur.com/VV0park.png)

Now go to your browser, and visit the URL - http://localhost:3000 If your followed the above steps, you would see the page as shown below. Background theme of the website is dark, so you have to look carefully for text in black color.

![](https://imgur.com/hVr1ElD.png)

### **Create `FileManager.sol` smart contract**

In the `contracts` directory add a new file called `FileManager.sol` and add the following block of code:

```solidity
pragma solidity >=0.4.21 <0.6.0;
pragma experimental ABIEncoderV2;

contract FileManager {
	// Structure of each File
	struct File {
		string fileName;
		string fileType;
		string cid;
	}

	// Mapping of each user's address with the array of files they are storing
	mapping(address => File[]) files;

	function addFile(string[] memory _fileInfo, string  memory _cid) public {
		files[msg.sender].push(File(_fileInfo[0], _fileInfo[1], _cid));
	}

	function getFiles(address _account) public  view  returns (File[] memory) {
		return files[_account];
	}
}
```

`FileManager` is a solidity smart contract which lets us store and view the meta details of file which we upload on the `IPFS` network. IPFS uses content addressing rather than location addressing. In order to identify files on the network, IPFS uses a cryptographic hash for each file. This hash is known as `content identifier` or `cid`. Whatever the size of the file, the length of this hash would be same, and is enough to identify every file uniquely. After uploading the file to IPFS, we get a `cid` which acts as a reference to that file on the network, and we store this `cid` on the Celo blockchain.

For example, let us say, we upload an image file named `dark-forest.jpg` on IPFS. Now IPFS protocol would generate a unique hash, called as `cid` or `contend id` which will reference this file on the network. A `cid` would look something like this `QmVwyUH96NeQPwLN5jDkgNxM41xGCB1EVjnBYX7NoWWmKH`. So this file can be accessed using any IPFS provider like **Infura** like this https://ipfs.infura.io/ipfs/QmVwyUH96NeQPwLN5jDkgNxM41xGCB1EVjnBYX7NoWWmKH. If we upload the same file again, then it would generate the same hash and there would be no redundancy.

### **Let's understand this smart contract**

The code for smart contract is everything within `contract FileManager {  }`.

**Basic structure about Files** - `File` is a struct which is basically a skeleton to store the details of each file. We are having three attributes of each file viz. `fileName`, `fileType` i.e. wether it is image, audio, video or an application and finally `cid`. Here, `files` is a mapping between the owner (address) of the files and the array of those `File` structures which they uploaded.

```solidity
// Structure of each File
struct  File {
	string fileName;
	string fileType;
	string cid;
}

// Mapping of each user's address with the array of files they are storing
mapping(address => File[]) files;
```

**Adding files** - `addFile()` function is used to add details of file to the array of `File` structures corresponding to each address. `files[msg.sender]` refers to the array of file structures, belonging to the caller of this function i.e. address `msg.sender`. Function's arguments are `_fileInfo[]` which is array of 2 parameters (file name and file type respectively) and second argument is `cid` which is the content id for the uploaded file.

```solidity
function  addFile(string[] memory _fileInfo, string  memory _cid) public {
	files[msg.sender].push(File(_fileInfo[0], _fileInfo[1], _cid));
}
```

**Viewing stored files** - `getFiles()` is a function which returns the array of file structures corresponding to the account address. It return the details of all the files as an array that is being uploaded by the address (passed in the argument of this function) on IPFS network.

```solidity
function  getFiles(address _account) public  view  returns (File[] memory) {
	return files[_account];
}
```

### **Make a new file for migrating smart contracts**

Create a new file in the `migrations` directory named `2_deploy_contracts.js`, and add the following block of code. This handles deploying the `FileManager` smart contract to the blockchain.

```solidity
const FileManager = artifacts.require("FileManager");

module.exports = function(deployer) {
	deployer.deploy(FileManager);
};
```
### **Compile Contracts with Truffle**

Any time you want deploy smart contracts, and you have made changes in them, you need to run `truffle compile`.

```bash
truffle compile
```

You should see:

```javascript
Compiling your contracts...
===========================
> Compiling ./contracts/FileManager.sol
> Compiling ./contracts/Migrations.sol

> Artifacts written to /home/guest/blockchain/dfm-celo-react/src/build/contracts
> Compiled successfully using:
   - solc: 0.5.16+commit.9c3226ce.Emscripten.clang
```

{% hint style="warning" %} There might be an error `Error: Cannot find module 'pify'`, if the `pify` module is not installed automatically while installing `truffle`. So, this issue can be resolved by separately installing `pify`, using the command below  {% endhint %}

```
npm install pify --save
```

Compiling the smart contracts would create `.json` file in the `src/build/contracts` directory. It stores `ABI` and other necessary metadata. 

{% hint style="info" %} `ABI` refers to Application Binary Interface, which is a standard for interacting with the smart contracts from outside the blockchain as well as contract-to-contract interaction. Please refer to the Solidity's documentation about ABI's [here](https://docs.soliditylang.org/en/v0.5.3/abi-spec.html#:~:text=The%20Contract%20Application%20Binary%20Interface,contract%2Dto%2Dcontract%20interaction.&text=This%20specification%20does%20not%20address,known%20only%20at%20run%2Dtime) to learn more. {% endhint %}

### **Fund the account and run migrations on the Celo's Alfjores test network.**

When deploying smart contracts to the `Celo` network, it will require some deployment cost. As you can see inside `truffle-config.js`, `@celo/contractkit` will help us in deploying on Celo and deployment cost will be managed by account whose mnemonic has been stored in the `.env` file. Therefore we need to fund the account.

#### **Fund your account**

Fund your account using the the faucet link https://celo.org/developers/faucet and pasting your Celo's wallet address in the input field. You'll need to send at least `0.4 CELO`. Minimum CELO required for deployment, will vary from contract to contract, depending upon what variables and data structures our contract is using. Though funding through faucet would give you enough `CELO` to run multiple deployments and transactions on the network.

### **Run Migrations**

Now everything is in place to run migrations and deploy the `FileManager`:

```javascript
truffle migrate --network alfajores
```

This might take a while depending upon your internet connection or traffic on the CELO network.

Note - For development purpose, we may deploy our contracts on local network, by running Ganache (Truffle's local blockchain simulation) and using the command 

```bash
truffle migrate --network development
```

On successfull execution of this command, you should see:

```javascript
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.

Account address:  0x73c4c24db1662caA1f1Cc7FAfc035b06fbd3DDcb
CELO Balance: 4.11118206


Starting migrations...
======================
> Network name:    'alfajores'
> Network id:      44787
> Block gas limit: 0 (0x0)


1_initial_migration.js
======================

   Replacing 'Migrations'
   ----------------------
   > transaction hash:    0x77b8e8c431e9de6a6880921a7edcf4c686ed15d5d6db39ef81402f0363c32c88
   > Blocks: 0            Seconds: 0
   > contract address:    0x13fE0e50a923E237E14148CfcdbeAcDBbB35c7CB
   > block number:        5316318
   > block timestamp:     1621935852
   > account:             0x73c4c24db1662caA1f1Cc7FAfc035b06fbd3DDcb
   > balance:             4.10812426
   > gas used:            152890 (0x2553a)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.0030578 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:           0.0030578 ETH


2_deploy_contracts.js
=====================

   Replacing 'FileManager'
   -----------------------
   > transaction hash:    0x903926b78ac2447faa8048fec85ee0947f261b1dc500bbbbea9b75543665cef3
   > Blocks: 0            Seconds: 0
   > contract address:    0x7d8eE7a1Db730Ba01d3aa77EE32A0a28fbd7A934
   > block number:        5316320
   > block timestamp:     1621935862
   > account:             0x73c4c24db1662caA1f1Cc7FAfc035b06fbd3DDcb
   > balance:             4.09661876
   > gas used:            533620 (0x82474)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.0106724 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:           0.0106724 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.0137302 ETH
```

If you didn't create an account on the `CELO` you'll see this error:

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

The information like contract address and ABI of the deployed contract is present in the `src/build/contract` directory as `FileManager.json`.

## Building user interface for interacting with the blockchain

* We have already setup our `React` project directory. Main files for the client side to interact with blockchain is present in the `src` folder.

* So go to the `src` directory using the command `cd src`

* First, we will make few functions to connect our browser with the Celo network. These functions will be kept in a separate file named `BlockchainUtil.js`. So make a file `BlockchainUtil.js` inside the `src` directory and put the following code inside it.

```javascript
import  React  from  'react';
import  Web3  from  'web3'
import  TruffleContract  from  '@truffle/contract';

// For connecting our web application with Metamask Web3 Provider
export  class  GetWeb3  extends  React.Component {
	async  getWeb3() {
		let  web3 = window.web3;
		if (typeof  web3 !== 'undefined') {
			// Setup Web3 Provider
			this.web3Provider = web3.currentProvider;
			this.web3 = new  Web3(web3.currentProvider);
			return  this.web3;
		} else {
			this.isWeb3 = false;
		}
	}
}

// For getting our Smart-Contract's instance to interact with it using javascript
export  class  GetContract  extends  React.Component {
	async  getContract(web3, contractJson) {
		// Setup Contract
		this.contract = await  TruffleContract(contractJson);
		this.contract.setProvider(web3.currentProvider);
		return  await  this.contract.deployed();
	}
}

// For getting our account address from the Metamask
export  class  GetAccount  extends  React.Component {
	async  getAccount(web3) {
		return  await  web3.eth.getAccounts();
	}
}
```

* **Upading `App.js`** - `App.js` is the entry point of any React application. Therefore we need to update `App.js` regularly with the components which we want to show in our application. As we move further, build all components, we will also update `App.js` in the end.

* Now let's make a component which will upload the files from our system to the IPFS network. So, make a file named `IPFSUploader.js` in the `src` directory and put the following code inside it.

```jsx
import  React  from  'react';
import  Compressor  from  'compressorjs';
import { Loader } from  'rimble-ui';

const {create} = require('ipfs-http-client')
const  ipfs = create({ host:  'ipfs.infura.io', port:  5001, protocol:  'https' })

class  IPFSUploader  extends  React.Component {
	constructor(props) {
		super(props)
		this.state = {
			buffer:  null,
			fileName:  null,
			fileType:  null,
			cid:  null,
			account:  this.props.state.account,
			loading:  false,
			loadingReason:  ""
		}
	}

	captureFile = (event) => {
		event.preventDefault()
		const  file = event.target.files[0]
		var  type = file.type.split('/');

		if(type[0] === "image") {
			new  Compressor(file, {
				quality:  0.2,
				success: (compressedResult) => {
					const  reader = new  window.FileReader()
					reader.readAsArrayBuffer(compressedResult);
					reader.onloadend = () => {
						this.setState({
							buffer:  Buffer(reader.result),
							fileName:  file.name,
							fileType:  file.type
						});
					}
				},
			});
		} else {
			const  reader = new  window.FileReader()
			reader.readAsArrayBuffer(file);
			reader.onloadend = () => {
				this.setState({
					buffer:  Buffer(reader.result),
					fileName:  file.name,
					fileType:  file.type
				});
			}
		}
	}

	onSubmit = async (event) => {
		event.preventDefault()
		
		this.setState({loading:  true, loadingReason:  "Uploading to IPFS"})
		const { cid } = await  ipfs.add(this.state.buffer);
		this.setState({cid:  cid.string, loadingReason:  "Waiting for approval"});
		
		try {
			await  this.props.state.contract.addFile([this.state.fileName, this.state.fileType], this.state.cid, {from:  this.props.state.account, gas:  20000000});
		} catch(e) {
			console.log("Transaction failed");
		}
		
		this.setState({loading:  false})
	}
	
	render() {
		return (
			<div>
				<center  style={{margin:  "50px auto"}}>
					<form  onSubmit={this.onSubmit}  >
						<input  className = "text-light bg-dark"  type='file'  onChange={this.captureFile}  /><br/><br/>

						{  this.state.loading ?
							<center>
								<Loader  size = "40px"  color = "white"/><br/>
								<font  size = "2"  color = "white"  style = {{marginTop:  "-10px"}}>{this.state.loadingReason}</font>
							</center>
							:
							<input  className = "btn btn-dark"  type='submit'/> }
					</form>
				</center>
			</div>
		);
	}
}

export  default  IPFSUploader;
```

Let's understand this component block by block.

**`IPFS Client`** - First we need to make a connection to IPFS client using the `ipfs-http-client` module. This has to be done by some IPFS provider like `Infura`. So, the following line would create an IPFS client - 
```javascript
const  ipfs = create({ host:  'ipfs.infura.io', port:  5001, protocol:  'https' })
```

**`state`** - **IPFSUploader** component will maintain a state of file properties like `fileName`, `fileType`, `buffer` of each file, `account` address and `cid` of the uploaded file. These state variables will be updated whenever there is a change in input field of file type.

**`captureFile()`** - This function will be called whenever there is an `onChange` event in the input field. This will update the state with necessary file information. In this function we will be having a `Compressor` instance which will compress the file of `image` type.

**`onSubmit()`** - This function would be called when the user will submit the form containing the file as an input. This function would first invoke the `add()` function of the IPFS client and upload the `buffer` of this file which was previously stored in the `state` of this component. Once the file is uploaded, it will return a `cid`. After that, we will add these file informations along with `cid` to the smart contract using the `addFile()` contract function.

Since we have used new libraries like `compressorjs`, `ipfs-http-client` and `rimble-ui` which we did not install previously, therefore please install these libraries using the below command. 
```bash
npm install --save ipfs-http-client compressorjs rimble-ui
```
{% hint style="info" %} Rimble UI library comes with a peer dependency of `react@16.9.0` which is not the latest version of React that we are using i.e. `react@17.0.2`. Running the `npm install` command without the `--force` tag would cause an `unable to resolve dependency tree` conflict. Thus, the `--force` tag is used to override any type of conflicts and proceeds with the installation anyway. Another way to resolve this conflict is by using the `--legacy-peer-deps` tag instead, but this would ignore all peer dependencies which we do not require, as the conflict is only between `react` and `rimble-ui`. {% endhint %}

Now make a new file named `IPFSViewer.js`. This component would be used to fetch file information from the deployed smart contract and display it on the website. Add the following code inside it.

```jsx
import React from 'react';
import App from './App';
import IPFSViewerCSS from './IPFSViewerCSS.css'

class IPFSViewer extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
          imageFiles: [],
          videoFiles: [],
          applicationFiles: [],
          audioFiles: [],
          otherFiles: []
        }
    }

    app = null

    async componentDidMount() {
      this.app = new App();
      await this.app.init();
      await this.loadFiles();
    }

    loadFiles = async () => {
      const files = await this.app.contract.getFiles(this.app.account[0]);
      var imageFiles = [], videoFiles = [], audioFiles = [], applicationFiles = [], otherFiles = [];
      files.forEach((file) => {
        var type = file[2].split('/');
        if(type[0] === "image") {
          imageFiles.push(file);
        } else if(type[0] === "video") {
          videoFiles.push(file);
        } else if(type[0] === "audio") {
          audioFiles.push(file);
        } else if(type[0] === "application") {
          applicationFiles.push(file);
        } else {
          otherFiles.push(file);
        }
      });
      this.setState({
        imageFiles,
        videoFiles,
        audioFiles,
        applicationFiles,
        otherFiles
      });
    }

    showImageFiles = () => {
        var fileComponent = [];
        this.state.imageFiles.forEach((file) => {
            var fileName;
            if(file[1].length < 12) {
                fileName = file[1];
            } else {
                fileName = file[1].substring(0, 6) + "..." + file[1].substring(file[1].length-8, file[1].length);
            }
            fileComponent.push(
                <a href = {`https://ipfs.infura.io/ipfs/${file[3]}`}>
                    <img alt = {fileName} src = {`https://ipfs.infura.io/ipfs/${file[3]}`} style = {{margin: "5px", width: "200px", height: "150px", border: "solid white 2px", borderRadius: "5px"}}/>
                    <center>{fileName}</center>
                </a>
            )
        });
        return fileComponent;
    }

    showVideoFiles = () => {
        var fileComponent = [];
        this.state.videoFiles.forEach((file) => {
            var fileName;
            if(file[1].length < 12) {
                fileName = file[1];
            } else {
                fileName = file[1].substring(0, 6) + "..." + file[1].substring(file[1].length-8, file[1].length);
            }
            fileComponent.push(
                <div>
                    <video src = {`https://ipfs.infura.io/ipfs/${file[3]}#t=0.1`} controls style = {{margin: "5px", width: "290px", height: "200px", border: "solid white 2px", borderRadius: "5px"}}/>
                    <center>{fileName}</center>
                </div>
            )
        });
        return fileComponent;
    }

    showAudioFiles = () => {
        var fileComponent = [];
        this.state.audioFiles.forEach((file) => {
            var fileName;
            if(file[1].length < 12) {
                fileName = file[1];
            } else {
                fileName = file[1].substring(0, 6) + "..." + file[1].substring(file[1].length-8, file[1].length);
            }
            fileComponent.push(
                <div>
                    <audio src = {`https://ipfs.infura.io/ipfs/${file[3]}#t=0.1`} controls style = {{margin: "10px"}}/>
                    <center>{fileName}</center>
                </div>
            )
        });
        return fileComponent;
    }

    showApplicationFiles = () => {
        var fileComponent = [];
        this.state.applicationFiles.forEach((file) => {
            var fileName;
            if(file[1].length < 12) {
                fileName = file[1];
            } else {
                fileName = file[1].substring(0, 6) + "..." + file[1].substring(file[1].length-8, file[1].length);
            }
            fileComponent.push(
                <div style = {{width: "120px"}}>
                    <center style = {{cursor: "pointer"}} onClick = {() => {window.location.href = `https://ipfs.infura.io/ipfs/${file[3]}`}}>
                        <a href = {`https://ipfs.infura.io/ipfs/${file[3]}`}>
                            <img alt = {fileName} src = {'https://img2.pngio.com/filetype-docs-icon-material-iconset-zhoolego-file-icon-png-256_256.png'} style = {{width: "50px", height: "50px"}}/><br/>
                            {fileName}
                        </a>
                    </center>
                </div>
            )
        });
        return fileComponent;
    }

    showOtherFiles = () => {
        var fileComponent = [];
        this.state.otherFiles.forEach((file) => {
            var fileName;
            if(file[1].length < 12) {
                fileName = file[1];
            } else {
                fileName = file[1].substring(0, 6) + "..." + file[1].substring(file[1].length-8, file[1].length);
            }
            fileComponent.push(
                <div style = {{width: "120px"}}>
                    <center style = {{cursor: "pointer"}}>
                        <a href = {`https://ipfs.infura.io/ipfs/${file[3]}`}>
                            <img alt = {fileName} src = {'https://images.vexels.com/media/users/3/152864/isolated/preview/2e095de08301a57890aad6898ad8ba4c-yellow-circle-question-mark-icon-by-vexels.png'} style = {{width: "50px", height: "50px"}}/><br/>
                            {fileName}
                        </a>
                    </center>
                </div>
            )
        });
        return fileComponent;
    }

    render() {
        var imageFiles = this.showImageFiles(), videoFiles = this.showVideoFiles(), audioFiles = this.showAudioFiles(), applicationFiles = this.showApplicationFiles(), otherFiles = this.showOtherFiles();
        return (
            <div style = {{margin: "20px"}}>
                <b style = {{color: "white"}}>Images</b><br/><br/>
                <div className = {"imageViewer"} style = {{color: "white", height: "200px", display: "flex", overflowX: "scroll"}}>
                    {(imageFiles.length === 0) ? "No files to show" : imageFiles}
                </div>

                <br/><br/>

                <b style = {{color: "white"}}>Videos</b><br/><br/>
                <div className = {"imageViewer"} style = {{color: "white", height: "250px", display: "flex", overflowX: "scroll"}}>
                    {(videoFiles.length === 0) ? "No files to show" : videoFiles}
                </div>

                <br/><br/>

                <b style = {{color: "white"}}>Audio</b><br/><br/>
                <div className = {"imageViewer"} style = {{color: "white", height: "250px", display: "flex", overflowX: "scroll"}}>
                    {(audioFiles.length === 0) ? "No files to show" : audioFiles}
                </div>

                <br/><br/>

                <b style = {{color: "white"}}>Applications</b><br/><br/>
                <div className = {"imageViewer"} style = {{color: "white", height: "150px", display: "flex", overflowX: "scroll"}}>
                    {(applicationFiles.length === 0) ? "No files to show" : applicationFiles}
                </div>

                <br/><br/>

                <b style = {{color: "white"}}>Others</b><br/><br/>
                <div className = {"imageViewer"} style = {{color: "white", height: "150px", display: "flex", overflowX: "scroll"}}>
                    {(otherFiles.length === 0) ? "No files to show" : otherFiles}
                </div>
            </div>
        )
    }
}

export default IPFSViewer;
```
Let's understand the above component block by block.

**`state`** - The state of this component would contain array of different types of files like `imageFiles`, `audioFiles` etc.

**`loadFiles()`** - This function would be called after the component is mounted and would load the state with all the files which are uploaded by the account address Using the `getFiles()` function of the smart contract, it can easily fetch all file informations from the blockchain. It will separate the different types of files like images, videos, audios etc. accordingly and update the state. 

**`showImageFiles()`** - This function would return components with all image files composed with proper `img` tag, as an array, to the caller of this function.

Similarly, there are different functions for each file type.

`IPFSViewerCSS.css` file has been imported to add few designs to the page like decreasing the width of the scroll bar, color changes etc. So, make a new file named `IPFSViewerCSS.css` and add the following code inside it.

```css
.imageViewer::-webkit-scrollbar {
    height: 2px;
}

.imageViewer::-webkit-scrollbar-track {
    box-shadow: inset 0 0 5px rgb(94, 94, 94);
    border-radius: 50px;
}

.imageViewer::-webkit-scrollbar-thumb {
    background: rgb(0, 0, 0);
    border-radius: 50px;
}

a {
    color: white;
}
```

Now we need to update our `App.js` file with all the components that we have made so far.

**Import Modules** - First import all the modules and components into the `App.js` file by appending the following code under the `//1 Importing...` section.

```javascript
// 1. Importing other modules
import {GetWeb3, GetContract, GetAccount} from './BlockchainUtil';
import IPFSUploader from './IPFSUploader';
import IPFSViewer from './IPFSViewer';

import contractJson from './build/contracts/FileManager.json';
```

**`Load Web3`** - Now put the following code under the `//2. Load web3...` section. This would set the state with web3 instance.

```javascript
// 1. Load web3
const Web3 = new GetWeb3();
this.web3 = await Web3.getWeb3();
this.setState({web3: this.web3});
```

**`Load Account`** - Put the following code under the `//3. Load Account...` section. This would set the state with Metamask wallet's first connected address.

```javascript
// 2. Load Account
const Account = new GetAccount();
this.account = await Account.getAccount(this.web3);
this.setState({account: this.account[0]});
```

**`Load Smart contract`** - Put the following code under the `//4. Load smart...` section. This would set the state with deployed smart contract's instance for the contract's interaction using Javascript.

```javascript
// 3. Load Contract
const Contract = new GetContract();
this.contract = await Contract.getContract(this.web3, contractJson);
this.setState({contract: this.contract});
```

**Load components** - Inside the `<div>` tag of `return()` function, replace the exisiting text `Distributed File Manager` with the the code of the following components.

```jsx
{/* 5. Navbar */}
<nav className="navbar navbar-dark shadow" style={{backgroundColor: "#1b2021", height: "60px", color: "white"}}>
  <b>Distributed File Manager</b>
  <span style={{float: "right"}}>{this.state.account}</span>
</nav>

{/* 6. IPFS Viewer com  ponent */}
<IPFSViewer state = {this.state} />

{/* 7. IPFS Uploader component */}
<IPFSUploader state = {this.state} />
```

Now go to the `root` directory of the project, i.e. `dfm` directory, and run the command `npm start`. The ReactJS server would start automatically.

Visit [http://localhost:3000](http://localhost:3000) to interact with built dApp.

Don't forget to setup Metamask with `Celo` Alfajores testnet and also fund the account with Alfajores test tokens in order to upload files.

In the Metamask extension, add a custom RPC by clicking at the network dropdown in the center of the extension. Fill the details as shown in the below image.

<center>
    <img src = "https://i.imgur.com/3eFZpJn.png" style = "width: 250px;">
</center>

| Info	          | Value                                    |
| ----------------|------------------------------------------|
| Network Name    | Celo Alfajores 			     |
| New RPC URL     | https://alfajores-forno.celo-testnet.org |
| Chain ID        | 44787 				     |
| Currency Symbol | CELO 				     |

{% hint style="info" %} If you find any difficulty in setting up the project, then feel free to clone this repository https://github.com/rajranjan0608/dfm, and follow the steps in the `README.md` file of this repo in order to run the application. {% endhint %}

<p align="center" style="margin: 30px;">
  <img src = 'https://imgur.com/a6on3z4.gif'>
</p>

## Conclusion

Congratulations! You have successfully built a Distributed File Manager by deploying the smart contract on **Celo** Alfajores test network using **Trufflesuite** and connecting your client side made with **ReactJS**. Most interesting part is that, we have used **IPFS** protocol for uploading our files on the distributed network.

## What's next?

We have built a Distributed File Manager with basic upload and view features. I want to encourage you to make a more scalable and sofisticated application by adding few more features like encrypting files before uploading and enhancing the UI.

## About the author

This tutorial was created by [Raj Ranjan](https://www.linkedin.com/in/iamrajranjan), You can get in touch with the author on [Figment Forum](https://community.figment.io/u/rranjan01234/) and on [GitHub](https://github.com/rajranjan0608)

If you had any difficulties following this tutorial or simply want to discuss Celo's tech with us you can [**join our community today**](https://discord.gg/fszyM7K)!
