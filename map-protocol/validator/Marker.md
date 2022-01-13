# Marker
### What is the Marker
Marker contains tools to make genesis.json and some client commands to better link atlas contracts

### Building Marker

```bash
go build -o Marker  *.go
```


| client   | commands   | summary |
| --------- | ------ | ------- |
| marker    | genesis     | create genesis to deloy contracts |
| marker    | validator   | Validator registration、 lockMap and other operations |
| marker    | voter        | voter vote、 lockMap and other operations|


## Marker genesis

`marker genesis` is a developer utility to easy running atlas blockchain testnets and related jobs around testnets.

Its main advantage over previous solutions is that it's able to create a `genesis.json` where all core conctracts are already deployed in it.



## Using marker genesis

### Generating a genesis.json

create the `genesis.json`;

first you need to config the markerConfig.json like this:
```bash

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
  ]
}
```
then to do so run:

```bash
marker genesis --buildpath path/to/protocol/build
```

Where `buildpath` is the path to truffle compile output folder. By default it will use `MAP_CONTRACTS` environment variable as `$MAP_CONTRACTS/build/contracts`.

This will create a `genesis.json`.


## Flag
| Flag        | Usage  |
| ---------   | ------ | 
| key         | Private key file path           | 
| keystore    | Keystore file path   Belong to <common params>            | 
| namePrefix    | namePrefix when setName      | 
| password    | Keystore file`s password   (defult value "")  Belong to <common params>| 
| commission    | This represents the share of the epoch rewards given to elected Voters(0< commission <100)| 
| lesser    | The validator receiving fewer votes than the validator for which the vote was revoked ,or 0 if that validator has the fewest votes of any validator validator     | 
| greater    | Greater The validator receiving more votes than the validator for which the vote was revoked,or 0 if that validator has the most votes of any validator validator.  | 
| voteNum    | The amount of map to use to vote        | 
| topNum    | topNum of validator        | 
| lockedNum    | The amount of map to lock 、unlock、relock、withdraw          | 
| withdrawIndex    |Relocks gold that has been unlocked but not withdrawn. | 
| relockIndex    | Relocks gold that has been unlocked but not withdrawn.  |
| validatorIndex    | used for revokePending or revokeActive       | 
| rpcaddr    | HTTP-RPC server listening interface    Belong to <common params>   | 
| rpcport    | HTTP-RPC server listening port     Belong to <common params>  | 
| value    | your transfer value      | 
| duration    | duration The time (in seconds) that these requirements persist for.     | 
| target    | target address      | 








## Validator commond

###  registerValidator
```bash
Registers a validator

USAGE
  $ Marker validator registerValidator   <common params>   --commission <value> --Lesser <from getTotalVotesForEligibleValidators> --greater <from getTotalVotesForEligibleValidators>
  
Detailed introduction
    //you need to know lesser or greater first , your can konw it by getTotalVotesForEligibleValidators commond
    function registerValidator(
        uint256 commission,
        address lesser,
        address greater,
        bytes calldata ecdsaPublicKey,
        bytes calldata blsPublicKey,
        bytes calldata blsPop
    ) external nonReentrant returns (bool) {
        ...
		//you need locded enough Map first
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
		//the lesser and greater will be used by votes.total.eligible
		//you need to know lesser or greater first 
        votes.total.eligible.insert(validator, value, lesser, greater);
        ...
    }
 ``` 




###  deregisterValidator
```bash
De-registers a validator.

USAGE
    $ Marker validator deregisterValidator   <common params>    --validatorIndex <from getRegisteredValidatorSigners>
    
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

## voter

###  vote
```bash
Increments the number of total and pending votes for `validator`.

USAGE
    $ Marker voter vote   <common params>    --target <from getTotalVotesForEligibleValidators> --voteNum <value> --lesser <from getTotalVotesForEligibleValidators> --greater <from getTotalVotesForEligibleValidators>
    
Detailed introduction
    //you need to know lesser and greater first , your can konw it by getTotalVotesForEligibleValidators commond
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
		//the lesser and greater will be used by votes.total.eligible
		//you need to know lesser or greater first 
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



###  activate
```bash
Converts `account`'s pending votes for `validator` to active votes.

USAGE
   $ Marker voter activate   <common params>    --target <from getTotalVotesForEligibleValidators> 
  
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



###  revokePending
```bash
Revokes `value` pending votes for `validator`

USAGE
    $ Marker voter     revokePending   <common params> --target <validatorAddress> --validatorIndex  <from getValidatorsVotedForByAccount> --lockedNum <value>  --Lesser <from getTotalVotesForEligibleValidators> --greater <from getTotalVotesForEligibleValidators>
  
Detailed introduction
    //you need to know lesser or greater first , your can konw it by getTotalVotesForEligibleValidators commond
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
		//the lesser and greater will be used by votes.total.eligible
		//you need to know lesser or greater first 
        votes.total.eligible.update(validator, newVoteTotal, lesser, greater);
	    ...
    }
 ``` 


###  revokeActive
```bash
Revokes `value` active votes for `validator`

USAGE
      $ Marker voter revokeActive   <common params> --target <validatorAddress> --validatorIndex  <from getValidatorsVotedForByAccount> --lockedNum <value>  --Lesser <from getTotalVotesForEligibleValidators> --greater <from getTotalVotesForEligibleValidators>
  
Detailed introduction
    //you need to know lesser or greater first , your can konw it by getTotalVotesForEligibleValidators commond
      function _revokeActive(
        address validator,
        uint256 value,
        address lesser,
        address greater,
        uint256 index
    ) internal returns (bool) {
	   ...
        decrementActiveVotes(validator, account, value);
		//the lesser and greater will be used by votes.total.eligible
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



## Owner commond

###  setValidatorLockedGoldRequirements
```bash
Updates the Locked Gold requirements for Validators.

USAGE
  $ Marker voter     setValidatorLockedGoldRequirements  <common params>   --value <LockedGoldRequirementsvalue>   --duration <LockedGoldRequirementsduration>
  $ Marker validator setValidatorLockedGoldRequirements  <common params>   --value <LockedGoldRequirementsvalue>   --duration <LockedGoldRequirementsduration>
  
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


###  CreateAccount
```bash

create a account

USAGE
  $ Marker validator createAccount  <common params> --namePrefix "validator"
  $ Marker voter     createAccount  <common params> --namePrefix "voter"

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


###  LockedMAP
```bash
Locks map to be used for voting or register validator

USAGE
  $ Marker validator lockedMAP  <common params>  --lockedNum <value>
  $ Marker voter lockedMAP     <common params>   --lockedNum <value> 



Detailed introduction
   function lock() external payable nonReentrant {
   ...
    //This is equivalent to you transfer the map to LockedGold contract address 
    _incrementNonvotingAccountBalance(msg.sender, msg.value);
   ...
  }
 
  
```
###  unlockMAP
```bash
Unlocks gold that becomes withdrawable after the unlocking period.

USAGE
  $ Marker validator unlockMap <common params>   --lockedNum <value>
  $ Marker voter     unlockMap <common params>   --lockedNum <value>



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



###  relockMAP
```bash
Relocks map that has been unlocked but not withdrawn.

USAGE
  $ Marker validator  relockMAP <common params>   --lockedNum <value> --relockIndex <from getPendingWithdrawals>
  $ Marker voter      relockMAP <common params>   --lockedNum <value> --relockIndex <from getPendingWithdrawals>

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


###  withdrawMap
```bash
Withdraws gold that has been unlocked after the unlocking period has passed.

USAGE
  $ Marker validator  withdrawMap      <common params>   --lockedNum <value> --WithdrawIndex <from getPendingWithdrawals>
  $ Marker voter      withdrawMap      <common params>   --lockedNum <value> --WithdrawIndex <from getPendingWithdrawals>
  
Detailed introduction
  //you need to know PendingWithdrawals index first , your can konw it by getPendingWithdrawals commond
  function withdraw(uint256 index) external nonReentrant {
    ...
	//the unlocking period
    require(now >= pendingWithdrawal.timestamp, "Pending withdrawal not available");
    ...
  }
 ``` 





###  getNumRegisteredValidators
```bash
Returns the number of registered validators

USAGE
   $ Marker validator  getNumRegisteredValidators      <common params>   
   $ Marker voter      getNumRegisteredValidators      <common params>  
  
 ``` 


###  getTopValidators
```bash
Returns the top n validator members for a particular validator.

USAGE
    $ Marker validator  getTopValidators      <common params>   --topNum <value>
    $ Marker voter      getTopValidators      <common params>   --topNum <value>
NOTICE
  your can konw 'topNum' by getNumRegisteredValidators commond
 ``` 


###  getTotalVotesForEligibleValidators
```bash
Returns lists of all validator validators and the number of votes they've received.

USAGE
    $ Marker validator  getTotalVotesForEligibleValidators      <common params>   
    $ Marker voter      getTotalVotesForEligibleValidators      <common params>   
 ```   


###  getValidatorEligibility
```bash
Returns whether or not a validator is eligible to receive votes.

USAGE
    $ Marker validator  getValidatorEligibility      <common params>   --target <validatorAddress>
    $ Marker voter      getValidatorEligibility      <common params>   --target <validatorAddress>
```   


###  getPendingVotesForValidatorByAccount
```bash
Returns the pending votes for `validator` made by `account`.

USAGE
  $ Marker validator  getPendingVotesForValidatorByAccount      <common params>   --target <validatorAddress>
  $ Marker voter      getPendingVotesForValidatorByAccount      <common params>   --target <validatorAddress>
```   


###  getActiveVotesForValidatorByAccount
```bash
Returns the active votes for `validator` made by `account`.

USAGE
   $ Marker validator  getActiveVotesForValidatorByAccount      <common params>   --target <validatorAddress>
   $ Marker voter      getActiveVotesForValidatorByAccount      <common params>   --target <validatorAddress>
```   



###  getValidatorsVotedForByAccount
```bash
Returns the validators that `account` has voted for.

USAGE
   $ Marker validator  getValidatorsVotedForByAccount      <common params>   --target <voterAddress>
   $ Marker voter      getValidatorsVotedForByAccount      <common params>   --target <voterAddress>

``` 




###  getAccountTotalLockedGold
```bash
Returns the total amount of locked gold for an account.

USAGE
  $ Marker validator  getAccountTotalLockedGold      <common params>   --target <validatorAddress>
  $ Marker voter      getAccountTotalLockedGold      <common params>   --target <validatorAddress>
``` 



###  getAccountNonvotingLockedGold
```bash
Returns the total amount of non-voting locked gold for an account.

USAGE
   $ Marker validator  getAccountTotalLockedGold      <common params>   --target <validatorAddress>
   $ Marker voter      getAccountTotalLockedGold      <common params>   --target <validatorAddress>
``` 


###  getAccountLockedGoldRequirement
```bash
Returns the current locked gold balance requirement for the supplied account.

USAGE
  $ Marker validator  getAccountLockedGoldRequirement      <common params>   
  $ Marker voter      getAccountLockedGoldRequirement      <common params>   
``` 



###  getPendingWithdrawals
```bash
Returns the pending withdrawals from unlocked gold for an account.

USAGE
  $ Marker validator  getPendingWithdrawals      <common params>    --target <targetAddress>
  $ Marker voter      getPendingWithdrawals      <common params>    --target <targetAddress>

RETURN
  PendingWithdrawals
``` 
  
  