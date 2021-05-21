# Learn how to create a chat application using Solidity & React

## Introduction
Today we will build a distributed chat application on Avalanche's Fuji test-network from scratch. The DApp will allow users to connect with other users and chat with them in real-time. We will develop our smart contract using Solidity which will be deployed on Avalanche's C-chain. It would have an easy-to-use UI developed using Reactjs. So lets begin ...
<br><br>

## Prerequisites
* Basic familiarity with Reactjs and Solidity
* Should've completed [Deploy a Smart Contract on Avalanche using Remix and MetaMask](https://learn.figment.io/network-documentation/avalanche/tutorials/deploy-a-smart-contract-on-avalanche-using-remix-and-metamask) tutorial

## Requirenments
* Node v10.18.0 or later
* [Metamask extension](https://metamask.io/download.html) on your browser
<br><br>

## Developing smart contract 

The basic functionality that an application should provide to classify as a chatting application is that the users should have the ability to connect with others and then share messages with them. So to accomplish this we will divide our contract into three parts :- Account creation, Adding new friends and finally sending messages to their friends.

<br>

### Account creation

We will define 3 methods `checkUserExists()`, `createAccount()` & `getUsername()` for the task. 

* The first method as the name suggests is used to check if an user is registered with our application or not; which will be called in the `createAccount()` method to make sure duplicate user are not created and it will also be called later from other methods to check the user existence.

* The second method registers _new user_ on the platform and finally the last method will return the username of the user if it exists.

<br>

### Adding new friend

Here too we will divide the task into 3 parts - `checkAlreadyFriends()`, `addFriend()` & `getMyFriendList()`.

* The first method, actually named `_addFriend()`, checks whether two users are already friends with each other or not. This is needed to prevent duplicate channel between the same parties and will also be used to prevent a user from sending messages to other users unless they are friends.

* The second method mark two users as friend if they both exists on the system and are already not friends with each other and finally the last method will return a list of all friends of a given user.

<br>

### Messaging

The final part of our contract will enable the exchange of messages between the users. We will divide the task into 2 methods i.e. `sendMessage` & `readMessage`.

* The  former method would allow an user to send message to another user if they both are registered on the application and are friends with each other. This will be achieved from the methods defined earlier (`checkUserExists` and `_alreadyFriends`) respectively.

* The later method, actually named `getMyChatMessages()` , returns the chat history that has happend between the two users so far.

<br>

### Data Collections

We will have three types of user-defined data types, namely - `user`, `friend` & `message`. 

1. `user` will have properties - `name` (which stores the default username that the user wants to be addressed with) and second a list of their friends.

2. `friend` has two properties - `pubkey` which is the friends' avax address and the `name` user would like to refer them as.

3. `message` consists of three properties - the `sender`, `timestamp` & the text message `msg`.

<br>

We would maintain 2 collections in our database :- 

1. `userList` where all the users on the platform are mapped with their public address.

2. `allMessages` which stores the messages in an ordered manner between the 2 users. As solidity doesn't allow user-defined keys in mapping, We would be using a workaround where we would hash the public key of the two users to obtain a unique value (practically).

<br>

Following is the solidity contract which we will deploy on the network.

```javascript
pragma solidity >=0.7.0 <0.9.0;

contract Database {
	
	// Stores the default name of a user and her friends info
	struct user {
		string name;
		friend[] friendList;
	}
	
	// Each friend is identified by its address and name assigned by the second party
	struct friend {
		address pubkey;
		string name;
	}
	
	// message construct stores the single chat message and its metadata
	struct message {
		address sender;
		uint256 timestamp;
		string msg;
	}
	
	// Collection of users registered on the application
	mapping(address => user) userList;
	// Collection of messages communicated in a channel between two users
	mapping(bytes32 => message[]) allMessages; // key : Hash(user1,user2)
	
	/// It checks whether a user(identified by its public key)
	/// has created an account on this application or not
	function checkUserExists(address pubkey) public view returns(bool) {
		// return keccak256(bytes(userList[pubkey].name)) != keccak256("");
		return bytes(userList[pubkey].name).length > 0;
	}
	
	/// Registers the caller(msg.sender) to our app with a non-empty username
	function createAccount(string calldata name) external {
		require(checkUserExists(msg.sender)==false,"User already exists!");
		require(bytes(name).length>0, "Username cannot be empty string"); 
		userList[msg.sender].name = name;
	}
	
	/// Returns the default name provided by an user
	function getUsername(address pubkey) external view returns(string memory) {
		require(checkUserExists(pubkey), "Such user doesn't exists on our application!");
		return userList[pubkey].name;
	}
	
	/// Adds new user as your friend with an associated nickname
	function addFriend(address friend_key, string calldata name) external {
		require(checkUserExists(msg.sender), "Create your account first!");
		require(checkUserExists(friend_key), "Such user doesn't exists on our application!");
		require(msg.sender!=friend_key, "You cannot add friend as yourself!");
		require(_alreadyFriends(msg.sender,friend_key)==false, "The given users are already friends with each other");
		
		_addFriend(msg.sender,friend_key,name);
		_addFriend(friend_key,msg.sender, userList[msg.sender].name);
	}
	
	/// Checks if two users are already friends or not
	function _alreadyFriends(address pubkey1, address pubkey2) internal view returns(bool) {
		
		if(userList[pubkey1].friendList.length > userList[pubkey2].friendList.length)
		{
			address tmp = pubkey1;
			pubkey1 = pubkey2;
			pubkey2 = tmp;
		}
		
		for(uint i=0; i<userList[pubkey1].friendList.length; ++i)
		{
			if(userList[pubkey1].friendList[i].pubkey == pubkey2)
				return true;
		}
		return false;
	}
	
	/// @dev A helper function to update the friendList
	function _addFriend(address me,address friend_key, string memory name) internal {
		friend memory newFriend = friend(friend_key,name);
		userList[me].friendList.push(newFriend);
	}
	
	/// Returns list of friends of the sender
	function getMyFriendList() external view returns(friend[] memory) {
		return userList[msg.sender].friendList;
	}
	
	/// @notice Returns a unique code for the channel created between the two users
	/// @dev Hash(key1,key2) where key1 is lexiographically smaller than key2
	function _getChatCode(address pubkey1, address pubkey2) internal pure returns(bytes32) {
		if(pubkey1 < pubkey2)
			return keccak256(abi.encodePacked(pubkey1, pubkey2));
		else
			return keccak256(abi.encodePacked(pubkey2,pubkey1));
	}
	
	/// Sends a new message to a given friend
	function sendMessage(address friend_key, string calldata _msg) external {
		require(checkUserExists(msg.sender), "Create your account first!");
		require(checkUserExists(friend_key), "Such user does not exists on our application!");
		require(_alreadyFriends(msg.sender,friend_key), "You are not friend with the given user");
		
		bytes32 chatCode = _getChatCode(msg.sender,friend_key);
		message memory newMsg = message(msg.sender,block.timestamp,_msg);
		allMessages[chatCode].push(newMsg);
	}
	
	/// Returns all the chat messages communicated in a channel
	function getMyChatMessages(address friend_key) external view returns(message[] memory) {
		bytes32 chatCode = _getChatCode(msg.sender, friend_key);
		return allMessages[chatCode];
	}
} 
```
<br>

Deploy the above contract using the steps provided at
[*Deploy a Smart Contract on Avalanche using Remix and MetaMask*](https://learn.figment.io/network-documentation/avalanche/tutorials/deploy-a-smart-contract-on-avalanche-using-remix-and-metamask). 

>Note down the `contract address` and `ABI` generated in the above tutorial. They will be required in further steps.

<br>

> **Knowledge Point**
><br>An Application Binary Interface (ABI) is a JSON object which stores the metadata about the methods of a contract like data type of input parameters, return data type & property of the method like payable, view, pure etc. You can learn more about the ABI from the [solidity documentation](https://docs.soliditylang.org/en/latest/abi-spec.html)

<br>

## Creating frontend using React
<br>

Now, we are going to create a react app and setup the frontend of the application.

<br>

Open the terminal and navigate to the directory where you intend to create your application.
```cmd
cd /path/to/directory
```

Now use `npm` to install create-react-app 

```cmd
npm install -g create-react-app
```

Create a new react app
```cmd
create-react-app avalanche-chatapp
```

Move to the newly created directory and install the given dependencies.
```cmd
cd avalanche-chatapp
npm install ethers@5.1.4 react-bootstrap@1.5.2 bootstrap@4.6.0 --save
```

Now remove the contents of src and public folder
```cmd
rm -v src/*
rm -v public/*
```

Make a `index.html` file in `public` folder of current directory and put the following html code.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <title>Chat App</title>
</head>

<body>
    <div id="root"></div>
</body>

</html>
```

<br>

Move out of the public folder and make a file named `Components.js` inside `src` folder and put the following code inside.
<br>

```javascript
import React from "react"
import { useState } from "react"
import { Button, Modal, Form,  Row,  Card, Navbar } from "react-bootstrap"
import 'bootstrap/dist/css/bootstrap.min.css';

//This is a modal with a form for adding new friend
export function AddNewChat(props){
    const [show, setShow] = useState(false);
    const handleClose = () => setShow(false);
    const handleShow = () => setShow(true);

    return (
        <>
            <div className="AddNewChat"  style={{ position:"absolute",bottom:"0px",padding:"10px 45px 0 45px",margin:"0 95px 0 0", width:"97%"}}>
                  <Button variant="success" className="mb-2" onClick={handleShow}>
                      + NewChat
                  </Button>
                  <Modal show={show} onHide={handleClose}>
                    <Modal.Header closeButton>
                      <Modal.Title>Add New Friend</Modal.Title>
                    </Modal.Header>
                    <Modal.Body>
                        <Form.Group>
                          <Form.Control required id="addPublicKey" size="text" type="text" placeholder="Enter Friends Public Key" />
                          <br />
                          <Form.Control required id="addName" size="text" type="text" placeholder="Name" />
                          <br />
                        </Form.Group>
                    </Modal.Body>
                    <Modal.Footer>
                      <Button variant="secondary" onClick={handleClose}>
                        Close
                      </Button>
                      <Button variant="primary" onClick={() => {
                          props.addHandler( document.getElementById('addName').value, document.getElementById('addPublicKey').value);
                          handleClose();
                       }}>
                            Add Friend
                      </Button>
                    </Modal.Footer>
                  </Modal>
            </div>  
        </>
    );
}

//This is a function which renders the friends in the friends list
export function ChatCards(props){
    return (
        <Row style={{marginRight:"0px"}}  >
            <Card  border="success" style={{width:'100%',alignSelf:'center',marginLeft:"15px"}} onClick={() =>{
                props.getMessages(props.publicKey);
            }} >
              <Card.Body>
                  <Card.Title>{props.name}</Card.Title>
                  <Card.Subtitle>{props.publicKey.length > 20 ? props.publicKey.substring(0,20) + " ...": props.publicKey}</Card.Subtitle>
              </Card.Body>
            </Card>
        </Row> 
    );
}

//This is a functional component which renders the individual messages
export function Message(props){
    console.log(props.marginLeft);
    return (
        <Row style={{marginRight:"0px"}}  >
            <Card  border="success" style={{width:'80%',alignSelf:'center',margin:"0 0 5px "+props.marginLeft, float:"right" , right:"0px"}} >
              <Card.Body>
              <h6 style={{float:"right"}}> {props.timeStamp}</h6>
                  <Card.Subtitle><b>{props.sender}</b></Card.Subtitle>
                  <Card.Text>{props.data}</Card.Text>
              </Card.Body>
            </Card>
        </Row>
    );
}

//This function renders the navbar of the application
export function NavBar(props){

    return (
        <Navbar bg="dark" variant="dark">
              <Navbar.Brand href="#home"> Avalanche Chat App</Navbar.Brand>
              <Navbar.Toggle />
              <Navbar.Collapse className="justify-content-end">
                <Navbar.Text> 
                    <Button style={{display:props.showButton}} variant="success" onClick={async () => {
                       props.login();
                    }}>
                      Connect to MetaMask
                    </Button>
                    <div style={{display:props.showButton==="none"?"block":"none"}}>
                      Signed in as: <a href="#"> {props.username} </a>
                    </div>
                </Navbar.Text>
              </Navbar.Collapse>
        </Navbar>
    );
}
```

<br>

Make a new file named `index.js` in the same folder `src` and paste the given code.
> Note: Write down the `contract address` obtained earlier in the variable named `contractAddress` on line 12

<br>

```javascript
import React from "react";
import ReactDom from "react-dom";
import { useState, useEffect } from "react";
import { Container, Row, Col, Card, Form, Button } from 'react-bootstrap';
import { NavBar, ChatCards, Message, AddNewChat } from './Components';
import {ethers} from "ethers";
/*Import  ABI File*/
import {abi} from "./abi";

export function App(props) {    

    const contractAddress = ""; // Add the contract address inside the quotes
    
    // Keeps The friends list which has available chats user has
    const [friends, setFriends] = useState(null);
    
    // Keep the UserName of the current user
    const [myName, setMyName] = useState(null);

    // Keeps the public Key of the current User
    const [myPublicKey, setMyPublicKey] = useState(null);

    // Keeps the UserName and Public Key of the Current open chat
    const [activeChat, setActiveChat] = useState({friendname:null,publicKey:null});

    // Keeps all the messages of the active chat
    const [activeChatMessages, setActiveChatMessages] = useState(null);
    const [showConnectButton,setShowConnectButton] = useState("block");
    let provider;
    let signer;

    /* Save the contents of abi in a variable*/
    const contractABI = abi; 
    // Saves the Contract in state variable
    const [myContract, setMyContract] = useState(null);

    // Login to metamask and check the if the user exists else creates one
    async function login() {
        /* Call connectToMetaMask */
        let res = await connectToMetamask();
        if(res===true){
            provider = new ethers.providers.Web3Provider(window.ethereum);
            signer = provider.getSigner();
            const mycontract = new ethers.Contract(contractAddress, contractABI, signer);
            setMyContract(mycontract);
            const address = await signer.getAddress();             
            let present = await mycontract.checkUserExists(address);
            let username;
            if(present)
                username = await mycontract.getUsername(address);
            else{
                username = prompt('Enter a username','Guest'); 
                if(username==='') username = 'Guest';
                await mycontract.createAccount(username);
            }
            setMyName(username);
            setMyPublicKey(address);
            setShowConnectButton("none");
        }
        else{
            alert("Couldn't connect to metamask");
        }        
    }

    // Checks if the metamask connects 
    async function connectToMetamask() {
        /* Open Metamask window*/
        try{
            await window.ethereum.enable();
            return true;
        }
        catch(err) {
            return false;
        }
    }

    // Add a friend to the users Friends List
    async function AddChat(name,publicKey){
        /* Add a friend */
        await myContract.addFriend(publicKey, name);
        const frnd = {"name":name,"publicKey":publicKey};
        setFriends(friends.concat(frnd));
    }

    //Sends messsage to a User 
    async function sendMessage(data){
        if(!(activeChat && activeChat.publicKey)) return;
        /* Send message */
        const recieverAddress = activeChat.publicKey;
        await myContract.sendMessage(recieverAddress,data);
    } 

    // Fetch chat messages with a friend 
    async function getMessage(friendsPublicKey){

        let nickname;
        let messages = [];

        friends.map((item) => {
            if(item.publicKey === friendsPublicKey)
                nickname = item.name;
        });

        /* Get message*/
        const data = await myContract.getMyChatMessages(friendsPublicKey);

        data.map((item) => {
            const timestamp = new Date(1000*item[1].toNumber()).toUTCString();
            messages.push({"publicKey":item[0], "timeStamp":timestamp, "data":item[2] });
        });
        
        setActiveChat({friendname:nickname,publicKey:friendsPublicKey});
        setActiveChatMessages(messages);
    }

    // This exceutes every time page renders and when myPublicKey or myContract changes
    useEffect(() => {
        async function loadFriends(){
            let friendList=[];

            /* Get Friends*/
            try{
                const data = await myContract.getMyFriendList();
                data.map((item) => {
                    friendList.push({"publicKey":item[0], "name":item[1]});
                })
            }catch(err){
                friendList = null;  
            }
            setFriends(friendList);
        }
        loadFriends();
    },[myPublicKey,myContract]);

    //Makes Cards for each Message
    const Messages = activeChatMessages ? activeChatMessages.map((message) => {
        let margin = "5%";
        let sender = activeChat.friendname;
        if(message.publicKey===myPublicKey){
            margin="15%";
            sender = "You";
        }
        return (
            <Message marginLeft={margin} sender={sender} data={message.data} timeStamp={message.timeStamp}/>
        );
    }) : null;
    
    //Displays each card
    const chats = friends? friends.map((friend) => {
     return (
         <ChatCards publicKey={friend.publicKey} name={friend.name} getMessages={(key) => getMessage(key)}/>
     );
    }) : null;

    return (
        <Container style={{padding:"0px" ,border:"1px solid grey"}}>
            {/*This shows the navbar with connect button*/}
            <NavBar username={myName} login = {async () => login()} showButton={showConnectButton} />
    
            <Row>
                {/*Here is the friendslist shown*/}
                <Col style={{"paddingRight":"0px","borderRight":"2px solid #000000"}}>
                    <div style={{ "backgroundColor":"#DCDCDC","height":"100%",overflowY:"auto"}}>
                          <Row style={{marginRight:"0px"}}  >
                              <Card style={{width:'100%',alignSelf:'center',marginLeft:"15px"}}>
                                <Card.Header>
                                    Chats
                                </Card.Header>
                              </Card>
                          </Row>
                          {chats}
                          <AddNewChat myContract={myContract} addHandler={(name,publicKey) => AddChat(name,publicKey)}/>
                    </div>
                </Col>
                <Col xs={8} style={{"paddingLeft":"0px"}} >
                    <div style={{ "backgroundColor":"#DCDCDC","height":"100%"}}>
                        {/*The chat header with refresh button and username and his publickey gets rendered here*/}
                         <Row style={{marginRight:"0px"}}  >
                              <Card style={{width:'100%',alignSelf:'center',margin:"0 0 5px 15px"}}>
                                <Card.Header>
                                    {activeChat.friendname} : {activeChat.publicKey}
                                    <Button style={{float:"right"}} variant="warning" onClick={() => {
                                        if(activeChat && activeChat.publicKey)
                                            getMessage(activeChat.publicKey);
                                    } }>
                                        Refresh
                                    </Button>
                                </Card.Header>
                               
                            </Card>
                          
                        </Row>
                        {/*The messages will be showen here*/}
                        <div className="MessageBox" style={{height:"400px",overflowY:"auto"}}>
                           {Messages}
                        </div>
                        {/*The form with send button and message input feilds*/}
                        <div className="SendMessage"  style={{ borderTop:"2px solid black",position:"relative"bottom:"0px",padding:"10px 45px 0 45px",margin:"0 95px 0 0",
                        width:"97%"}}>
                            <Form>
                                <Form.Row className="align-items-center">
                                    <Col xs={9}>
                                        <Form.Control id="messageData" className="mb-2"  placeholder="Send Message"/>
                                    </Col>
                                    <Col >
                                      <Button className="mb-2" style={{float:"right"}} onClick={ () => {
                                          sendMessage(document.getElementById('messageData').value);
                                          document.getElementById('messageData').value = "";
                                      }}>
                                        Send
                                      </Button>
                                    </Col>
                                </Form.Row>
                            </Form>
                        </div> 
                    </div>
                </Col>
            </Row>
        </Container>
    );
}

ReactDom.render( <
    App / > ,
    document.getElementById('root')
);
```
<br>

Make a new file named `abi.js` in the `src` folder. Export const variable named abi which stores the ABI you had generated earlier. Your abi.js file should look like this!

```javascript
export const abi = [
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "friend_key",
				"type": "address"
			},
			{
				"internalType": "string",
				"name": "name",
				"type": "string"
			}
		],
		"name": "addFriend",
		"outputs": [],
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "string",
				"name": "name",
				"type": "string"
			}
		],
		"name": "createAccount",
		"outputs": [],
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "friend_key",
				"type": "address"
			},
			{
				"internalType": "string",
				"name": "_msg",
				"type": "string"
			}
		],
		"name": "sendMessage",
		"outputs": [],
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "pubkey",
				"type": "address"
			}
		],
		"name": "checkUserExists",
		"outputs": [
			{
				"internalType": "bool",
				"name": "",
				"type": "bool"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "friend_key",
				"type": "address"
			}
		],
		"name": "getMyChatMessages",
		"outputs": [
			{
				"components": [
					{
						"internalType": "address",
						"name": "sender",
						"type": "address"
					},
					{
						"internalType": "uint256",
						"name": "timestamp",
						"type": "uint256"
					},
					{
						"internalType": "string",
						"name": "msg",
						"type": "string"
					}
				],
				"internalType": "struct Database.message[]",
				"name": "",
				"type": "tuple[]"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [],
		"name": "getMyFriendList",
		"outputs": [
			{
				"components": [
					{
						"internalType": "address",
						"name": "pubkey",
						"type": "address"
					},
					{
						"internalType": "string",
						"name": "name",
						"type": "string"
					}
				],
				"internalType": "struct Database.friend[]",
				"name": "",
				"type": "tuple[]"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "address",
				"name": "pubkey",
				"type": "address"
			}
		],
		"name": "getUsername",
		"outputs": [
			{
				"internalType": "string",
				"name": "",
				"type": "string"
			}
		],
		"stateMutability": "view",
		"type": "function"
	}
]
```

<br>

Now its time to run our React app. Use the following command to start the React app.
```cmd
npm start
```
* Visit [http://localhost:3000](http://localhost:3000) to interact with the app.

* Don't forget to setup Metamask with `Fuji testnet` and also fund the account with Fuji c-chain test tokens for tx fees. Please refer to this tutorial on [Connecting Datahub to Metamask](https://learn.figment.io/network-documentation/avalanche/tutorials/connect-datahub-to-metamask).

<br>

![preview](https://github.com/realnimish/blockchain-chat-app/blob/main/public/UI.png?raw=true)

## Conclusion
Congratulatons!! You've successfully developed a distributed chatting application deployed on Avalanche's Fuji network and have also created a UI via which you can interact with the DApp.

<br>

## Troubleshooting
<br>

**Transaction Failure**

* Check if your account has sufficient balance at [fuji block-explorer](https://testnet.avascan.info/). You can fund your address from the given [faucet](https://faucet.avax-test.network/) 

* Make sure that you have selected the correct account on metamask if you have more than one account connected to the site.

<br>

**Application crash**

Check if you have updated the `contractAddress` variable in `src/index.js` properly!

<br>

## What's Next
The current DApp has very limited functionalities and we can improve it by adding features like deleting messages, blocking users, creating group(s) and optimising the DApp interaction cost with possible methods like max chat limit or using event log for short messages.

<br>

## About the Author(s)

The tutorial was created by [Nimish Agrawal](https://github.com/realnimish) & [Sayan Kar](https://github.com/SayanKar). They are currently undergraduate students pursuing B.Tech in CSE [2018-2022] from Indian Institute of Information Technology, Guwahati. They both are highly passionate about the Blockchain Technology and are continuosly striving to learn more about the emerging protocols in the space and contribute to the community with their experience. You can reach out to them on [Figment Forum](https://community.figment.io/u/nimishagrawal100.in/) or on LinkedIn [@Nimish Agrawal](https://www.linkedin.com/in/realnimish) and [@Sayan Kar](https://www.linkedin.com/in/sayan-kar-). 
