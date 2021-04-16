---
description: How to successfully connect to a Celo Wallet with a React Native DApp
---

# How to successfully set up a Celo Wallet with a React Native DApp using Redux

## Introduction:

In this article you will learn how to successfully connect your React Native App to use the Celo Wallet and return a Wallet Address from the Alfajores Wallet.
To carry out transactions on the Celo Network, you have to connect your Wallet to be able to carry out transactions. When you start out building a dAPP using React Native, you will need this guide to demonstrate how you can install the required libraries to get your dApp up and running.

## Prerequisite:

This article assumes that you have basic knowledge of JavaScript (TypeScript) and how to start a React Native App using expo. It is also assumed that you have read the expo documentation and have basic knowledge of the Celo Wallet.
Celo Wallet
Start React Native using expo
DappKit

## Project Setup

Open the Celo documentation and follow the set up instructions:
`expo init $YOUR_APP_NAME`
We will use the TypeScript Template >> Tabs
To use the Celo DappKit, install using:
`yarn add @celo/dappkit`
You will need node version ^10.13.0.
DAppKit's dependencies require a bit of adjustment to a vanilla Expo. The first are a lot of the Node.js modules that are expected. You can get those mostly by using the following modules
`yarn add node-libs-react-native vm-browserify`
node-libs-react-native:
This package provides React Native compatible implementations of Node core modules like stream and http. This is a fork of node-libs-browser with a few packages swapped to be compatible in React Native.
vm-browserify is used to emulate node's vm module for the browser.
A couple of points note:
The metro.config.js file
const crypto = require.resolve('crypto-browserify')

"github gist"
This should allow you to build the project, however some dependencies might expect certain invariants on the global environment. For that you should create a file global.ts with the following contents and then add import './global' at the top of your App.js/tsx file:
"global github gist"
Set up Redux

## Code

After installing the required libraries, we can then build 2 simple screens in the React Native app. 
The first screen will have 2 buttons. The first button will be used to sign a User into the dApp. The second button will be used to demonstrate the user cannot access the second screen without being signed in.
The second screen will be the screen that proves that the user has successfully authenticated. The second screen will return the user's wallet address.
Let's build the Screens:
"screen 1 gist"

```json
export default function HomeScreen() {
  // const alert = useSelector(state => state.alert)
  const dispatch = useDispatch()

   const login = async () => {
    dispatch(walletActions.connect())
  }

  return (
    <View style={styles.container}>
      {/* <Text style={styles.title}>This is a DApp where you can join a co-operative circle, save together and earn.</Text> */}
      <Button onPress={login}>
        Connect Wallet
      </Button>

    </View>
  );
}
```

"screen 1 screenshot"

"screen 2 gist"
"screen 2 screenshot"
Let's implement the Logic to connect to the Wallet:
"code gist"
explanation breakdown
Let's implement Redux set up:
"code gist"
explanation breakdown

## Conclusion

This was a very interesting tutorial. In this tutorial, we learned:
We cannot wait to see what you create!

## About the Author

This tutorial was created by [Segun Ogundipe](https://www.linkedin.com/in/segun-ogundipe) and [Emmanuel Oaikhenan](https://github.com/emmaodia).
