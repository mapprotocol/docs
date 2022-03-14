# Marker

Introduction to the Makalu Command Line Interface and installation instructions.

### What is the Marker

Marker contains tools to make genesis.json and some client commands to better link Makalu contracts.

The Command Line Interface allows users to interact with the Makalu Protocol smart contracts.

It allows you to interact with the Makalu Protocol and smart contracts using command-line tools rather than writing go
Script. Some common features you may want to consider are helping users participate in elections or in on-chain
governance, voting for validators, or helping users interact with multi-sig contracts.

### Building Marker

```shell
Building `Marker` requires both a Go (version 1.14 or later) and a C compiler.You can install them using your favourite package manager.
git clone https://github.com/mapprotocol/atlas.git
cd github.com/mapprotocol/atlas/cmd/marker
go build -o Marker  *.go
```

### Commands

The tool is broken down into modules and commands with the following pattern:

```shell
Marker　<command> <...args> 
``` 
The Marker tool assumes that users are running a node which they have access to signing transactions on.

### Optional: Run a Full Node

Commands need to connect to a Makalu node to execute most functionality. 