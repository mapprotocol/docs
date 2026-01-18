An archive node is an instance of an Atlas client configured to build an archive of all historical states. It is a
useful tool for certain use cases but might be more tricky to run than a full node.

## what is an archive node

To grasp the importance of an archive node, let's clarify the concept of "state." Atlas can be referred to as
transaction-based state machine. It consists of accounts and applications executing transactions which are changing
their state. The global data with information about each account and contract is stored in a trie database called state.

- Account balances and nonces
- Contract code and storage
- Consensus-related data, e.g. Staking Deposit Contract

To interact with the network, verify and produce new blocks, Atlas clients have to keep up with the most recent
changes (the tip of the chain) and therefore the current state. An execution layer client configured as a full node
verifies and follows the latest state of the network but only caches the past few states, e.g. the state associated with
the last 128 blocks, so it can handle chain reorgs and provide fast access to recent data. The recent state is what all
clients need to verify incoming transactions and use the network.

You can imagine the state as a momentary network snapshot at a given block and the archive as a history replay.

Historical states can be safely pruned because they are not necessary for the network to operate and it would be
uselessly redundant for client to keep all out-of-date data. States that existed before some recent block (e.g. 128
blocks before the head) are effectively thrown away. Full nodes only keep historical blockchain data (blocks and
transactions) and occasional historical snapshots they can use to regenerate older states on request. They do this by
re-executing past transactions in the EVM, which can be computationally demanding when the desired state is far from the
nearest snapshot.

However, this means that accessing a historical state on a full node consumes a lot of computation. The client might
need to execute all past transactions and compute one historical state from genesis. Archive nodes solve this by storing
not only the most recent states but every historical state created after each block. It basically makes a trade-off with
bigger disk space requirement.

It's important to note that the network does not depend on archive nodes to keep and provide all historical data. As
mentioned above, all historical interim states can be derived on a full node. Transactions are stored by any full node
and can be replayed to build the whole archive.

## Use cases

Regular usage of Atlas like sending transactions, deploying contracts, verifying consensus, etc. does not require
access to historical states. Users never need an archive node for a standard interaction with the network.

The main benefit of state archive is a quick access to queries about historical states. For example, archive node would
promptly return results like:

- What was ETH balance of account 0x1337... at block 15537393?
- What is the balance of token 0x in contract 0x at block 1920000?

As explained above, a full node would need to generate this data by EVM execution which uses the CPU and takes time.
Archive nodes access them on the disk and serve responses immediately. This is a useful feature for certain parts of
infrastructure, for example:

- Service providers like block explorers
- Researchers
- Security analysts
- Dapp developers
- Auditing and compliance

There are various free services that also allow access to historical data. As it is more demanding to run an archive
node, this access is mostly limited and works only for occasional access. If your project requires constant access to
historical data, you should consider running one yourself.

## Hardware

Always make sure to verify hardware requirements for a given mode in a client's documentation. The biggest requirement
for archive nodes is the disk space. Depending on client, it varies from 3TB to 12TB. Even if HDD might be considered a
better solution for large amounts of data, syncing it and constantly updating the head of the chain will require SSD
drives. SATA(opens in a new tab) drives are good enough but it should be a reliable quality, at least TLC(opens in a new
tab). Disks can be fitted into a desktop computer or a server with enough slots. Such dedicated devices are ideal for
running high uptime node. It's totally possible to run it on a laptop but the portability will come at an additional
cost.

All of the data needs to fit in one volume, therefore disks have to be joined, e.g. with RAID0(opens in a new tab) or
LVM(opens in a new tab). It might be also worth considering using ZFS(opens in a new tab) as it supports "Copy-on-write"
which ensures data is correctly written to the disk without any low level errors.

For more stability and security in preventing accidental database corruption, especially in a professional setup,
consider using ECC memory(opens in a new tab) if your system supports it. The size of RAM is generally advised to be the
same as for a full node but more RAM can help speed up the synchronization.

During initial sync, clients in archive mode will execute every transaction since genesis. Execution speed is mostly
limited by the CPU, so a faster CPU can help with the initial sync time. 

## Run an archive node


Run the archive node using the following command.

```shell
atlas --datadir ./node --syncmode "full" --gcmode "archive"
```
