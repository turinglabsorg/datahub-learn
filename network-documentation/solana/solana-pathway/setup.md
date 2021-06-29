# Setup the project

## Prerequisites

The following software is required to set up and complete the Solana Pathway

* Node.js v14 or higher installed : [https://nodejs.org/](https://nodejs.org/)
* `yarn` installed : [https://yarnpkg.com/getting-started/install](https://yarnpkg.com/getting-started/install)
* Rust installed : [https://rustup.rs/](https://rustup.rs/)
* Solana Tool Suite installed : [https://docs.solana.com/cli/install-solana-cli-tools](https://docs.solana.com/cli/install-solana-cli-tools)

{% hint style="warning" %}
**There are known compatibility issues with Microsoft Windows and also Apple M1 products!  
  
Windows Users**: _The Rust BPF toolchain is not available for Windows._ This means the compilation step cannot be completed from a Windows commandline. It is still possible to complete the Pathway: You must install [Docker Desktop](https://learn.figment.io/network-documentation/extra-guides/docker-setup-for-windows) and [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10#manual-installation-steps) - then you will need to clone the `learn-solana-dapp` repository and install [Rust](https://rustup.rs/) and the [Solana Tool Suite](https://docs.solana.com/cli/install-solana-cli-tools), inside of the WSL filesystem.

To access the filesystem of your [installed Linux distribution](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-6---install-your-linux-distribution-of-choice) for WSL :  
Run the command [`wsl`](https://docs.microsoft.com/en-us/windows/wsl/reference) from a `cmd.exe` or PowerShell terminal.

**macOS** **Users**: If you are using any of the [Apple M1](https://en.wikipedia.org/wiki/Apple_M1#Products_that_use_the_Apple_M1) products, you may need to build the Solana Tool Suite from source. Refer to this GitHub pull request for more information : [https://github.com/solana-labs/solana/pull/16346/](https://github.com/solana-labs/solana/pull/16346/)
{% endhint %}



Start by cloning the repository and install the npm dependencies

```text
git clone https://github.com/figment-networks/learn-solana-dapp.git
cd learn-solana-dapp
yarn
```

{% embed url="https://www.youtube.com/watch?v=ndYmwoem6tI&list=PLkgTdjgP1aUAiqqbvVi3b0sSdxByd5KSX&index=1" caption="Setup the Solana Learn Pathway Project" %}

Create an `.env.local` file at the root of the directory. Copy and paste the contents of the existing `.env.example` into the new file and save it to disk \(alternatively, you can rename `.env.example` \).

The value for `REACT_APP_DATAHUB_API_KEY` can be found on the [DataHub Services Dashboard](https://datahub.figment.io/services/solana). Click on the Solana icon in the list of available protocols and then copy your key as shown below. You can now paste your personal API key into `.env.local` . This will authenticate you and enable you to make JSON-RPC requests to the Solana cluster via DataHub.

![](../../../.gitbook/assets/screen-shot-2021-06-14-at-10.46.03-pm.png)

With the API key in place, save the `.env.local` file and start the React interface with :

```text
yarn start
```

Once the development server loads and compiles the application, your default browser should be redirected to `http://localhost:3000` :

{% hint style="warning" %}
If the local development server has started, but a new browser window or tab has not opened, you can still do so manually by pasting \(or typing\) `http://localhost:3000` into the address bar of your browser. Once the app is running and you can view it in your browser, click on  
"View step instructions" to see the challenge for step one, Connect to the Solana Devnet!
{% endhint %}

![](../../../.gitbook/assets/screen-shot-2021-06-14-at-10.47.58-pm%20%282%29%20%283%29.png)

{% hint style="info" %}
[**Join us on Discord**](https://discord.gg/fszyM7K) if you encounter any issues with the tutorial or have any questions!
{% endhint %}

You can now move ahead to the next step by clicking on the "Next" button below on the right.

