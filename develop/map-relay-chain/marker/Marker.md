# Marker

Introduction to the Map Command Line Interface and installation instructions.

### What is the Marker

Marker contains tools to make genesis.json and some client commands to better link Map contracts.

The Command Line Interface allows users to interact with the Map Protocol smart contracts.

It allows you to interact with the Map Protocol and smart contracts using command-line tools rather than writing go Script. Some common features you may want to consider are helping users participate in elections or in on-chain governance, voting for validators, or helping users interact with multi-sig contracts.

### Building Marker

```shell
Building `Marker` requires both a Go (version 1.14 or later) and a C compiler.You can install them using your favourite package manager.
git clone https://github.com/mapprotocol/atlas.git
cd atlas
make marker 
```

Now you can run "./build/bin/marker" to launch marker or switch to ./build/bin path and run "./marker" to launch marker

### Commands

The tool is broken down into modules and commands with the following pattern:

```shell
./marker　<command> <...args> 
```

The Marker tool assumes that users are running a node which they have access to signing transactions on.

Please note that our commands need to specify your keystore file, so you need to generate your keystore file in advance.

The method of generating keystore file is here:

1. you need to building `atlas` project,building `atlas` requires both a Go (version 1.14 or later) and a C compiler.
2. download our atlas project and use this command: `git clone https://github.com/mapprotocol/atlas.git` in your favorite folder.
3. Switch to atlas project folder use this command `go build -o ./favoritefolder/atlas  *.go` will generate atlas client in the directory  specified by -o.
4. Use your client generate your keystore file like this.

```shell
USAGE
 ./atlas account new --keystore "keystore path"
EXAMPLES:
 ./atlas account new --keystore ./datadir/keystore
RESPONSE:
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:

Your new key was generated

Public address of the key:   0x929510A8b54D3a8d7943e2Cdb5BA1888F7Ab7C4a
Path of the secret key file: ./datadir/keystore/UTC--2022-03-15T02-11-43.837807000Z--929510a8b54d3a8d7943e2cdb5ba1888f7ab7c4a
The keystore has been stored in the directory specified by --keystore.
```

If you already have an account,you can use the atlas client to convert. Enter the atlas console and use the command:

```shell
USAGE
./atlas --dataDir "./data" console
web3.personal.importRawKey("your private key","your password")
EXAMPLES:
> web3.personal.importRawKey("eaffcd749482e68ca4ccf5a07a52eb7ff876ea461fbab642b2b57bcb33edb280","linjing")
"0xd2f9e7716cc88944e5ed9f675649532c80d765f8"
The keystore has been stored in the directory specified by --dataDir.
```

### Optional: Run a Full Node

Commands need to connect to a Map node to execute most functionality.
