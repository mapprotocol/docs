# Marker

### What is the Marker

Marker contains tools to make genesis.json and some client commands to better link atlas contracts

### Building Marker

```bash
cd github.com/mapprotocol/atlas/cmd/marker
go build -o Marker  *.go
```


| client | commands  | summary                                               |
| -------- | ----------- | ------------------------------------------------------- |
| marker | genesis   | create genesis to deloy contracts                     |
| marker | validator | Validator registration、 lockMap and other operations |
| marker | voter     | voter vote、 lockMap and other operations             |

## Marker genesis

`marker genesis` is a developer utility to easy running atlas blockchain testnets and related jobs around testnets.

Its main advantage over previous solutions is that it's able to create a `genesis.json` where all core conctracts are already deployed in it.

## Using marker genesis

### Generating a genesis.json

cd marker config path : github.com/mapprotocol/atlas/marker/config

```bash

first you need to config the markerConfig.json like this:

{
  "AdminInfo": {
    "Account":  "your admin account keysore path",
        "Password":  "your admin account password"
  },
  "Validators": [
    {
     "Account":  "your validator account keysore path",
        "Password":  "your validator account password"
    },
    {
     "Account":  "your validator account keysore path",
        "Password":  "your validator account password"
    },
    {
     "Account":  "your validator account keysore path",
        "Password":  "your validator account password"
    },
    {
     "Account":  "your validator account keysore path",
        "Password":  "your validator account password"
    }
    ...
  ]
}
```

then to do so run:

```bash
marker genesis --buildpath path/to/protocol/build
```

Where `buildpath` is the path to truffle compile output folder.

This will create a `genesis.json`.

## Flag


| Flag          | Usage                                                                                               |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| key           | Private key file path                                                                               |
| keystore      | Keystore file path   ( Belong to common params)                                                     |
| namePrefix    | namePrefix when setName                                                                             |
| password      | Keystore file`s password   (defult value "")   ( Belong to common params)                           |
| commission    | The proportion of awards collected by the validator, and then the rest to voter(0< commission <0.1) |
| voteNum       | The amount of map to use to vote (The unit is 10^18)                                                |
| topNum        | topNum of validator                                                                                 |
| lockedNum     | The amount of map to lock 、unlock、relock、withdraw  (The unit is 10^18)                           |
| withdrawIndex | Relocks gold that has been unlocked but not withdrawn.                                              |
| relockIndex   | Relocks gold that has been unlocked but not withdrawn.                                              |
| rpcaddr       | HTTP-RPC server listening interface    ( Belong to common params)                                   |
| rpcport       | HTTP-RPC server listening port    ( Belong to common params)                                        |
| value         | your transfer value                                                                                 |
| duration      | duration The time (in seconds) that these requirements persist for.                                 |
| target        | target address                                                                                      |

## From the perspective of validator

### Register

```bash
Registers a validator

USAGE
  $ Marker  register   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --commission <value> 
  
Detailed introduction
    function registerValidator(
        uint256 commission,
        address lesser,
        address greater,
        bytes calldata ecdsaPublicKey,
        bytes calldata blsPublicKey,
        bytes calldata blsPop
    ) external nonReentrant returns (bool) {
        ...
	//you need locded enough MAP first
	uint256 lockedGoldBalance = getLockedGold().getAccountTotalLockedGold(account);
	require(lockedGoldBalance >= validatorLockedGoldRequirements.value, "Deposit too small");
	...
	//this will Call precompile contract 
	require(
		 _updateEcdsaPublicKey(validator, account, signer, ecdsaPublicKey),
		     "Error updating ECDSA public key"
	);
	require(
		 _updateBlsPublicKey(validator, account, blsPublicKey, blsPop),
		 "Error updating BLS public key"
	 );
	...
	//This validator will be eligible to participate in the election
	getElection().markValidatorEligible(lesser, greater, account);  

	}


	struct TotalVotes {
        // A list of eligible Validators sorted by total (pending+active) votes.
        // Note that this list will omit ineligible Validators, including those that may have > 0
        // total votes.
	// the total (pending+active) votes will decide whether you are elected at end of epoch
        SortedLinkedList.List eligible;
    }

	function markValidatorEligible(address lesser, address greater, address validator){
        ... 
        votes.total.eligible.insert(validator, value, lesser, greater);
        ...
    }
```

### quicklyRegister

```bash
If you have not creat Account  or locked the Map, you can quickly register through the `quicklyRegister` command, which integrates the `createAccount` `lockedMAP`

USAGE
$ Marker  quicklyRegister   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>--lockedNum <value> --commission <value>
```

### deregister

```bash
De-registers a validator.

USAGE
$ Marker  deregister   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>

Detailed introduction
//you need to know index first , your can konw it by getRegisteredValidatorSigners commond
function deregisterValidator(uint256 index) external nonReentrant returns (bool) {
...
uint256 requirementEndTime = validator.registerTimestamp.add(
validatorLockedGoldRequirements.duration
);

//you cannot deregister Validator during the requirementEndTime
    require(requirementEndTime < now, "Not yet requirement end time");

    //Marks a validator ineligible for electing validators.
    //Will not participate in validation
    getElection().markValidatorIneligible(account);

    // Remove the validator.
    deleteElement(registeredValidators, account, index);
    delete validators[account];
    ...
}
```

## From the perspective of voters

### Vote

```solidity

Increments the number of total and pending votes for `validator`.

USAGE
    $ Marker  vote   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>   --target <validator> --voteNum <value> 
  
Detailed introduction

    function vote(address validator, uint256 value, address lesser, address greater)
    external
    nonReentrant
    returns (bool)
    {
        // Add validator to the validators voted for by the account.
        bool alreadyVotedForValidator = false;
        address[] storage validators = votes.validatorsVotedFor[account];
        for (uint256 i = 0; i < validators.length; i = i.add(1)) {
            alreadyVotedForValidator = alreadyVotedForValidator || validators[i] == validator;
        }
	//1.voter can't vote for the same person twice
	//2.The number of votes cannot exceed the number of maxNumValidatorsVotedFor
        if (!alreadyVotedForValidator) {
            require(validators.length < maxNumValidatorsVotedFor, "Voted for too many validators");
            validators.push(validator);
        }

	//the votes will be pending
        incrementPendingVotes(validator, account, value);
        incrementTotalVotes(validator, value, lesser, greater);
	//your ticket will be marker pendingVote.epoch
        pendingVote.epoch = getEpochNumber();
  
    }

	function incrementTotalVotes(address validator, uint256 value, address lesser, address greater)
    {
        ...
        votes.total.eligible.update(validator, newVoteTotal, lesser, greater);
    }

    function incrementPendingVotes(address validator, address account, uint256 value) private {  
      ...

	//the votes will be Recorded by validatorPending
	//the votes in the validatorPending will do not participate in Epoch Awards
	//if you want to participate in Epoch Awards you need to active 
	//the active commond is activate
        PendingVote storage pendingVote = validatorPending.byAccount[account];
        pendingVote.value = pendingVote.value.add(value);
       ...
    }
  
```
### quicklyVote

```bash
If you have not creat Account or locked the Map, you can quickly vote through the `quicklyVote` command, which integrates the `createAccount` `lockedMAP`

USAGE
$ Marker  quicklyVote   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport>--keystore <keystore path> --lockedNum <value>  --voteNum <value> --target <validator>
```

### Activate

```bash
Converts `account`'s pending votes for `validator` to active votes.

USAGE
   $ Marker  activate   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>   --target <from getTotalVotesForEligibleValidators> 
  
Detailed introduction
    function _activate(address validator, address account) internal returns (bool) {
        ...
	//active at next epoch
        require(pendingVote.epoch < getEpochNumber(), "Pending vote epoch not passed");
        ...
        decrementPendingVotes(validator, account, value);
        incrementActiveVotes(validator, account, value);  
    }

	function incrementActiveVotes(address validator, address account, uint256 value)
    {
        ...
	//the votes will be Recorded by validatorActive
	//the votes in the validatorActive will participate in Epoch Awards
        validatorActive.valueByAccount[account] = validatorActive.valueByAccount[account].add(value);
        ...
    }
```

### RevokePending

```bash

Revokes `value` pending votes for `validator`

USAGE
    $ Marker  revokePending   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>--target <validatorAddress>  --lockedNum <value>  
  
Detailed introduction

     function revokePending(
        address validator,
        uint256 value,
        address lesser,
        address greater,
        uint256 index
    ) external nonReentrant returns (bool) {
        ...
         decrementPendingVotes(validator, account, value);
        ...
	// update the validator ranking
	decrementTotalVotes(validator, value, lesser, greater);
	//this will increment in voter NonvotingBalance 
        getLockedGold().incrementNonvotingAccountBalance(account, value);
        ...
    }
    function decrementTotalVotes(address validator, uint256 value, address lesser, address greater)
    private
    {
        ...
        votes.total.eligible.update(validator, newVoteTotal, lesser, greater);
        ...
    }
```


### RevokeActive

```bash
Revokes `value` active votes for `validator`

USAGE
    $ Marker  revokeActive   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>--target <validatorAddress>  --lockedNum <value>  
  
Detailed introduction
      function _revokeActive(
        address validator,
        uint256 value,
        address lesser,
        address greater,
        uint256 index
    ) internal returns (bool) {
	...
        decrementActiveVotes(validator, account, value);
	// update the validator ranking
        decrementTotalVotes(validator, value, lesser, greater);
        getLockedGold().incrementNonvotingAccountBalance(account, value);   
       ...
    }

    function decrementTotalVotes(address validator, uint256 value, address lesser, address greater)
    private
    {
         votes.total.eligible.update(validator, newVoteTotal, lesser, greater);
    }
```

## From the perspective of owner

Is a specific person who can modify contract rule settings


### setValidatorLockedGoldRequirements

```bash
Updates the Locked Gold requirements for Validators.

USAGE
  $ Marker setValidatorLockedGoldRequirements  --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --value <LockedGoldRequirementsvalue>   --duration <LockedGoldRequirementsduration>
  
Detailed introduction
    function setValidatorLockedGoldRequirements(uint256 value, uint256 duration)
    public
    onlyOwner 
    returns (bool)
    {
      ...
        require(
            value != requirements.value || duration != requirements.duration,
            "Validator requirements not changed"
        );
	validatorLockedGoldRequirements = LockedGoldRequirements(value, duration);
       ...
    }

```


## common commond

### CreateAccount

```bash

create a account

USAGE
  $ Marker  createAccount  --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path> --namePrefix <you can marker your account like "validator" or "voter" and so on that you want>

Detailed introduction

  function createAccount() public returns (bool) {
    ...
    account.exists = true;
    ...
  }
  function setName(string memory name) public {
    ...
    account.name = name;
    ...
  }
  function setAccountDataEncryptionKey(bytes memory dataEncryptionKey) public {
   ...
    //Setter for the data encryption key and version.
    //dataEncryptionKey secp256k1 public key for data encryption.
    account.dataEncryptionKey = dataEncryptionKey;
   ...
  }
 
```

### LockedMAP

```bash
Locks map to be used for voting or register validator

USAGE
  $ Marker  lockedMAP  --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path> --lockedNum <value>



Detailed introduction
   function lock() external payable nonReentrant {
   ...
    //This is equivalent to you transfer the map to LockedGold contract address 
    _incrementNonvotingAccountBalance(msg.sender, msg.value);
   ...
  }
 
  
```

### UnlockMAP

```bash
Unlocks gold that becomes withdrawable after the unlocking period.

USAGE
  $ Marker  unlockMap --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --lockedNum <value>



Detailed introduction
   function unlock(uint256 value) external nonReentrant {
   ...
    //if you are a validator you will be limit by 'balanceRequirement'
    uint256 balanceRequirement = getValidators().getAccountLockedGoldRequirement(msg.sender);
    require(
      balanceRequirement == 0 ||
        balanceRequirement <= getAccountTotalLockedGold(msg.sender).sub(value),
      "Trying to unlock too much gold"
    );
   ...
   //you can only unlock it after the unlocking period.
    uint256 available = now.add(unlockingPeriod);
    account.pendingWithdrawals.push(PendingWithdrawal(value, available));
  
  }
   //Attention reminder
   //if you are a validator you will be Limited by validatorLockedGoldRequirements.value
   function getAccountLockedGoldRequirement(address account) public view returns (uint256) {
        if (isValidator(account)) {
            return validatorLockedGoldRequirements.value;
        }
        return 0;
    }
    
```

### RelockMAP

```bash
Relocks map that has been unlocked but not withdrawn.

USAGE
  $ Marker   relockMAP --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --lockedNum <value> --relockIndex <from getPendingWithdrawals>

Detailed introduction
  //you need to know PendingWithdrawals index first , your can konw it by getPendingWithdrawals commond
  function relock(uint256 index, uint256 value) external nonReentrant {
    ...
    // the index used at this palces
    PendingWithdrawal storage pendingWithdrawal = account.pendingWithdrawals[index];
    ...
    if (value == pendingWithdrawal.value) {
      deletePendingWithdrawal(account.pendingWithdrawals, index);
    ...
  }
```

### WithdrawMap

```bash
Withdraws gold that has been unlocked after the unlocking period has passed.

USAGE
  $ Marker   withdrawMap      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --lockedNum <value> --WithdrawIndex <from getPendingWithdrawals>
  
Detailed introduction
  //you need to know PendingWithdrawals index first , your can konw it by getPendingWithdrawals commond
  function withdraw(uint256 index) external nonReentrant {
    ...
    //the unlocking period
    require(now >= pendingWithdrawal.timestamp, "Pending withdrawal not available");
    ...
  }
```
### GetNumRegisteredValidators

```bash
Returns the number of registered validators

USAGE
   $ Marker   getNumRegisteredValidators      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
  
```
### GetTopValidators

```bash
Returns the top n validator members for a particular validator.

USAGE
    $ Marker   getTopValidators      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --topNum <value>
NOTICE
  your can konw 'topNum' by getNumRegisteredValidators commond
```

### GetTotalVotesForEligibleValidators

```bash
Returns lists of all validator validators and the number of votes they've received.

USAGE
    $ Marker   getTotalVotesForEligibleValidators      --password <password>  --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
```
### getTotalVotes

```bash
Returns the total votes received across all validators.

USAGE
    $ Marker   getTotalVotes      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
```
### GetValidatorEligibility

```bash
Returns whether or not a validator is eligible to receive votes.

USAGE
    $ Marker   getValidatorEligibility      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <validatorAddress>
```
### GetPendingVotesForValidatorByAccount

```bash
Returns the pending votes for `validator` made by `account`.

USAGE
  $ Marker  getPendingVotesForValidatorByAccount      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <validatorAddress>
```
### GetActiveVotesForValidatorByAccount

```bash
Returns the active votes for `validator` made by `account`.

USAGE
   $ Marker  getActiveVotesForValidatorByAccount      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <validatorAddress>
```
### GetValidatorsVotedForByAccount

```bash
Returns the validators that `account` has voted for.

USAGE
   $ Marker   getValidatorsVotedForByAccount      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <voterAddress>

```
### GetAccountTotalLockedGold

```bash
Returns the total amount of locked gold for an account.

USAGE
  $ Marker   getAccountTotalLockedGold      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <validatorAddress>
```
### GetAccountNonvotingLockedGold

```bash
Returns the total amount of non-voting locked gold for an account.

USAGE
   $ Marker   getAccountTotalLockedGold      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <validatorAddress>
```
### GetAccountLockedGoldRequirement

```bash
Returns the current locked gold balance requirement for the supplied account.

USAGE
  $ Marker   getAccountLockedGoldRequirement      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
```
### GetPendingWithdrawals

```bash
Returns the pending withdrawals from unlocked gold for an account.

USAGE
  $ Marker   getPendingWithdrawals      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>   --target <targetAddress>

RETURN
  PendingWithdrawals
```
