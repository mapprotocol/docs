# Marker genesis

`marker genesis` is a developer utility to easy running atlas blockchain testnets and related jobs around testnets.

Its main advantage over previous solutions is that it's able to create a `genesis.json` where all core conctracts are
already deployed in it.

## Using marker genesis

### Generating a genesis.json

Suppose "github.com/mapprotocol/atlas" is your project path.

cd marker config path : github.com/mapprotocol/atlas/marker/config to modify markerConfig.json

```shell

first you need to config the markerConfig.json like this:

{
  "AdminInfo": {
    "Account":  "your admin account keystore path(this account is the owner of contracts)",
        "Password":  "your admin account password"
  },
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
marker genesis --buildpath path/to/protocol/build --markerCfg "this your markerConfig.json path default to github.com/mapprotocol/atlas/marker/config/markerConfig.json"
```

Where `buildpath` is the path to truffle compile output folder.

This will create a `genesis.json`.
