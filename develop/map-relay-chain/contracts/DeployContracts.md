# Deploy Genesis Contracts on MAP  Relay Chain with Truffle

How to deploy a smart contract to MAP Relay Chain using Truffle.

## Introduction to Truffle

Truffle is a world-class development environment, testing framework, and asset pipeline for blockchains using the
Ethereum Virtual Machine (EVM). By creating a Truffle project and editing a few configuration settings you can easily
deploy your project on Map chain.

To deploy on MAP Relay Chain using Truffle, you should have your local environment. If you prefer to deploy without a local
environment, you can deploy using Remix or Replit.

If you are new to Truffle, complete  [Quickstart Tutorial](https://trufflesuite.com/docs/truffle/quickstart) to get more
familiar with this tool.

## Project Setup

Setup Project Folder

Open your terminal window, create a project directory, and navigate into that directory.

## Initialize Truffle

Initializing truffle creates the scaffolding for your truffle project.

`truffle init`

## Configure Deployment Settings

The default truffle.config.js file contains connections required to deploy to the Ethereum networks, imports
HDWalletProvider, and connects to the mnemonic in your .env file. To deploy a Map network, you need to update this
configuration file to point toward the different Map networks and add a few details specific to Map best practices.

# Update the truffle-config.js file

Open truffle-config.js in a text editor and configure its contents through the following example:

``` shell
  networks: {
   development: {
     host: "127.0.0.1",
     port: 7445,
     network_id: "*"
   },
   mapNetwork: {
      url: "https://poc2-rpc.maplabs.io"
      network_id: "*"
   }
  },
``` 

## Compile Contract

`truffle compile`

## Deploy Contract

Deploy to your chosen Map network running one of the following commands.

`truffle deploy --network mapNetwork`


## Deployment of contract related to validator

Introduce the contracts related to the deployment of validator

you need compile your `atlas-contracts` project,we need the `bytecode` about `atlas-contracts` to make `genesis.json`file.

1.Download `atlas-contracts` project in any folder you like , use this command `git clone https://github.com/mapprotocol/atlas-contracts.git`

2.Suppose you have installed `node`,  then switch to the project file initialize the project and use this command `npm install`

3.Download truffle with `npm install truffle`

4.Compile the project using truffle, the command compiled by truffle is `truffle compile`

5.A file called `build` will be generated in your `atlas-contracts` project. We will use this file to specify the corresponding parameters.