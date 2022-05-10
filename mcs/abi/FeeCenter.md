```json
{
  "_format": "hh-sol-artifact-1",
  "contractName": "FeeCenter",
  "sourceName": "contracts/FeeCenter.sol",
  "abi": [
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "indexed": true,
          "internalType": "bytes32",
          "name": "previousAdminRole",
          "type": "bytes32"
        },
        {
          "indexed": true,
          "internalType": "bytes32",
          "name": "newAdminRole",
          "type": "bytes32"
        }
      ],
      "name": "RoleAdminChanged",
      "type": "event"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "account",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "sender",
          "type": "address"
        }
      ],
      "name": "RoleGranted",
      "type": "event"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": true,
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "account",
          "type": "address"
        },
        {
          "indexed": true,
          "internalType": "address",
          "name": "sender",
          "type": "address"
        }
      ],
      "name": "RoleRevoked",
      "type": "event"
    },
    {
      "inputs": [],
      "name": "DEFAULT_ADMIN_ROLE",
      "outputs": [
        {
          "internalType": "bytes32",
          "name": "",
          "type": "bytes32"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [],
      "name": "MANAGER_ROLE",
      "outputs": [
        {
          "internalType": "bytes32",
          "name": "",
          "type": "bytes32"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "manager",
          "type": "address"
        }
      ],
      "name": "addManager",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "token",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "amount",
          "type": "uint256"
        }
      ],
      "name": "doDistribute",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "id",
          "type": "uint256"
        },
        {
          "internalType": "address",
          "name": "token",
          "type": "address"
        }
      ],
      "name": "getDistribute",
      "outputs": [
        {
          "internalType": "address",
          "name": "feeAddress",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "rates",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        }
      ],
      "name": "getRoleAdmin",
      "outputs": [
        {
          "internalType": "bytes32",
          "name": "",
          "type": "bytes32"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "to",
          "type": "uint256"
        },
        {
          "internalType": "address",
          "name": "token",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "amount",
          "type": "uint256"
        }
      ],
      "name": "getTokenFee",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "token",
          "type": "address"
        }
      ],
      "name": "getVaultToken",
      "outputs": [
        {
          "internalType": "address",
          "name": "vault",
          "type": "address"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "internalType": "address",
          "name": "account",
          "type": "address"
        }
      ],
      "name": "grantRole",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "internalType": "address",
          "name": "account",
          "type": "address"
        }
      ],
      "name": "hasRole",
      "outputs": [
        {
          "internalType": "bool",
          "name": "",
          "type": "bool"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "manager",
          "type": "address"
        }
      ],
      "name": "removeManager",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "internalType": "address",
          "name": "account",
          "type": "address"
        }
      ],
      "name": "renounceRole",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes32",
          "name": "role",
          "type": "bytes32"
        },
        {
          "internalType": "address",
          "name": "account",
          "type": "address"
        }
      ],
      "name": "revokeRole",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "to",
          "type": "uint256"
        },
        {
          "internalType": "address",
          "name": "token",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "lowest",
          "type": "uint256"
        },
        {
          "internalType": "uint256",
          "name": "highest",
          "type": "uint256"
        },
        {
          "internalType": "uint256",
          "name": "proportion",
          "type": "uint256"
        }
      ],
      "name": "setChainTokenGasFee",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "id",
          "type": "uint256"
        },
        {
          "internalType": "address",
          "name": "to",
          "type": "address"
        },
        {
          "internalType": "uint256",
          "name": "rate",
          "type": "uint256"
        }
      ],
      "name": "setDistributeRate",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "address",
          "name": "token",
          "type": "address"
        },
        {
          "internalType": "address",
          "name": "tVault",
          "type": "address"
        }
      ],
      "name": "setTokenVault",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "bytes4",
          "name": "interfaceId",
          "type": "bytes4"
        }
      ],
      "name": "supportsInterface",
      "outputs": [
        {
          "internalType": "bool",
          "name": "",
          "type": "bool"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    }
  ],
  "bytecode": "0x60a06040524660805234801561001457600080fd5b5061002060003361004f565b61004a7f241ecf16d79d0f8dbfb92cbc07fe17840425976cf0667f022fe9877caa831b083361004f565b6100fb565b610059828261005d565b5050565b6000828152602081815260408083206001600160a01b038516845290915290205460ff16610059576000828152602081815260408083206001600160a01b03851684529091529020805460ff191660011790556100b73390565b6001600160a01b0316816001600160a01b0316837f2f8788117e7eff1d82e926ec794901d17c78024a50270940304540a733656f0d60405160405180910390a45050565b60805161101a6101136000396000505061101a6000f3fe608060405234801561001057600080fd5b506004361061010b5760003560e01c80638ee6bebb116100a2578063ac18de4311610071578063ac18de431461026e578063ca0da31f14610281578063d547741f14610294578063ec87621c146102a7578063f6c64664146102bc57600080fd5b80638ee6bebb146101fc57806391d148541461020f5780639dc6108f14610222578063a217fddf1461026657600080fd5b80632d06177a116100de5780632d06177a146101b05780632f2ff15d146101c357806336568abe146101d657806360a62f35146101e957600080fd5b806301ffc9a7146101105780631a4ceff61461013857806322db25921461014d578063248a9ca31461017f575b600080fd5b61012361011e366004610d42565b6102cf565b60405190151581526020015b60405180910390f35b61014b610146366004610da1565b610306565b005b61016061015b366004610d1f565b61038f565b604080516001600160a01b03909316835260208301919091520161012f565b6101a261018d366004610d06565b60009081526020819052604090206001015490565b60405190815260200161012f565b61014b6101be366004610c6c565b6103f8565b61014b6101d1366004610d1f565b610420565b61014b6101e4366004610d1f565b61044b565b61014b6101f7366004610c87565b6104c5565b6101a261020a366004610d6c565b610527565b61012361021d366004610d1f565b6105be565b61024e610230366004610c6c565b6001600160a01b039081166000908152600360205260409020541690565b6040516001600160a01b03909116815260200161012f565b6101a2600081565b61014b61027c366004610c6c565b6105e7565b61014b61028f366004610d6c565b61060b565b61014b6102a2366004610d1f565b610687565b6101a2600080516020610fc583398151915281565b61014b6102ca366004610cba565b6106ad565b60006001600160e01b03198216637965db0b60e01b148061030057506301ffc9a760e01b6001600160e01b03198316145b92915050565b61031e600080516020610fc5833981519152336105be565b6103435760405162461bcd60e51b815260040161033a90610eab565b60405180910390fd5b604080516060810182529384526020808501938452848201928352600096875260028082528288206001600160a01b039097168852959052909420915182555160018201559151910155565b6000828152600460209081526040808320815180830190925280546001600160a01b0316825260010154918101919091528190846103e5576001600160a01b038085166000908152600360205260409020541681525b8051602090910151909590945092505050565b6000610404813361082e565b61041c600080516020610fc583398151915283610892565b5050565b60008281526020819052604090206001015461043c813361082e565b6104468383610898565b505050565b6001600160a01b03811633146104bb5760405162461bcd60e51b815260206004820152602f60248201527f416363657373436f6e74726f6c3a2063616e206f6e6c792072656e6f756e636560448201526e103937b632b9903337b91039b2b63360891b606482015260840161033a565b61041c828261091c565b6104dd600080516020610fc5833981519152336105be565b6104f95760405162461bcd60e51b815260040161033a90610eab565b6001600160a01b03918216600090815260036020526040902080546001600160a01b03191691909216179055565b60008381526002602081815260408084206001600160a01b03871685528252808420815160608101835281548152600182015493810193909352909201549181018290529082906105879061271090610581908790610981565b9061098d565b905081602001518111156105a157506020015190506105b7565b81518110156105b357505190506105b7565b9150505b9392505050565b6000918252602082815260408084206001600160a01b0393909316845291905290205460ff1690565b60006105f3813361082e565b61041c600080516020610fc58339815191528361091c565b610623600080516020610fc5833981519152336105be565b61063f5760405162461bcd60e51b815260040161033a90610eab565b6040805180820182526001600160a01b03938416815260208082019384526000958652600490529320925183546001600160a01b031916921691909117825551600190910155565b6000828152602081905260409020600101546106a3813361082e565b610446838361091c565b6001600160a01b0380831660009081526003602052604090205416806107055760405162461bcd60e51b815260206004820152600d60248201526c1d985d5b1d081b9bdd081cd95d609a1b604482015260640161033a565b600080805260046020908152604080518082019091527f17ef568e3e12ab5b9c7254a8d58478811de00f9e6eb34345acd53bf8fd09d3ec546001600160a01b031681527f17ef568e3e12ab5b9c7254a8d58478811de00f9e6eb34345acd53bf8fd09d3ed5491810182905291906107859061271090610581908790610981565b9050610792858483610999565b6001600090815260046020908152604080518082019091527fabd6e7cb50984ff9c2f3e18a2660c3353dadf4e3291deeb275dae2cd1e44fe05546001600160a01b031681527fabd6e7cb50984ff9c2f3e18a2660c3353dadf4e3291deeb275dae2cd1e44fe065491810182905291906108149061271090610581908990610981565b905061082587836000015183610999565b50505050505050565b61083882826105be565b61041c57610850816001600160a01b03166014610ab4565b61085b836020610ab4565b60405160200161086c929190610e03565b60408051601f198184030181529082905262461bcd60e51b825261033a91600401610e78565b61041c82825b6108a282826105be565b61041c576000828152602081815260408083206001600160a01b03851684529091529020805460ff191660011790556108d83390565b6001600160a01b0316816001600160a01b0316837f2f8788117e7eff1d82e926ec794901d17c78024a50270940304540a733656f0d60405160405180910390a45050565b61092682826105be565b1561041c576000828152602081815260408083206001600160a01b0385168085529252808320805460ff1916905551339285917ff6391f5c32d9c69d2a47ea670b442974b53935d1edc7fd64eb21e047a839171b9190a45050565b60006105b78284610f1c565b60006105b78284610efa565b604080516001600160a01b038481166024830152604480830185905283518084039091018152606490920183526020820180516001600160e01b031663a9059cbb60e01b17905291516000928392908716916109f59190610de7565b6000604051808303816000865af19150503d8060008114610a32576040519150601f19603f3d011682016040523d82523d6000602084013e610a37565b606091505b5091509150818015610a61575080511580610a61575080806020019051810190610a619190610ce4565b610aad5760405162461bcd60e51b815260206004820152601f60248201527f5472616e7366657248656c7065723a205452414e534645525f4641494c454400604482015260640161033a565b5050505050565b60606000610ac3836002610f1c565b610ace906002610ee2565b67ffffffffffffffff811115610ae657610ae6610fae565b6040519080825280601f01601f191660200182016040528015610b10576020820181803683370190505b509050600360fc1b81600081518110610b2b57610b2b610f98565b60200101906001600160f81b031916908160001a905350600f60fb1b81600181518110610b5a57610b5a610f98565b60200101906001600160f81b031916908160001a9053506000610b7e846002610f1c565b610b89906001610ee2565b90505b6001811115610c01576f181899199a1a9b1b9c1cb0b131b232b360811b85600f1660108110610bbd57610bbd610f98565b1a60f81b828281518110610bd357610bd3610f98565b60200101906001600160f81b031916908160001a90535060049490941c93610bfa81610f6b565b9050610b8c565b5083156105b75760405162461bcd60e51b815260206004820181905260248201527f537472696e67733a20686578206c656e67746820696e73756666696369656e74604482015260640161033a565b80356001600160a01b0381168114610c6757600080fd5b919050565b600060208284031215610c7e57600080fd5b6105b782610c50565b60008060408385031215610c9a57600080fd5b610ca383610c50565b9150610cb160208401610c50565b90509250929050565b60008060408385031215610ccd57600080fd5b610cd683610c50565b946020939093013593505050565b600060208284031215610cf657600080fd5b815180151581146105b757600080fd5b600060208284031215610d1857600080fd5b5035919050565b60008060408385031215610d3257600080fd5b82359150610cb160208401610c50565b600060208284031215610d5457600080fd5b81356001600160e01b0319811681146105b757600080fd5b600080600060608486031215610d8157600080fd5b83359250610d9160208501610c50565b9150604084013590509250925092565b600080600080600060a08688031215610db957600080fd5b85359450610dc960208701610c50565b94979496505050506040830135926060810135926080909101359150565b60008251610df9818460208701610f3b565b9190910192915050565b7f416363657373436f6e74726f6c3a206163636f756e7420000000000000000000815260008351610e3b816017850160208801610f3b565b7001034b99036b4b9b9b4b733903937b6329607d1b6017918401918201528351610e6c816028840160208801610f3b565b01602801949350505050565b6020815260008251806020840152610e97816040850160208701610f3b565b601f01601f19169190910160400192915050565b60208082526017908201527f43616c6c6572206973206e6f742061206d616e61676572000000000000000000604082015260600190565b60008219821115610ef557610ef5610f82565b500190565b600082610f1757634e487b7160e01b600052601260045260246000fd5b500490565b6000816000190483118215151615610f3657610f36610f82565b500290565b60005b83811015610f56578181015183820152602001610f3e565b83811115610f65576000848401525b50505050565b600081610f7a57610f7a610f82565b506000190190565b634e487b7160e01b600052601160045260246000fd5b634e487b7160e01b600052603260045260246000fd5b634e487b7160e01b600052604160045260246000fdfe241ecf16d79d0f8dbfb92cbc07fe17840425976cf0667f022fe9877caa831b08a2646970667358221220430ed7eea7ebd42aad8e0c26bb8a73acfb8fa0827388369ea577b7bca2041c8e64736f6c63430008070033",
  "deployedBytecode": "0x608060405234801561001057600080fd5b506004361061010b5760003560e01c80638ee6bebb116100a2578063ac18de4311610071578063ac18de431461026e578063ca0da31f14610281578063d547741f14610294578063ec87621c146102a7578063f6c64664146102bc57600080fd5b80638ee6bebb146101fc57806391d148541461020f5780639dc6108f14610222578063a217fddf1461026657600080fd5b80632d06177a116100de5780632d06177a146101b05780632f2ff15d146101c357806336568abe146101d657806360a62f35146101e957600080fd5b806301ffc9a7146101105780631a4ceff61461013857806322db25921461014d578063248a9ca31461017f575b600080fd5b61012361011e366004610d42565b6102cf565b60405190151581526020015b60405180910390f35b61014b610146366004610da1565b610306565b005b61016061015b366004610d1f565b61038f565b604080516001600160a01b03909316835260208301919091520161012f565b6101a261018d366004610d06565b60009081526020819052604090206001015490565b60405190815260200161012f565b61014b6101be366004610c6c565b6103f8565b61014b6101d1366004610d1f565b610420565b61014b6101e4366004610d1f565b61044b565b61014b6101f7366004610c87565b6104c5565b6101a261020a366004610d6c565b610527565b61012361021d366004610d1f565b6105be565b61024e610230366004610c6c565b6001600160a01b039081166000908152600360205260409020541690565b6040516001600160a01b03909116815260200161012f565b6101a2600081565b61014b61027c366004610c6c565b6105e7565b61014b61028f366004610d6c565b61060b565b61014b6102a2366004610d1f565b610687565b6101a2600080516020610fc583398151915281565b61014b6102ca366004610cba565b6106ad565b60006001600160e01b03198216637965db0b60e01b148061030057506301ffc9a760e01b6001600160e01b03198316145b92915050565b61031e600080516020610fc5833981519152336105be565b6103435760405162461bcd60e51b815260040161033a90610eab565b60405180910390fd5b604080516060810182529384526020808501938452848201928352600096875260028082528288206001600160a01b039097168852959052909420915182555160018201559151910155565b6000828152600460209081526040808320815180830190925280546001600160a01b0316825260010154918101919091528190846103e5576001600160a01b038085166000908152600360205260409020541681525b8051602090910151909590945092505050565b6000610404813361082e565b61041c600080516020610fc583398151915283610892565b5050565b60008281526020819052604090206001015461043c813361082e565b6104468383610898565b505050565b6001600160a01b03811633146104bb5760405162461bcd60e51b815260206004820152602f60248201527f416363657373436f6e74726f6c3a2063616e206f6e6c792072656e6f756e636560448201526e103937b632b9903337b91039b2b63360891b606482015260840161033a565b61041c828261091c565b6104dd600080516020610fc5833981519152336105be565b6104f95760405162461bcd60e51b815260040161033a90610eab565b6001600160a01b03918216600090815260036020526040902080546001600160a01b03191691909216179055565b60008381526002602081815260408084206001600160a01b03871685528252808420815160608101835281548152600182015493810193909352909201549181018290529082906105879061271090610581908790610981565b9061098d565b905081602001518111156105a157506020015190506105b7565b81518110156105b357505190506105b7565b9150505b9392505050565b6000918252602082815260408084206001600160a01b0393909316845291905290205460ff1690565b60006105f3813361082e565b61041c600080516020610fc58339815191528361091c565b610623600080516020610fc5833981519152336105be565b61063f5760405162461bcd60e51b815260040161033a90610eab565b6040805180820182526001600160a01b03938416815260208082019384526000958652600490529320925183546001600160a01b031916921691909117825551600190910155565b6000828152602081905260409020600101546106a3813361082e565b610446838361091c565b6001600160a01b0380831660009081526003602052604090205416806107055760405162461bcd60e51b815260206004820152600d60248201526c1d985d5b1d081b9bdd081cd95d609a1b604482015260640161033a565b600080805260046020908152604080518082019091527f17ef568e3e12ab5b9c7254a8d58478811de00f9e6eb34345acd53bf8fd09d3ec546001600160a01b031681527f17ef568e3e12ab5b9c7254a8d58478811de00f9e6eb34345acd53bf8fd09d3ed5491810182905291906107859061271090610581908790610981565b9050610792858483610999565b6001600090815260046020908152604080518082019091527fabd6e7cb50984ff9c2f3e18a2660c3353dadf4e3291deeb275dae2cd1e44fe05546001600160a01b031681527fabd6e7cb50984ff9c2f3e18a2660c3353dadf4e3291deeb275dae2cd1e44fe065491810182905291906108149061271090610581908990610981565b905061082587836000015183610999565b50505050505050565b61083882826105be565b61041c57610850816001600160a01b03166014610ab4565b61085b836020610ab4565b60405160200161086c929190610e03565b60408051601f198184030181529082905262461bcd60e51b825261033a91600401610e78565b61041c82825b6108a282826105be565b61041c576000828152602081815260408083206001600160a01b03851684529091529020805460ff191660011790556108d83390565b6001600160a01b0316816001600160a01b0316837f2f8788117e7eff1d82e926ec794901d17c78024a50270940304540a733656f0d60405160405180910390a45050565b61092682826105be565b1561041c576000828152602081815260408083206001600160a01b0385168085529252808320805460ff1916905551339285917ff6391f5c32d9c69d2a47ea670b442974b53935d1edc7fd64eb21e047a839171b9190a45050565b60006105b78284610f1c565b60006105b78284610efa565b604080516001600160a01b038481166024830152604480830185905283518084039091018152606490920183526020820180516001600160e01b031663a9059cbb60e01b17905291516000928392908716916109f59190610de7565b6000604051808303816000865af19150503d8060008114610a32576040519150601f19603f3d011682016040523d82523d6000602084013e610a37565b606091505b5091509150818015610a61575080511580610a61575080806020019051810190610a619190610ce4565b610aad5760405162461bcd60e51b815260206004820152601f60248201527f5472616e7366657248656c7065723a205452414e534645525f4641494c454400604482015260640161033a565b5050505050565b60606000610ac3836002610f1c565b610ace906002610ee2565b67ffffffffffffffff811115610ae657610ae6610fae565b6040519080825280601f01601f191660200182016040528015610b10576020820181803683370190505b509050600360fc1b81600081518110610b2b57610b2b610f98565b60200101906001600160f81b031916908160001a905350600f60fb1b81600181518110610b5a57610b5a610f98565b60200101906001600160f81b031916908160001a9053506000610b7e846002610f1c565b610b89906001610ee2565b90505b6001811115610c01576f181899199a1a9b1b9c1cb0b131b232b360811b85600f1660108110610bbd57610bbd610f98565b1a60f81b828281518110610bd357610bd3610f98565b60200101906001600160f81b031916908160001a90535060049490941c93610bfa81610f6b565b9050610b8c565b5083156105b75760405162461bcd60e51b815260206004820181905260248201527f537472696e67733a20686578206c656e67746820696e73756666696369656e74604482015260640161033a565b80356001600160a01b0381168114610c6757600080fd5b919050565b600060208284031215610c7e57600080fd5b6105b782610c50565b60008060408385031215610c9a57600080fd5b610ca383610c50565b9150610cb160208401610c50565b90509250929050565b60008060408385031215610ccd57600080fd5b610cd683610c50565b946020939093013593505050565b600060208284031215610cf657600080fd5b815180151581146105b757600080fd5b600060208284031215610d1857600080fd5b5035919050565b60008060408385031215610d3257600080fd5b82359150610cb160208401610c50565b600060208284031215610d5457600080fd5b81356001600160e01b0319811681146105b757600080fd5b600080600060608486031215610d8157600080fd5b83359250610d9160208501610c50565b9150604084013590509250925092565b600080600080600060a08688031215610db957600080fd5b85359450610dc960208701610c50565b94979496505050506040830135926060810135926080909101359150565b60008251610df9818460208701610f3b565b9190910192915050565b7f416363657373436f6e74726f6c3a206163636f756e7420000000000000000000815260008351610e3b816017850160208801610f3b565b7001034b99036b4b9b9b4b733903937b6329607d1b6017918401918201528351610e6c816028840160208801610f3b565b01602801949350505050565b6020815260008251806020840152610e97816040850160208701610f3b565b601f01601f19169190910160400192915050565b60208082526017908201527f43616c6c6572206973206e6f742061206d616e61676572000000000000000000604082015260600190565b60008219821115610ef557610ef5610f82565b500190565b600082610f1757634e487b7160e01b600052601260045260246000fd5b500490565b6000816000190483118215151615610f3657610f36610f82565b500290565b60005b83811015610f56578181015183820152602001610f3e565b83811115610f65576000848401525b50505050565b600081610f7a57610f7a610f82565b506000190190565b634e487b7160e01b600052601160045260246000fd5b634e487b7160e01b600052603260045260246000fd5b634e487b7160e01b600052604160045260246000fdfe241ecf16d79d0f8dbfb92cbc07fe17840425976cf0667f022fe9877caa831b08a2646970667358221220430ed7eea7ebd42aad8e0c26bb8a73acfb8fa0827388369ea577b7bca2041c8e64736f6c63430008070033",
  "linkReferences": {},
  "deployedLinkReferences": {}
}
```