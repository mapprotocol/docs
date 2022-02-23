# Marker

### What is the Marker

Marker contains tools to make genesis.json and some client commands to better link atlas contracts

### Building Marker

```shell
cd github.com/mapprotocol/atlas/cmd/marker
go build -o Marker  *.go
```

| client | commands  | summary                                               |
| -------- | ----------- | ------------------------------------------------------- |
| marker | genesis   | create genesis to deloy contracts                     |
| marker | validator | Validator registration、 lockMap and other operations |
| marker | voter     | voter vote、 lockMap and other operations             |


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
| lockedNum     | The amount of map to lock 、unlock、relock、withdraw  (The unit is 10^18)                            |
| withdrawIndex | Relocks gold that has been unlocked but not withdrawn.                                              |
| relockIndex   | Relocks gold that has been unlocked but not withdrawn.                                              |
| rpcaddr       | HTTP-RPC server listening interface    ( Belong to common params)                                   |
| rpcport       | HTTP-RPC server listening port    ( Belong to common params)                                        |
| value         | your transfer value                                                                                 |
| duration      | duration The time (in seconds) that these requirements persist for.                                 |
| target        | the target address to query                                                                         |
| validator     | validator address                                                                                   |

## From the perspective of validator

### Register

Registers a validator

```shell

USAGE
  $ Marker  register   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --commission <value> 
  
Detailed
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

If you have not creat Account or locked the Map, you can quickly register through the `quicklyRegister` command, which
integrates the `createAccount` `lockedMAP`

```shell

USAGE
$ Marker  quicklyRegister   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>--lockedNum <value> --commission <value>
```

### deregister

De-registers a validator.

```shell

USAGE
$ Marker  deregister   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>

Detailed

    function deregisterValidator(uint256 index) external nonReentrant returns (bool) {
        ...
        int256 requirementEndTime = validator.registerTimestamp.add(validatorLockedGoldRequirements.duration);
    
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

Increments the number of total and pending votes for `validator`.

```shell

USAGE
$ Marker  vote   --password < password > --rpcaddr < rpcaddr > --rpcport < rpcport > --keystore < keystore path > --validator < validator > --voteNum < value >

Detailed

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

If you have not creat Account or locked the Map, you can quickly vote through the `quicklyVote` command, which
integrates the `createAccount` `lockedMAP`

```shell
USAGE
$ Marker  quicklyVote   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport>--keystore <keystore path> --lockedNum <value>  --voteNum <value> --validator <validator>
```

### Activate

Converts `account`'s pending votes for `validator` to active votes.

```shell
USAGE
   $ Marker  activate   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>   --validator <from getTotalVotesForEligibleValidators> 
  
Detailed
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

Revokes `value` pending votes for `validator`

```shell

USAGE
    $ Marker  revokePending   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>--validator <validatorAddress>  --lockedNum <value>  
  
Detailed

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

Revokes `value` active votes for `validator`

```shell

USAGE
    $ Marker  revokeActive   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>--validator <validatorAddress>  --lockedNum <value>  
  
Detailed
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

owner is a specific person who can modify contract rule settings

### setValidatorLockedGoldRequirements

Updates the Locked Gold requirements for Validators.

```shell

USAGE
  $ Marker setValidatorLockedGoldRequirements  --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --value <LockedGoldRequirementsvalue>   --duration <LockedGoldRequirementsduration>
  
Detailed
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

create a account

```shell


USAGE
  $ Marker  createAccount  --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path> --namePrefix <you can marker your account like "validator" or "voter" and so on that you want>

Detailed

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

Locks map to be used for voting or register validator

```shell

USAGE
  $ Marker  lockedMAP  --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path> --lockedNum <value>


Detailed
   function lock() external payable nonReentrant {
   ...
    //This is equivalent to you transfer the map to LockedGold contract address 
    _incrementNonvotingAccountBalance(msg.sender, msg.value);
   ...
  }

```

### UnlockMAP

Unlocks gold that becomes withdrawable after the unlocking period.

```shell

USAGE
  $ Marker  unlockMap --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --lockedNum <value>

Detailed
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

Relocks map that has been unlocked but not withdrawn.

```shell

USAGE
  $ Marker   relockMAP --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --lockedNum <value> --relockIndex <from getPendingWithdrawals>

Detailed
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

Withdraws gold that has been unlocked after the unlocking period has passed.

```shell

USAGE
  $ Marker   withdrawMap      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --lockedNum <value> --WithdrawIndex <from getPendingWithdrawals>
  
Detailed
  //you need to know PendingWithdrawals index first , your can konw it by getPendingWithdrawals commond
  function withdraw(uint256 index) external nonReentrant {
    ...
    //the unlocking period
    require(now >= pendingWithdrawal.timestamp, "Pending withdrawal not available");
    ...
  }
```

### GetNumRegisteredValidators

Returns the number of registered validators

```shell

USAGE
   $ Marker   getNumRegisteredValidators      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
  
```

### GetTopValidators

Returns the top n validator members for a particular validator.

```shell

USAGE
    $ Marker   getTopValidators   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --topNum <value>
NOTICE
  your can konw 'topNum' by getNumRegisteredValidators commond
```

### GetTotalVotesForEligibleValidators

Returns lists of all validator validators and the number of votes they've received.

```shell

USAGE
    $ Marker   getTotalVotesForEligibleValidators      --password <password>  --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
```

### getTotalVotes

Returns the total votes received across all validators.

```shell

USAGE
    $ Marker   getTotalVotes      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
```

### GetValidatorEligibility

Returns whether or not a validator is eligible to receive votes.

```shell

USAGE
    $ Marker   getValidatorEligibility   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --validator <validatorAddress>
```

### GetValidator

Returns the validator`s information.

```shell

USAGE
    $ Marker   getValidator   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --validator <validatorAddress>
```

### GetRewardInfo

Returns the awards of the last epoch

```shell

USAGE
    $ Marker   getRewardInfo   --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path> 
```

### GetPendingVotesForValidatorByAccount

Returns the pending votes for `validator` made by `account`.

```shell

USAGE
  $ Marker  getPendingVotesForValidatorByAccount      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --validator <validatorAddress>
```

### GetPendingInfoForValidator

Returns the pending votes for `validator` made by `account` And the pending Epoch.

```shell
USAGE
  $ Marker  GetPendingInfoForValidator      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --validator <validatorAddress>
```

### GetActiveVotesForValidatorByAccount

Returns the active votes for `validator` made by `account`.

```shell

USAGE
   $ Marker  getActiveVotesForValidatorByAccount      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --validator <validatorAddress>
```

### GetValidatorsVotedForByAccount

Returns the validators that `account` has voted for.

```shell

USAGE
   $ Marker   getValidatorsVotedForByAccount      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --validator <voterAddress>

```

### GetAccountTotalLockedGold

Returns the total amount of locked gold for an account.

```shell

USAGE
  $ Marker   getAccountTotalLockedGold      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <targetAddress>
```

### GetAccountNonvotingLockedGold

Returns the total amount of non-voting locked gold for an account.

```shell

USAGE
   $ Marker   getAccountTotalLockedGold      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  --target <targetAddress>
```

#### GetAccountLockedGoldRequirement

Returns the current locked gold balance requirement for the supplied account.

```shell

USAGE
  $ Marker   getAccountLockedGoldRequirement      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>  
```

### GetPendingWithdrawals

Returns the pending withdrawals from unlocked gold for an account.

```shell

USAGE
  $ Marker   getPendingWithdrawals      --password <password> --rpcaddr <rpcaddr>  --rpcport <rpcport> --keystore <keystore path>   --target <targetAddress>

RETURN
  PendingWithdrawals
```
