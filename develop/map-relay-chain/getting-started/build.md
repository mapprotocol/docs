## Building from the source

Building atlas requires both a Go (version 1.16 or later) and a C compiler.
You can install them using your favourite package manager.
Once the dependencies are installed, run

```
    git clone https://github.com/mapprotocol/atlas.git
    cd atlas
    make atlas
```

## Running atlas

Going `atlas -h` can get help infos.

### Running on the atlas main network

```
$ atlas --datadir ./data0 console
```


### Running on the Atlas Chain singlenode(private) network

To start a g
instance for single node,  run it with these flags:

```
$ atlas --single --datadir ./data0  console
```
if add  `--http` flag to the command, it will enable the RPC server, than you can access the RPC with the  `http://127.0.0.1:7445`