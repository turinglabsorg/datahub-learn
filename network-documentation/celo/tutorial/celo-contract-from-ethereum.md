# Converting an Ethereum Contract to the Celo Network

The Celo blockchain is completely independent of the Ethereum blockchain, but the contracts are written in the same way and the native currency for the Celo blockchain is ERC-20 compliant.  This means it’s really easy to convert code already written for the Ethereum network to the Celo network.  

To better understand how to do this, we’re going to take the faucet code from [Chapter 7](https://github.com/ethereumbook/ethereumbook/blob/develop/07smart-contracts-solidity.asciidoc) of the [Ethereum book](https://github.com/ethereumbook/ethereumbook), and make it work for the Celo network.  

## Original Faucet code
Below is the [code](https://raw.githubusercontent.com/ethereumbook/ethereumbook/develop/code/Solidity/Faucet8.sol) from the Ethereum book.  

````
// SPDX-License-Identifier: CC-BY-SA-4.0

// Version of Solidity compiler this program was written for
pragma solidity ^0.6.4;

contract Owned {
    address payable owner;

    // Contract constructor: set owner
    constructor() public {
        owner = msg.sender;
    }

    // Access control modifier
    modifier onlyOwner {
        require(msg.sender == owner, "Only the contract owner can call this function");
        _;
    }
}

contract Mortal is Owned {
    // Contract destructor
    function destroy() public onlyOwner {
        selfdestruct(owner);
    }
}

contract Faucet is Mortal {
    event Withdrawal(address indexed to, uint amount);
    event Deposit(address indexed from, uint amount);

    // Accept any incoming amount
    receive() external payable {
        emit Deposit(msg.sender, msg.value);
    }

    // Give out ether to anyone who asks
    function withdraw(uint withdraw_amount) public {
        // Limit withdrawal amount
        require(withdraw_amount <= 0.1 ether);

        require(
            address(this).balance >= withdraw_amount,
            "Insufficient balance in faucet for withdrawal request"
        );

        // Send the amount to the address that requested it
        msg.sender.transfer(withdraw_amount);

        emit Withdrawal(msg.sender, withdraw_amount);
    }
}
````


If we were to deploy this contract on the Celo network as is, it would work for withdrawing the native currency, Celo.  This is because Celo is equivalent to Ether in Solidity contracts like this.  

But what about Celo's stable currency, cUSD?  How would we withdraw that token from this faucet?

To withdraw a specific token, we will have to rewrite our faucet to consider ERC-20 tokens.  

## Rewriting the Faucet

While we're at it, we should utilize the [Open Zeppelin](https://github.com/pkdcryptos/OpenZeppelin-openzeppelin-solidity) library to avoid writing code that's already been written well.  

First, we'll remove our Owned and Mortal contracts and import Open Zeppelin contracts instead.

Add the Open Zeppelin library to your project.
````
npm install openzeppelin-solidity
````

Now we can import the relevant contracts in our Faucet.sol file. 

The Owned and Mortal contracts from the code above are replaced below with the Open Zeppelin access contract called Ownable.  Instead of us having to write basic behavior like this for every Solidity project, we can just utilize this library instead.  

The [Ownable](https://github.com/pkdcryptos/OpenZeppelin-openzeppelin-solidity/blob/master/contracts/ownership/Ownable.sol) contract provides administrative access control over the faucet.  Only the creator of the contract can destroy the contract.  What's nice about the Open Zeppelin contract is it adds a bit more functionality than ours.  Specifically the ability to transfer or renounce ownership over a contract.  


Our updated Faucet.sol file now has these imports at the top:

````
pragma solidity >=0.8.0;

import "../node_modules/openzeppelin-solidity/contracts/security/ReentrancyGuard.sol";
import "../node_modules/openzeppelin-solidity/contracts/access/Ownable.sol";

import "../node_modules/openzeppelin-solidity/contracts/token/ERC20/IERC20.sol";

contract Faucet is ReentrancyGuard, Ownable {
  // ...
}  

````

We also added the [Reentrancy Guard](https://github.com/pkdcryptos/OpenZeppelin-openzeppelin-solidity/blob/master/contracts/utils/ReentrancyGuard.sol) contract to protect against nested calls to our faucet.  We didn't need to add this for our faucet to be functional, but why not add some security while we can?

To complete our contract and have it work for both Celo and cUSD, we have to update our withdraw function.  

Everything else stays the same.  Our logging events for withdrawals and deposits don't change.  Our recieve function stays the same.  

We need to add an additional parameter to our withdraw function that specifies which ERC-20 token to withdraw.  

````
    function withdraw(uint256 withdraw_amount, address token) public {

        require(
            address(this).balance >= withdraw_amount,
            "Insufficient balance in faucet for withdrawal request"
        );

        require(
            // transfer the specified token from this contract to msg.sender
            IERC20(token).transfer(msg.sender, withdraw_amount),
            "Withdrawing cUSD failed."
        );
        emit Withdrawal(msg.sender, withdraw_amount);
    }
````

By using the ERC20 interface from Open Zeppelin, we can pass any address that points to a Celo token contract and withdraw that token from this faucet, provided there is a sufficient balance beforehand.  

Here is the full Faucet.sol contract after our changes:

````
pragma solidity >=0.8.0;

import "../node_modules/openzeppelin-solidity/contracts/security/ReentrancyGuard.sol";
import "../node_modules/openzeppelin-solidity/contracts/access/Ownable.sol";

import "../node_modules/openzeppelin-solidity/contracts/token/ERC20/IERC20.sol";

contract Faucet is ReentrancyGuard, Ownable {
    event Withdrawal(address indexed to, uint256 amount);
    event Deposit(address indexed from, uint256 amount);

    address Celo = 0xF194afDf50B03e69Bd7D057c1Aa9e10c9954E4C9;
    address cUSD = 0x874069Fa1Eb16D44d622F2e0Ca25eeA172369bC1;

    function withdraw(uint256 withdraw_amount, address token) public {
        require(token == Celo || token == cUSD, "token is not celo or cUSD");

        require(
            address(this).balance >= withdraw_amount,
            "Insufficient balance in faucet for withdrawal request"
        );

        require(
            IERC20(token).transfer(msg.sender, withdraw_amount),
            "Withdrawing cUSD failed."
        );
        emit Withdrawal(msg.sender, withdraw_amount);
    }

    function donate() external payable {
        emit Deposit(msg.sender, msg.value);
    }

    receive() external payable {
        emit Deposit(msg.sender, msg.value);
    }

    fallback() external payable {
        emit Deposit(msg.sender, msg.value);
    }
}

````

This is our Ethereum faucet contract completely converted to Celo.  We even added some additional checks to make sure the token passed to the withdraw function matches the celo or cUSD contract address.  This ensures only celo currencies can be withdrawn from this faucet.  


## Calling the contract
To call this contract from a dApp kit frontend, we can run the following code.
I recommend using the truffle box kit for any fullstack development with Celo.

Run the following in the root of a project directory to setup the kit:
````bash
truffle unbox critesjosh/celo-dappkit
````

Please note that addresses are handled as strings in javascript and that withdraw amounts have to be handled as big numbers.  

````bash
cd client
npm install bignumber
````

A withdraw from faucet function would look like this.  This code could be triggered from a button press in a react application as seen in the example [here](https://github.com/BrittanyDeventer/green-deeds-celo/blob/master/client/screens/CeloScreen.js).

````
  withdrawFromFaucet = async (withdrawAmount) => {

    const requestId = 'withdraw_cUSD'
    const dappName = 'Green Deeds'
    const callback = Linking.makeUrl('/my/path')

    // convert user amount to wei in bignumber format
    let amount = parseFloat(withdrawAmount)
    let weiAmount = BigNumber(amount*10e17)

    const txObject = await this.state.faucetContract.methods.withdraw(weiAmount, cUSD)

    // Send a request to the Celo wallet to send an update transaction to the Faucet contract
    requestTxSig(
      kit,
      [
        {
          from: this.state.address,
          to: this.state.faucetContract.options.address,
          tx: txObject,
          feeCurrency: FeeCurrency.cUSD
        }
      ],
      { requestId, dappName, callback }
    ).catch(err => console.log("requestTXSig Err: ", err))

    // Get the response from the Celo wallet
    const dappkitResponse = await waitForSignedTxs(requestId)
    const tx = dappkitResponse.rawTxs[0]
    
    // Get the transaction result, once it has been included in the Celo blockchain
    let result = await toTxResult(kit.web3.eth.sendSignedTransaction(tx)).waitReceipt()

    console.log(`Faucet contract update transaction receipt: `, result.transactionHash)  
    Alert.alert("Transaction Complete!",
    `Tx Hash: ${result.transactionHash}`
    )
  }
  ````


## What we learned
This tutorial was meant to show how easy it is to convert a smart contract written for Ethereum and make it work for the Celo network.  As long as we understand that the celo currency is an ERC-20, etheruem network, compliant token, we can see what part of existing open source code needs to be modified to work in this new environment.  

### Next steps
I hope this tutorial helps to see the potential in looking at existing open source code and understanding how simple modifications can lead to efficient coding solutions.  Please consider the following reading list to continue on your journey.  

#### Further Reading:
-  [Celo Background and Key Concepts](https://docs.celo.org/overview#background-and-key-concepts)
-  [Open Zeppelin Source](https://github.com/pkdcryptos/OpenZeppelin-openzeppelin-solidity)
-  [Celo Savings Circle](https://github.com/celo-org/savings-circle-demo)
   -  Another dApp that utilizes the ERC20 interface in Celo contracts
-  [Ethereum Book](https://github.com/ethereumbook/ethereumbook)
