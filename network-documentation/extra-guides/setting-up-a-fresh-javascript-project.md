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
cd samplecode
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
{
  "name": "samplecode",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^9.0.2"
  }
}
```

* _**If we are choosing to use the more modern**_ ****[**ES6 `import` syntax**](https://www.digitalocean.com/community/tutorials/js-modules-es6)_,_ we will need to add a line to our `package.json` to prevent a`SyntaxError: Cannot use import statement outside a module` . The `import` keyword was introduced in Node.js v12, a language feature to simplify the use of modules. Alternatively, we could use an `.mjs` file extension for all of our files, however adding this line to `package.json` enables us to keep the `.js` file extension and saves a lot of  hassle with configuration : 

{% tabs %}
{% tab title="Type or paste this key:value pair into package.json if using ES6 import syntax" %}
```javascript
"type": "module",
```
{% endtab %}
{% endtabs %}

* _**If we are choosing to use the more common**_ **`require()`** _**syntax**_, then it is unnecessary to add this line to `package.json` . 

With these steps completed, we now have a directory that is prepared to have other dependencies installed into it using `npm install` .

## References

{% embed url="https://www.digitalocean.com/community/tutorials/js-modules-es6" %}



