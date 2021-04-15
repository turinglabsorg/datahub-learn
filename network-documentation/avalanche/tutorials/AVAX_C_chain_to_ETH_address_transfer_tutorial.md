---
description: Teaching how to Transfer AVAX native tokens from C chain to ETH address
---

# Learn how to transfer AVAX tokens from the C-chain of an AVAX wallet to an ETH wallet.

In this tutorial, we are going to learn how to programmatically transfer native AVAX tokens from the C-chain to an ETH wallet.

First, we need to import the appropriate libraries (web3 and ethers)

```text
const Web3 = require("web3")
const { ethers } = require('ethers')
```

The mnemonic key from your AVAX wallet needs to go between the quotation marks below. This is later used to extract the C chain wallet address.

```text
let mnemonic = "";
```

The code below is pointing to the Fuji network. (if accepted as tutorial, change this to Figment node) or we can point it to the Datahub AVAX node instead. 

```text
const web3 = new Web3(new Web3.providers.HttpProvider("https://api.avax-test.network/ext/bc/C/rpc"))   
```

The private key is needed to execute a transfer later on. With the mnemonic phrase provided earlier, we can obtain the private key to your AVAX wallet in the ETH address format.

```text
const wallet = new ethers.Wallet.fromMnemonic(mnemonic);
AVAX_privatekey = wallet.privateKey;             
```

Since AVAX EVM directly borrows from web3.eth, eb3.eth.getBalance fetches the AVAX balance. 

```text
async function main(){
    web3.eth.getBalance(wallet.address, function(err, result) {    
        if (err) {
          console.log(err)
        } else {
          console.log(web3.utils.fromWei(result, "ether") + " ETH")
        }
      })
```

The block below is to view the # of transactions associated with the wallet address(not necessary for the purpose of AVAX transfer from C chain to an ETH address) but could be useful when you later want to transfer an ERC20 token 

```text
    web3.eth.getTransactionCount(wallet.address)       
    .then(console.log);                                               
    const createTransaction = await web3.eth.accounts.signTransaction(           
        {
           gas: 21000,
```

Type in your destination ETH address beween the quotation marks below

```text
           to:"",
```

Below, we need to input how many tokens are to be transferred out of your AVAX wallet off the C-chain. Currently the amount is set to 0.15 AVAX tokens  (0.15 tokens in the line below)

```text
           value: web3.utils.toWei('0.15', 'ether'),     
        },
        AVAX_privatekey                                 
     );
     const createReceipt = await web3.eth.sendSignedTransaction(
        createTransaction.rawTransaction
     );
     console.log(
        `Transaction successful with hash: ${createReceipt.transactionHash}`
     );
  };
main().catch((err) => {
    console.log("We have encountered an error!")
    console.error(err)
})
```

Try transfering your Fuji AVAX tokens by running this script and see if it worked. 