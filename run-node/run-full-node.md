Running your own node provides various benefits, opens up new possibilities, and helps support the ecosystem. This page
will guide you on how to start your own node and participate in transaction validation.

## Prerequisites

### Hardware Requirements

Hardware requirements vary depending on the type of node but are generally not very high. Here are the recommended
configurations:

1. Machine Configuration
   MAP is a proof-of-stake network, which has different hardware requirements compared to proof-of-work networks.
   Proof-of-stake consensus is less demanding on CPUs but more sensitive to network connectivity and latency.
   Here is a standard requirement list for running a validator on the MAP network:
    - Memory: 16 GB RAM
    - CPU: Quad-core 2.5 GHz (64-bit)
    - Disk: 256 GB SSD storage space, plus a good secondary HDD
    - Network: At least 100 MB input/output Ethernet, preferably with redundant connections and HA switches
2. MAP Quantity
   Your account needs to have at least 1,000,000 MAP.

### Operating System Requirements

Atlas client supports mainstream operating systems - Linux, MacOS, Windows. This means you can run the node on a regular
desktop or server and install the operating system that suits you best on these devices. To avoid potential issues and
security vulnerabilities, make sure your operating system is up to date.

### Software Requirements

Building Atlas requires git, Go (version 1.14 or higher), and a C compiler.

## Clone the Code Repository and Build

```shell
git clone https://github.com/mapprotocol/atlas.git
cd atlas
git checkout v1.1.5
make atlas
```

## Running the Node

Run `atlas -h` to get help information.

### Running a Mainnet Node

```shell
atlas --datadir ./node console
```

### Running a Single-Node Network

A single-node network is used for testing and development purposes. It does not sync with any other network and does not
store any data.

To run a single-node network, run the following command:

```shell
atlas --datadir ./node --single console
```

If you add the `--http` flag in the command, it will enable the RPC server, and then you can access RPC
via http://127.0.0.1:7445.

```shell
atlas --datadir ./node --single --http --http.addr "127.0.0.1" --http.port 7445 console
```