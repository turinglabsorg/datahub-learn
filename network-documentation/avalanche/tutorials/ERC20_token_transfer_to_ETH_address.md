---
description: Teaching how to Transfer ERC-20 tokens from C-chain to ETH address
---

## About the author

This tutorial was created by [Seongwoo Oh](https://github.com/blackwidoq). He is a student and an Avalanche novice.


# Transfer ERC-20 tokens from the C-chain of your AVAX wallet to an ETH address \(Metamask, for example\)

In this tutorial, we are going to learn how to programmatically transfer ERC-20 tokens from the C-chain to an ETH wallet.

Simply put, ERC-20 tokens are the tokens that meet the technical standards of the Ethereum blockchain. So, they natively reside on the Ethereum blockchain. Thanks to the C-chain's full Ethereum compatibility, one can transfer ERC-20 token using the Avalanche-Ethereum bridge, which can be found [here](https://aeb.xyz/#/transfer). 

For the purpose of this tutorial, we are going to assume that you already have ERC-20 tokens (PNG and GRT) on the C-chain of your Avalanche wallet, as shown below (I transferred these tokens for the sake of learning how to do it). This module will walk you through transferring PNG tokens from an Avalanche C-chain address to an ETH address.    

![example](https://i.imgur.com/P09Vl07.png)

Similar to the previous C-chain to ETH address transfer of AVAX token transfer tutorial, we will start by installing some Ethereum libraries. In addition to installing web3 and ethers, we are going to install ethereumjs-tx, which is a module for creating, manipulating, and signing Ethereum transactions. 

```bash
$ npm install ethereumjs-tx
$ npm install web3
$ npm install ethers
```
If web3 and ethers are already installed from the previous tutorial, you can skip the last two commands.

After installing the libraries, we need to import them in order to use the libraries to interact with the Avalanche C chain 

```bash
var Tx = require('ethereumjs-tx');
var Web3 = require('web3')
const { ethers } = require('ethers')
```

The mnemonic key from your AVAX wallet needs to go between the quotation marks below. This is later used to extract the C chain wallet address. 

```bash
let mnemonic = "";
```

The code below is pointing to the Fuji network. Alternatively, we can point it to the Datahub AVAX node.

```javascript
const web3 = new Web3(new Web3.providers.HttpProvider("https://api.avax-test.network/ext/bc/C/rpc"))
```

The private key is needed to execute a transfer later on. With the mnemonic phrase provided earlier, we can obtain the private key to your AVAX wallet in the ETH address format. 

```bash
const wallet = new ethers.Wallet.fromMnemonic(mnemonic);
eth_privatekey = wallet.privateKey; 
```
Up to this point, except for the introduction of ethereumjs-tx, it has been a repeat of AVAX C-chain to ETH address transfer tutorial. 

Now comes the new learning material. Transferring tokens from one wallet to another is a transaction. In order to perform a transaction (as in, signing a transaction using the ethereumjs-tx module), certain information needs to be provided. The token address (also known as contract address) of the ERC-20 token (Pangolin in this case) needs to be provided. So, we are going to store the contract address and the ticker below. The token ticker is not needed but we are adding it for our own use.

```bash
const tokenAddress = "0x60781C2586D68229fde47564546784ab3fACA982"  
const token_name = "PNG"
```
Another piece of information needed is the wallet from which the ERC-20 tokens are to be transferred from. 

```bash
var myAddress =  wallet.address   
```

Logically, we also need provide the destination address. Put the destination address between the quotation marks.

```bash
var toAddress = ""
```

```bash
var amount = web3.utils.toHex(1e18)

// get transaction count, later will used as nonce
//var count = web3.eth.getTransactionCount(myAddress);  
async function main() {

    async function nonce_pesky () {                                                                      //nonce number, my man
        var nonce = await web3.eth.getTransactionCount(myAddress, 'pending');
        return nonce
    }
    var nonce_sir = await nonce_pesky(); // here the data will be return.
    console.log("nonce numb", nonce_sir);


    var privateKey = new Buffer.from( eth_privatekey.substring(2), 'hex')  
    // Get abi array here https://etherscan.io/address/0x86fa049857e0209aa7d9e616f7eb3b3b78ecfdb0#code
    var abiArray = [{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},{"constant":false,"inputs":[],"name":"stop","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"guy","type":"address"},{"name":"wad","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"owner_","type":"address"}],"name":"setOwner","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"src","type":"address"},{"name":"dst","type":"address"},{"name":"wad","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"dst","type":"address"},{"name":"wad","type":"uint128"}],"name":"push","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"name_","type":"bytes32"}],"name":"setName","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"wad","type":"uint128"}],"name":"mint","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"src","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"stopped","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"authority_","type":"address"}],"name":"setAuthority","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"src","type":"address"},{"name":"wad","type":"uint128"}],"name":"pull","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"wad","type":"uint128"}],"name":"burn","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"dst","type":"address"},{"name":"wad","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[],"name":"start","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"authority","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"src","type":"address"},{"name":"guy","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"inputs":[{"name":"symbol_","type":"bytes32"}],"payable":false,"type":"constructor"},{"anonymous":true,"inputs":[{"indexed":true,"name":"sig","type":"bytes4"},{"indexed":true,"name":"guy","type":"address"},{"indexed":true,"name":"foo","type":"bytes32"},{"indexed":true,"name":"bar","type":"bytes32"},{"indexed":false,"name":"wad","type":"uint256"},{"indexed":false,"name":"fax","type":"bytes"}],"name":"LogNote","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"authority","type":"address"}],"name":"LogSetAuthority","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"}],"name":"LogSetOwner","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"}]
    // var abiArray = JSON.parse('[{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},{"constant":false,"inputs":[],"name":"stop","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"guy","type":"address"},{"name":"wad","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"owner_","type":"address"}],"name":"setOwner","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"src","type":"address"},{"name":"dst","type":"address"},{"name":"wad","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"dst","type":"address"},{"name":"wad","type":"uint128"}],"name":"push","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"name_","type":"bytes32"}],"name":"setName","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"wad","type":"uint128"}],"name":"mint","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"src","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"stopped","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"authority_","type":"address"}],"name":"setAuthority","outputs":[],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"src","type":"address"},{"name":"wad","type":"uint128"}],"name":"pull","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"owner","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"wad","type":"uint128"}],"name":"burn","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"dst","type":"address"},{"name":"wad","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":false,"inputs":[],"name":"start","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"authority","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"src","type":"address"},{"name":"guy","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"inputs":[{"name":"symbol_","type":"bytes32"}],"payable":false,"type":"constructor"},{"anonymous":true,"inputs":[{"indexed":true,"name":"sig","type":"bytes4"},{"indexed":true,"name":"guy","type":"address"},{"indexed":true,"name":"foo","type":"bytes32"},{"indexed":true,"name":"bar","type":"bytes32"},{"indexed":false,"name":"wad","type":"uint256"},{"indexed":false,"name":"fax","type":"bytes"}],"name":"LogNote","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"authority","type":"address"}],"name":"LogSetAuthority","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"}],"name":"LogSetOwner","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"}]', 'utf-8')
    var contractAddress = tokenAddress
    var contract = new web3.eth.Contract(abiArray, contractAddress, {from: myAddress})


    async function gas() {                                      //gas price, my man
        let gas_p = await web3.eth.getGasPrice();
        //console.log(abc);
        return gas_p
    }
    var gas_price = await gas(); // here the data will be return.
    console.log("gasprice as a number",gas_price);

    shit_getBalance().then(function (result) {            //shit coin balance
        console.log(result +" " + token_name);
    });

    // async function g_limit() {
    //     var glimit = web3.eth.getBlock("latest").gasLimit;
    //     var gasLimitHex = glimit ;//web3.toHex(glimit);
    //     return gasLimitHex
    // }
    // var gasslimit = await g_limit();
    // console.log("gaslimit",gasslimit)
    var rawTransaction = {"from":myAddress, "gasPrice":web3.utils.toHex(gas_price),"gasLimit":web3.utils.toHex(210000),"to":contractAddress,"value":"0x0","data":contract.methods.transfer(toAddress, amount).encodeABI(),"nonce":web3.utils.toHex(nonce_sir)} 
    var transaction = new Tx(rawTransaction)
    transaction.sign(privateKey)
    web3.eth.sendSignedTransaction('0x' + transaction.serialize().toString('hex'))
}
main().catch((err) => {
    console.log("We have encountered an error!")
    console.error(err)
})

async function shit_getBalance() {
    let minABI = [
        // balanceOf
        {
          "constant":true,
          "inputs":[{"name":"_owner","type":"address"}],
          "name":"balanceOf",
          "outputs":[{"name":"balance","type":"uint256"}],
          "type":"function"
        },
        // decimals
        {
          "constant":true,
          "inputs":[],
          "name":"decimals",
          "outputs":[{"name":"","type":"uint8"}],
          "type":"function"
        }
      ];
    let contract = new web3.eth.Contract(minABI,tokenAddress);  
    balance = await contract.methods.balanceOf(wallet.address).call();
    converted_balance = web3.utils.fromWei(balance, 'ether') //web3.fromWei(balance, 'ether')
    return converted_balance;
  }
```