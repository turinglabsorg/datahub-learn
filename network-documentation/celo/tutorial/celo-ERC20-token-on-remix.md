---
description: >-
  How to create fungible tokens on Celo using Remix-IDE
---

# How to mint your own fungible Celo Token 

## Prerequisite:
1. Celo environment setup for Remix, tutorial for which can be found [here](network-documentation/celo/tutorial/celo-for-remix.md)
2. Basic knowledge of solidity and Remix



## 1. What are tokens?
Tokens are a unit of measurement in virtual world. It can represent any value the creator wants it to have.

For Example:
* `Shares of a company`
* `Player currency in Online games`
* `Unit of influence in governance of Defi projects`
* `Piece of Art or Media`
* `more ...`


## 2. Types of Tokens
Broadly these digital tokens can be classified into two categories:
### a. Fungible Tokens : 
Fungibility means **replaceable** by another identical item. In simple term, tokens that have equal value and are interchangable.
This is identical to `Fiat currency`. Every Dollar is equal to every other Dollar of same value.

### b. Non-Fungible Tokens (NFTs):
As you can guess, these are exactly opposite of fungible tokens. Every non-fungible token is `unique` thus can't be interchanged.
Examples can be Digital Art or Songs.

In this example we will learn how to mint `fungible tokens`.
We will use standard interface of `fungible tokens` that are quite popular on **Ethereum** and learn how to build similar 
tokens on **Celo**.

## 3. ERC20 Standard
ERC20 is a standard interface used on Ethereum to build fungible tokens.

This interface contains some **functions**
```
//optional
function name() public view returns (string)
function symbol() public view returns (string)
function decimals() public view returns (uint)
//required
function totalSupply() public view returns (uint)
function balanceOf(address tokenOwner) public view returns (uint balance)
function allowance(address tokenOwner, address spender) public view returns (uint remaining)
function transfer(address to, uint tokens) public returns (bool success)
function approve(address spender, uint tokens) public returns (bool success)
function transferFrom(address from, address to, uint tokens)public returns (bool success)
```
and some **events**
```
event Transfer(address indexed from, address indexed to, uint tokens)
event Approval(address indexed tokenOwner, address indexed spender, uint tokens)
```
that needs to be defined before deploying our smart contract on blockchain.

In this tutorial, we will create a minimalistic version of ERC20 (fungible) tokens.

## 4. Defining Functions and Events

We have named our contract `CeloFungibleToken` and the implementation is given below.
Create a new file on Remix `erc20.sol` and copy the code.
```
// This contract should not be used in production

pragma solidity ^0.5.0;

contract ERC20Interface {
    function totalSupply() public view returns (uint);
    function balanceOf(address tokenOwner) public view returns (uint balance);
    function allowance(address tokenOwner, address spender) public view returns (uint remaining);
    function transfer(address to, uint tokens) public returns (bool success);
    function approve(address spender, uint tokens) public returns (bool success);
    function transferFrom(address from, address to, uint tokens) public returns (bool success);

    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}




contract CeloFungibleToken is ERC20Interface{
    string public name;                          // name of the token
    string public symbol;                        // symbol of token
    uint8 public decimals;                       // divisibility of token
    uint256 public _totalSupply;                 // total number of tokens in existence

    mapping(address => uint) balances;
    mapping(address => mapping(address => uint)) allowed;

    
    constructor() public {
        name = "CeloFungibleToken";
        symbol = "CFT";
        decimals = 10; 
        _totalSupply = 10000000000000; //totaltokens=(_totalSupply/10**decimals)=1000 (in our case)
        
        /** 
         decimals means the unit of divisibility we want in our tokens,
         For example if we want a divisibility of 10^(-3) and total supply of 1000 tokens then
         decimals = 3 and _totalSupply = 1000000
         
        **/

       
        balances[msg.sender] = _totalSupply;
        emit Transfer(address(0), msg.sender, _totalSupply);
    }

    function totalSupply() public view returns (uint) {
        return _totalSupply  - balances[address(0)];
    }

    function balanceOf(address tokenOwner) public view returns (uint balance) {
        return balances[tokenOwner];
    }


    // this function allows an address to give an allowance to another address (spender) 
    // to be able to retrieve tokens from it. 
    
    function allowance(address tokenOwner, address spender) public view returns (uint remaining) {
        return allowed[tokenOwner][spender];
    }

    function approve(address spender, uint tokens) public returns (bool success) {
        allowed[msg.sender][spender] = tokens;
        emit Approval(msg.sender, spender, tokens);
        return true;
    }

    function transfer(address to, uint tokens) public returns (bool success) {
        balances[msg.sender] = balances[msg.sender]- tokens;
        balances[to] = balances[to] +  tokens;
        emit Transfer(msg.sender, to, tokens);
        return true;
    }
    
    
     // this function moves the amount of tokens from sender to recipient and the given amount is 
     // then deducted from the caller’s allowance. 
     
    function transferFrom(address from, address to, uint tokens) public returns (bool success) {
        balances[from] = balances[from] -  tokens;
        allowed[from][msg.sender] = allowed[from][msg.sender] -  tokens;
        balances[to] = balances[to] + tokens;
        emit Transfer(from, to, tokens);
        return true;
    }
}
```

Our contract should be able to `Compile` now.

## 5. Deployment
We will deploy our contract on `Alfajores testnet`. We need to make sure that we have enough balance in our testnet account.
We can get free testnet balance from [here](https://celo.org/developers/faucet).

### Procedure
a. Select the account on Celo Wallet with which we want to deploy the smart contract.(Make sure to select the account on `Alfajores Test Network`)


b. Click on **Deploy** button.   
 <p align="center">
 <img src="../../../.gitbook/assets/celo-extension-deploy-button.JPG" width="350" alt="accessibility text">
 </p>


c. Pay the `transaction fee` and sign the transaction using celo wallet.

 <p align="center">
 <img src="../../../.gitbook/assets/signing-transaction-celo.JPG" width="350" alt="accessibility text">
 </p>


**Congratulations!** we have deployed our very own Fungible token on celo blockchain. It usually takes around 5 seconds to get our transaction confirmed. Once our transaction is confirmed, let's head over to [BlockScout](https://alfajores-blockscout.celo-testnet.org/) to see all the details.

[Here](https://alfajores-blockscout.celo-testnet.org/address/0x4324bf228a8a3f1ddfd232335372d5cbaae38cd1/transactions) are the details of the smart contract deployment shown in this example.

## 6. Transferring Token

Now that our contract is deployed, we have all the CFT tokens that exists on Celo Blockchain. Now we should also learn how to transfer these tokens from our account to others.


There are many ways of transferring tokens from one address to another. We will learn about two easiest ways.

**1. USING SMART CONTRACT** :  

a. Select the account which have 1000 CFT, in my case `0x0eAd666A5B65ED614990fD582693039ed49847E6` (which you can verify on Blockscout using the link given above) on Celo extension wallet.

b. In the tranfer tab and we need to enter the **Alfajores Testnet Address** of account to which we want to tranfer the tokens with the amount of tokens to be transfered. For this tutorial, we will use  `0x39E526B01fDe70d64FABDCe5Ca92b47789AA231D` address and send 10 CFT tokens (take care of decimals that we defined in the contract).

 <p align="center">
 <img src="../../../.gitbook/assets/transfer-tab-celo-extension.JPG" width="350" alt="accessibility text">
 </p>
 
c. Click on `Transact`

 <p align="center">
 <img src="../../../.gitbook/assets/sending-token-celo-remix.JPG" width="350" alt="accessibility text">
 </p>

d. Pay the `transaction fee`


With that we have learned how to tranfer your fungible tokens to other addresses. But there is still a problem, we aren't able to see our tokens on our celo wallet. So Let's learn how to do that.

a. Select the **Add Token** button on your celo wallet as shown in the image.

<p align="center">
 <img src="../../../.gitbook/assets/celo-extension-wallet.JPG" width="350" alt="accessibility text">
 </p>
 
b. Enter the contract address in **Token Contract Address** and click `Next`.

<p align="center">
 <img src="../../../.gitbook/assets/token-address-celo-wallet.JPG" width="350" alt="accessibility text">
</p>

c. Click on `Add token`


Now we will be able to see our token balance.

<p align="center">
 <img src="../../../.gitbook/assets/celo-wallet-after-token-addition.JPG" width="350" alt="accessibility text">
</p>



**2. Celo Wallet**

After you have added the token address to our wallet, simply click on `Send` button and add the address of recipient. Tokens will be transfered after you sign the trasaction and pay `transaction fee`.

## Conclusion

This was a very intresting tutorial. In this tutorial we learned:
1. What are tokens
2. Different types of tokens
3. How to deploy fungible token on Celo blockchain
4. How to send tokens over Celo.

Now deploy more fungible tokens on Celo and send some over to `0x0eAd666A5B65ED614990fD582693039ed49847E6`. We would love to see what you created.
