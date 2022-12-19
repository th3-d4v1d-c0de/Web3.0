This is a Guide on how to create a basic smart contract using Truffle Suite.

# Prerequisites 
- Have NodeJS installed
- Have Truffle Suite installed

# Steps to deploy
1. Create a working directory 
 ```
 mkdir MyFirstSC
 cd MyFirstSC
 ```
 2. Truffle boxes are boilerplates that allow to create a contract faster (available [here](https://trufflesuite.com/boxes/)). For this example the MetaCoin box will do
 ```
 truffle unbox MetaCoin
 ```
 Result:
 ```
 Starting unbox...
=================

✓ Preparing to download box
✓ Downloading
✓ Cleaning up temporary files
✓ Setting up box

Unbox successful, sweet!

Commands:

  Compile contracts: truffle compile
  Migrate contracts: truffle migrate
  Test contracts:    truffle test
```
3. Check the downloaded files
```
.
├── LICENSE
├── contracts
│   ├── ConvertLib.sol
│   └── MetaCoin.sol
├── migrations
│   └── 1_deploy_contracts.js
├── test
│   ├── TestMetaCoin.sol
│   └── metacoin.js
└── truffle-config.js

3 directories, 7 files
```
4. The contracts **ConvertLib.sol** and **MetaCoin.sol** can be deleted since we are going to create our own.
5. Create a new contract under the **contracts** folder
```
// Code developed by Wei-Meng Lee (https://blog.cryptostars.is/writing-smart-contracts-on-ethereum-5b917b69049e)

// SPDX-License-Identifier: MIT  
pragma solidity ^0.8;  
  
contract ProofOfExistence {  
	//---store the hash of the strings---  
	mapping (bytes32 => bool) private proofs;  
	  
	//--------------------------------------------------  
	// Store a proof of existence in the contract state  
	//--------------------------------------------------  
	function storeProof(bytes32 proof) private {  
		// use the hash as the key  
		proofs[proof] = true;  
	}  
	  
	//----------------------------------------------  
	// Calculate and store the proof for a document  
	//----------------------------------------------  
	function notarize(string memory document) public {  
	// call storeProof() with the hash of the string  
		storeProof(proofFor(document));  
	}  
	  
	//--------------------------------------------  
	// Helper function to get a document's sha256  
	//--------------------------------------------  
	// Takes in a string and returns the hash of the string  
	function proofFor(string memory document) private pure  
	returns (bytes32) {  
		// converts the string into bytes array and then hash it  
		return sha256(bytes(document));  
	}  
  
	//----------------------------------------  
	// Check if a document has been notarized  
	//----------------------------------------  
	function checkDocument(string memory document) public view  
	returns (bool){  
		// use the hash of the string and check the proofs mapping  
		// object  
		return proofs[proofFor(document)];  
	}  
}
```

6. Now let's edit the **1_deploy_contracts.js** file in the **migrations** folder
Original:
```
const ConvertLib = artifacts.require("ConvertLib");
const MetaCoin = artifacts.require("MetaCoin");

module.exports = function(deployer) {
  deployer.deploy(ConvertLib);
  deployer.link(ConvertLib, MetaCoin);
  deployer.deploy(MetaCoin);
};
```
Modified:
```
const Proof = artifacts.require("Proofofexistence");

module.exports = function(deployer) {  
  deployer.deploy(Proof);  
};
```

7. It's Ganache time. Let's open it and select **NEW WORKSPACE** > **ETHEREUM**
![Ganache start](/images/ganache_start.png "Ganache start")
8. Now let's add the network, that can be achieve by clicking in ADD Network an then selecting the **truffle-config.js** in our smart contract folder
![Ganache add network](/images/ganache_add_network.png "Ganache add network")
9. Explore the several tabs in Ganache. Checking the contracts tab is possible to see that the contract is not there yet.
![Ganache without contracts](/images/ganache_no_contracts.png "Ganache without contracts")
10. Time to deploy the contract, for that execute the following:
```
truffle migrate
```
Result:
```
Compiling your contracts...
===========================
> Compiling ./contracts/Proofofexistence.solAttempt #1
> Artifacts written to /MyFirstSC/build/contracts
> Compiled successfully using:
   - solc: 0.8.13+commit.abaa5c0e.Emscripten.clang
⠧ Fetching solc version list from solc-bin. Attempt #1
✓ Downloading compiler. Attempt #1.
Starting migrations...
======================
> Network name:    'ganache'
> Network id:      5777
> Block gas limit: 6721975 (0x6691b7)


1_deploy_contracts.js
=====================
⠙ Fetching solc version list from solc-bin. Attempt #1
   Deploying 'ProofOfExistence' #1.
   ----------------------------
   > transaction hash:    0x7176b2813c31b87849cde22a2ffeae65daf181f7030b4bd5e881574737d8fbe2
   > Blocks: 0            Seconds: 0lc-bin. Attempt #1
   > contract address:    0x7EfA54D112A6d476db7d7A0572A562cB0C337048
   > block number:        1econds: 0
   > block timestamp:     1671450786
   > account:             0x618F1b97Bb43794694fDCf2B80F1414D3DF86d6F
   > balance:             99.99415282
   > gas used:            292359 (0x47607)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00584718 ETH

   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00584718 ETH

Summary
=======
> Total deployments:   1
> Final cost:          0.00584718 ETH
```
11. Congrats your contract is now deployed on Ganche.
![Ganache contract deployed](/images/ganache_contracts.png "Ganache contract deployed")
![Ganache contract transaction](/images/ganache_contract_transaction.png "Ganache contract transaction")
# Test the contract functionality 
- To interact with the contract deployed on Ganache execute the command:
```
truffle console
```
- Now execute commands related to your contract. The following is an example of what can be done:
```
truffle(ganache)> let instance = await ProofOfExistence.deployed()
undefined
truffle(ganache)> instance.notarize("th3_d4v1d_c0de rulezzz!")
{
  tx: '0xd2892c6df9053d6b2c8dafac9c466dd462a0ddbca375481e4f12de42d1e23251',
  receipt: {
    transactionHash: '0xd2892c6df9053d6b2c8dafac9c466dd462a0ddbca375481e4f12de42d1e23251',
    transactionIndex: 0,
    blockHash: '0x25d6e70371e73d99898b3590a3d5a5b2224c4585512b3c497cbbcdca5cccf659',
    blockNumber: 2,
    from: '0x618f1b97bb43794694fdcf2b80f1414d3df86d6f',
    to: '0x7efa54d112a6d476db7d7a0572a562cb0c337048',
    gasUsed: 45253,
    cumulativeGasUsed: 45253,
    contractAddress: null,
    logs: [],
    status: true,
    logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
    rawLogs: []
  },
  logs: []
}
truffle(ganache)> instance.checkDocument("Hello, world!")
false
truffle(ganache)> instance.checkDocument("th3_d4v1d_c0de rulezzz!")
true
```

Check the results on Ganache GUI also.

Congrats! You've now sucessfully deployed a contract locally. 