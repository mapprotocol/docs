## What is marker

marker is a simple command-line tool provided by Atlas. With marker, you can easily perform various operations without
the need for additional scripting. It allows you to interact with the Atlas protocol and smart contracts using
command-line commands. Some of the common functionalities include registering validators, participating in elections,
on-chain governance, and voting for validators.

## Building marker

```shell
git clone https://github.com/mapprotocol/atlas.git
cd atlas
make marker
```

After the build is complete, you can run "./build/bin/marker" to start marker, or navigate to the "./build/bin"
directory and run "./marker" to start marker.

## Usage

Most of the marker subcommands require connecting to a running Atlas RPC node. Therefore, you need to start an Atlas RPC
node first. You can refer to [Run RPC Node](../../run-node/run-rpc-node.md) for instructions
on how to start an RPC node. Alternatively, you can use the
provided [public RPC endpoints](../../network/relay-chain.md).

There is also a subcommand that requires authentication using a keystore file. Therefore, you need to prepare a keystore
file in advance when using these commands. You can generate a keystore file using the following method:

1. [Build Atlas](../../run-node/install.md)
2. Generate a keystore file using the Atlas client:

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

If you already have an account, you can go to the Atlas console to convert your account private key into a keystore file.

```shell
USAGE
./atlas --dataDir "./data" console
web3.personal.importRawKey("your private key","your password")
EXAMPLES:
> web3.personal.importRawKey("eaff...db280","password")
"0xd2f9e7716cc88944e5ed9f675649532c80d765f8"
The keystore has been stored in the directory specified by --dataDir.
```

After the execution is completed, a keystore file will be generated in the "data" directory of the current directory.