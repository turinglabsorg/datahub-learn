---
description: >-
  How to create fungible tokens on Celo using Remix-IDE
---

# How to mint your own fungible Celo Token 

## Prerequisite:
1. Celo environment setup on Remix, tutorial for which can be found [here]()
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
This is identical to `Fiat currency`. Every dollar is equal to every other dollar of same value.

### b. Non-Fungible Tokens (NFTs):
As you can guess, they are exactly opposite of fungible tokens. Every non-fungible token is `unique` thus can't be interchanged.
Examples can be Digital Art or Songs.

In this example we will learn how to mint `fungible tokens`.
We will use standard interface of `ERC20 tokens` that are quite popular on **Ethereum** and learn how to build similar 
tokens on **Celo**.

## 3. ERC20 Standard
ERC20 is a standard interface used in ethereum to build fungible tokens.

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
We can get free testnet balance from [here]().

### Procedure
1. Select the account on Celo Wallet with which we want to deploy the smart contract.
2. Click on Deploy
3. Pay the transaction fee and sign the transaction using celo wallet.

Congratulation! we have deployed our very own Fungible token on celo blockchain. It usually takes aroound 5 seconds to get our transaction confirmed. Once our trasaction is confirmed
Let's head over to [blockScout](https://alfajores-blockscout.celo-testnet.org/) to see all the details.

[Here](https://alfajores-blockscout.celo-testnet.org/address/0x4324bf228a8a3f1ddfd232335372d5cbaae38cd1/transactions) are the details of the smart contract deployment shown in the example.

## 6. Transferring Token

Now that our contract is deployed, we have all the CFT tokens that exists on Celo Blockchain. Now we should also learn how to tranfer these tokens from our account to others.


There are 2 ways of transfering tokens:
1. **SMART CONTRACT** :  

a. Select the account which have 1000 CFT, in my case `0x0eAd666A5B65ED614990fD582693039ed49847E6` (which you can verify on Blockscout using the link given above) on Celo extesnion wallet.

b. In the tranfer tab and enter the **Alfajores Testnet Address** of account to which you want to tranfer the tokens. I will tranfer it to `0x39E526B01fDe70d64FABDCe5Ca92b47789AA231D`.

c. Click on `Transact` and sign the transactiion.

With that we have learned how to tranfer your fungible tokens to other addresses. But there is still a problem, we aren't able to see our tokens on our celo wallet. So Let's learn how to do that.

1. Select the **Add Token** button on your celo wallet.
2. Enter the contract address in **Token Contract Address** and ckick `Next`.

Now you will be able to see your token balance.


2. **Celo Wallet**

After you have added the token address to our wallet, simply click on `Send` button and add the address of recipient. Tokens will be transfered after you sign the trasaction and pay `transaction fee`.

## Conclusion

This was a very intresting tutorial. In this tutorial we learned:
1. What are tokens
2. Different types of tokens
3. How to deploy fungible token on Celo blockchain
4. How to send tokens over Celo.

Now deploy more fungible tokens on Celo and send some over to `0x0eAd666A5B65ED614990fD582693039ed49847E6`. We would love to see what you created.
