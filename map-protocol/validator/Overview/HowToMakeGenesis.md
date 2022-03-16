## Marker genesis

`Marker genesis` is a developer utility to easy running atlas blockchain testnets and related jobs around testnets.

Its main advantage over previous solutions is that it's able to create a `genesis.json` where all core conctracts are
already deployed in it.

We need to use the marker tool. For information on how to use the marker tool, please refer to [Marker](../../marker/Marker.md)

### Generating a genesis.json

Suppose "github.com/mapprotocol/atlas" is your project path.

cd marker config path : github.com/mapprotocol/atlas/marker/config to modify markerConfig.json.

First you need to config the markerConfig.json like this:

```shell
{
  "AdminAddress": "0x1c0edab88dbb72b119039c4d14b1663525b3ac15",
  "Validators": [
    {
     "Account":  "your validator account keystore path",
        "Password":  "your validator account password"
    },
    {
     "Account":  "your validator account keystore path",
        "Password":  "your validator account password"
    },
    {
     "Account":  "your validator account keystore path",
        "Password":  "your validator account password"
    },
    {
     "Account":  "your validator account keystore path",
        "Password":  "your validator account password"
    }
    ...
  ]
}
```

Second you need compile your `map-contracts` project,we need the bytecode about `map-contracts` to make `genesis.json` file.
1.Download `map-contracts` project in any folder you like , use this command `git clone https://github.com/mapprotocol/map-contracts.git`
2.Suppose you have installed node, then then switch to the project file initialize the project and use this command `npm install`
3.Compile the project using truffle, the command compiled by truffle is `truffle compile`
4.A file called `build` will be generated in your `map-contracts` project. We will use this file to specify the corresponding parameters.

then to do so run:

```shell
USAGE
  $ ./Marker genesis

OPTIONS
  --buildpath                                                  buildpath is the path to 
                                                               truffle compile output folder.
  
  --newenv                                                     the genesis.json will be 
                                                               generated under this folder 

  --markercfg                                                  this your markerConfig.json 
                                                               path default to github.com/
                                                               mapprotocol/atlas/marker/config/
                                                               markerConfig.json
                                                     
  
EXAMPLES:

marker genesis --buildpath ./root/map-contracts/build/contracts --newenv ./root/atlasEnv --markercfg "./root/atlas/marker/config/markerConfig.json"


This will create a `genesis.json` in the ./root/atlasEnv folder
```
