## How To Become Validator

### Step 1: [createAccount](Validator-Tool-Marker.md#CreateAccount)

Manage your account, keys, and metadata.

Keep your locked MAP more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked MAP in storage.

```shell
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

### Step 2: [lockedMAP](Validator-Tool-Marker.md#LockedMAP)

Locks MAP to be used in validator elections.
You must lock 1000k MAP to become validator.

```shell
Detailed
   function lock() external payable nonReentrant {
   ...
    //This is equivalent to you transfer the map to LockedGold contract address 
    _incrementNonvotingAccountBalance(msg.sender, msg.value);
   ...
  }
```

### Step 3: [validator register](Validator-Tool-Marker.md#Register)

Register a new Validator.
You need to use `register` command to pass in your own information

```shell
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

	//This structure will serialize the validator according to the number of votes
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

## How To Vote

### Step 1: [createAccount](Validator-Tool-Marker.md#CreateAccount)

Manage your account, keys, and metadata.

Keep your locked MAP more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked MAP in storage.

```shell
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

### Step 2: [lockedMAP](Validator-Tool-Marker.md#LockedMAP)

Locks MAP to vote.

```shell
Detailed
   function lock() external payable nonReentrant {
   ...
    //This is equivalent to you transfer the map to LockedGold contract address 
    _incrementNonvotingAccountBalance(msg.sender, msg.value);
   ...
  }
```

### Step 3: [vote](Validator-Tool-Marker.md#Vote)

Vote to your target validator when you go to this step, your ticket will be in Pengding status, and you need to deactivate it to finally receive the reward

```shell
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
		//2.The number of votes cannot exceed the number of `maxNumValidatorsVotedFor`
        if (!alreadyVotedForValidator) {
            require(validators.length < maxNumValidatorsVotedFor, "Voted for too many validators");
            validators.push(validator);
        }
	
		//the votes will be pending
        incrementPendingVotes(validator, account, value);
        incrementTotalVotes(validator, value, lesser, greater);
      
    }

	function incrementTotalVotes(address validator, uint256 value, address lesser, address greater)
    {
        ...
        votes.total.eligible.update(validator, newVoteTotal, lesser, greater);
    }

	 function incrementPendingVotes(address validator, address account, uint256 value) private {  
      ...
	    //the ticket will be Recorded by validatorPending
		//the ticket in the validatorPending will do not participate in Epoch Awards
		//if you want to participate in Epoch Awards you need to active 
		//the active commond is activate
        PendingVote storage pendingVote = validatorPending.byAccount[account];
        pendingVote.value = pendingVote.value.add(value);
		//your ticket will be marker pendingVote.epoch
        pendingVote.epoch = getEpochNumber();
    }
```

### Step 4: [activate](Validator-Tool-Marker.md#Activate)

Activate your ticket to get reward
This operation need to be greater than your pending vote epoch

```shell
Detailed
    function _activate(address validator, address account) internal returns (bool) {
        ...
		//Activate need greater than your pending vote epoch
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

## How To withdraw Locked MAP

### Step 1: [unlock](Validator-Tool-Marker.md#UnlockMAP)

Unlocks MAP that becomes withdrawable after the unlocking period.

You can only unlock your nonvoting map.

if you are a validator you will be limit by 'balanceRequirement'(10k MAP)

```shell
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
	//if you are a validator you will be Limited by `validatorLockedGoldRequirements.value`(10k MAP)
   function getAccountLockedGoldRequirement(address account) public view returns (uint256) {
        if (isValidator(account)) {
            return validatorLockedGoldRequirements.value;
        }
        return 0;
    }
```

### Step 2:withdraw

Withdraws MAP that has been unlocked after the unlocking period has passed.

```shell
Detailed
  //you need to know PendingWithdrawals index first , your can konw it by getPendingWithdrawals commond
  function withdraw(uint256 index) external nonReentrant {
    ...
	//the unlocking period
    require(now >= pendingWithdrawal.timestamp, "Pending withdrawal not available");
    ...
  }
```

## How To withdraw votes

### Step 1:

[revokePending](Validator-Tool-Marker.md#RevokePending):

If your vote is not active , use 'revokePending'commond.

[revokeActive](Validator-Tool-Marker.md#RevokeActive):

If your vote is active, use 'revokeActive'commond.
Both of these methods will put the map in your nonvoting map

```shell
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

### Step 2:

Now your votes`s state has changed from pending or active to locking state.
the next step is same to [How To withdraw Locked MAP](#How To withdraw Locked MAP)
