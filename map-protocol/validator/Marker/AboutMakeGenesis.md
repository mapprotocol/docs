## Marker genesis

`Marker genesis` is a developer utility to easy running atlas blockchain testnets and related jobs around testnets.

Its main advantage over previous solutions is that it's able to create a `genesis.json` where all core conctracts are
already deployed in it.

### Generating a genesis.json

Suppose "github.com/mapprotocol/atlas" is your project path.

cd marker config path : github.com/mapprotocol/atlas/marker/config to modify markerConfig.json

```shell

first you need to config the markerConfig.json like this:

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

then to do so run:

```shell
USAGE
  $ Marker genesis

OPTIONS
  --buildpath                                                  buildpath is the path to 
                                                               truffle compile output folder.
  
  --newenv                                                     the genesis.json will be 
                                                               generated under this folder 

  --markerCfg                                                  this your markerConfig.json 
                                                               path default to github.com/
                                                               mapprotocol/atlas/marker/config/
                                                               markerConfig.json
                                                             
  
EXAMPLES:

marker genesis --buildpath path/to/protocol/build --newenv ./root/atlasEnv --markerCfg "./root/atlas/marker/config/markerConfig.json"


This will create a `genesis.json` in the ./root/atlasEnv folder
```
