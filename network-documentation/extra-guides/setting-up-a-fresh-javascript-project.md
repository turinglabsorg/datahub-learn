---
description: >-
  This guide explains how to set up a new project directory for working with
  example JavaScript code
---

# Setting up a fresh JavaScript Project

## Making sure Node.js is installed

In a terminal window, type the command `node -v` to check the currently installed version of Node.js.

Most modern JavaScript APIs will target Node.js v12+. Using a version manager such as [nvm](https://github.com/nvm-sh/nvm) or [fnm](https://github.com/Schniz/fnm) is strongly encouraged, as it allows developers to quickly switch between Node.js versions.

## Initialize the project directory

{% tabs %}
{% tab title="Enter these commands into a terminal" %}
```text
mkdir samplecode
cd tezospath
npm init -y
npm install --save dotenv
```
{% endtab %}
{% endtabs %}

This creates our project directory, the directory name can be changed to anything suitable - it does not need to remain `samplecode`. The `cd` command changes into the newly created directory \(making it the working directory\). Next we need to run a package manager like npm, with the `-y` flag to generate a default `package.json` . Next we will install the [dotenv](https://www.npmjs.com/package/dotenv) package.   
  
Assuming no errors during the installation of dotenv, the project directory will now contain these files and directories in the following structure :

{% tabs %}
{% tab title="Directory structure" %}
```text
+-- /samplecode
    |
    +-- package.json
    |
    +-- package-lock.json
    |
    +-- /node_modules
        |  
        +-- /dotenv
```
{% endtab %}
{% endtabs %}

Since we used the `--save` flag when installing, it will be much easier to see which dependencies are installed by looking inside the `package.json`.

```text

```

### Create .env & .gitignore

Create a new `.env` file and paste the following environment variables into it :

{% tabs %}
{% tab title=".env" %}
```bash
DATAHUB_URL=https://tezos--rpc--florencenet--full.datahub.figment.io/apikey/API_KEY
TEZOS_TESTNET_NODE_URL=https://api.tez.ie/rpc/florencenet
BAKER_ADDRESS='tz1aWXP237BLwNHJcCD4b3DutCevhqq2T1Z9'
```
{% endtab %}
{% endtabs %}

We must replace `API_KEY` at the end of the DataHub URL with a valid API key from the  
[Services Dashboard](https://datahub.figment.io/services/tezos) on DataHub. Refer to [this guide](dotenv-and-.env.md) on `.env` files for more information if this is unclear.

Also create a new file called `.gitignore` and type in `.env` on a line by itself. This will prevent our API key from being leaked to a public code repository by accident, and is an important safety consideration. 

{% tabs %}
{% tab title=".gitignore" %}
```text
.env
```
{% endtab %}
{% endtabs %}

Remember to save both files to disk before proceeding. The directory structure has not changed, so the new files inside the project directory will now be :

```text
+-- /tezospath
    |
    +-- .env
    |
    +-- .gitignore
    |
    +-- package.json
    |
    +-- package-lock.json
    |
    +-- /node_modules
        |
        +-- /@taquito  
        +-- /dotenv
        +-- ...  
```

One last thing : 

* _**If we are choosing to use the more modern**_ ****[**ES6 `import` syntax**](https://www.digitalocean.com/community/tutorials/js-modules-es6)_,_ we will need to add a line to our `package.json` to prevent a`SyntaxError: Cannot use import statement outside a module` . The `import` keyword was introduced in Node.js v12, a language feature to simplify the use of modules. Alternatively, we could use an `.mjs` file extension for all of the pathway files, however adding this line to `package.json` enables us to keep the `.js` file extension and saves a lot of related hassle with configuration : 

{% tabs %}
{% tab title="Type or paste this key:value pair into package.json if using ES6 import syntax" %}
```javascript
"type": "module",
```
{% endtab %}
{% endtabs %}



* _**If we choose to use the more common**_ **`require()`** _**syntax**_, adding the line above to our `package.json` is unnecessary. The relevant code blocks throughout the tutorial will have an additional tab which illustrates the `require()` syntax. 

For maximum developer happiness, choose _one ****_and use it throughout the tutorial. Mixing the two methods will inevitably lead to errors. 

![Choose between import and require\(\) for the entire pathway.](../../.gitbook/assets/tez-require-syntax.png)

Now that this is complete, we are set up and ready to make our connection to a Tezos node using the TaquitoToolkit and DataHub.

