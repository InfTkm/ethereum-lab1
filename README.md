## Spring 2020 Developing Blockchain Use Cases Lab 1                             
### Carnegie Mellon University                  
### Due: 11:59 PM, Monday, March 28, 2022                                
### 10 Points
### Lab Assistance provided by Michael McCarthy
### Email: mm6@andrew.cmu.edu
### Zoom URL: Please see Canvas and look under "Pages".

**Learning Objectives:** In this lab the student will set up an Ethereum
development environment (using Truffle and Ganache) and deploy four smart
contracts to an Ethereum test blockchain. The smart contracts will be
written in the Solidity programming language.

Three of these contracts will involve working with fungible tokens and the fourth contract is a non-fungible (NFT) contract.

We will experiment with running "transactions". Transactions cause state
changes, cost ether (for using gas), and may only return responses at a later time.

We will also run "calls". Calls do not change state on the blockchain and
cost nothing to run (no gas). They are processed immediately and provide
an immediate return value.

We will review the various views of the blockchain that Ganache provides.

Solidity event generation and a testing framework are also introduced and the student will modify an existing contract.

The contracts themselves will be studied in detail at a later date. For
now, we want to get some experience deploying and interacting with the
Solidity code and the environment provided by client side Truffle and
server side Ganache.

## Part 1. Installations

1) Download and install Google Chrome.
2) Install the Metamask extension for Google Chrome.
   See: https://metamask.io/
3) Install git.
   See https://git-scm.com/downloads
4) Download and install node.js and npm.
   The node package manager (npm) is included with the download of
   node.js. See https://nodejs.org/en/download/
5) Download and install truffle.
   See: https://truffleframework.com/docs/truffle/getting-started/installation
   npm install -g truffle
6) Download and install Ganache.
   See: https://truffleframework.com/ganache
7) If you want to use Atom as your editor (recommended) , download and install atom.
   See: https://atom.io
8) If you are using Atom, you may want to download and install
   language-solidity for Atom.
   See: https://atom.io/packages/language-solidity
9) If you are using Atom, you may want to download and install atom-solidity-linter
   for Atom.
   See: https://atom.io/packages/atom-solidity-linter

## Part 2  (Modified from "Mastering Ethereum" by Antonopoulos and Wood)

1) Run ganache quick start and leave it running for the remainder of this part.
2) Create a new project directory named DBUC_Lab1_Part2 and cd into it.
3) Execute the command:

 ```

   truffle init

```

4) Examine the directory structure:

* **contracts** holds solidity source code
  * Migrations.sol is a deployment contract
* **migrations** holds javascript code for efficient redeployments
  * 1_initial_migrations.js
  * This first migration deploys the Migrations.sol contract.
* **test**
  * This directory is for writing tests in Javascript.
It typically uses the mocha framework and Chai Assertions library.
In more complex deployments, this directory structure will mirror
the directory structure required by the application.
* **truffle-config.js**

  * This Javascript file contains configuration parameters for this truffle project.

5) Create a new contract in the contracts directory. This file will be named
   Faucet.sol. The content of Faucet.sol is:

   ```
   // Solidity code Faucet.sol
   pragma solidity ^0.5.0;
   contract Faucet {
       function withdraw(uint withdraw_amount) public {
           require(withdraw_amount <= 100000000000000000000);
           msg.sender.transfer(withdraw_amount);
       }
       function() external payable {}
   }
   ```
6) Create a new migration file in the migrations directory. Do
   not remove the initial migration 1_initial_migration.js.
   This new file will be named 2_deploy_migration.js.
   This file will contain a migration script to deploy Faucet.sol.
   The content of 2_deploy_migrations.js is:
   ```

   // Javascript in 2_deploy_migrations.js to deploy Faucet.sol
   var Faucet = artifacts.require("./Faucet.sol");
   module.exports = function(deployer) {
     deployer.deploy(Faucet);
   };

   ```

7) To create a package.json file, run the following command
   from within the project directory:

 ```
   npm init
 ```

   Take the suggested defaults.

8) From the project directory, run the command

 ```

  npm install dotenv truffle-wallet-provider ethereumjs-wallet

 ```
   This creates a node_modules directory. Ignore the warnings.

9) To compile and deploy the two contracts (Migrations.sol and Faucet.sol)
   to the blockchain (represented by Ganache), make sure that Ganache is running
   and execute the command:

 ```
   truffle migrate --reset
 ```

10) To interact with the deployed contract and accounts, use the truffle console.

    a. From the command line, execute the following commands:

    ```
    truffle console
    ```

    b. Access the contract with an asynchronous request. Use a
       callback function and a promise. The callback
       function is defined within the "then" clause.
       Execute the following command within the truffle console.

      ```
      Faucet.deployed().then(function(x){ myApp = x; });
      ```

      The response should be 'undefined'.

    c. To view the response enter the name myApp.

 ```
      myApp

  ```
  d. To get access to a web3 object, enter three lines of Javascript.
       The first two will return 'undefined'.

 ```

      var Web3 = require('web3');
      var web3 = new Web3(new Web3.providers.HttpProvider('http://127.0.0.1:7545'));
      web3.isConnected() // should return true if all three lines worked.

```

   e. Get the balance on the contract.

 ```
      contractBalance = web3.eth.getBalance(Faucet.address).toNumber()
 ```

   f. View the account addresses available on Ganache:

 ```
      web3.eth.accounts
 ```

   g. View the first address. This address is our default address provided
      by Ganache. We are in possession of its private key. Ganache has
      provided this account with 100 eth (but only usable on Ganache).

  ```
      web3.eth.accounts[0]
  ```

   h. View the contract address.

  ```
      Faucet.address
  ```

   i. Using web3, transfer eth from the first account to the contract.
      The value returned is the transaction hash. Check the logs in Ganache.

  ```
      web3.eth.sendTransaction({from:web3.eth.accounts[0],to:Faucet.address,value:web3.toWei(0.5, 'ether')})

  ```

   j. Check that the balance on the contract is higher than before.

  ```
      web3.eth.getBalance(Faucet.address).toNumber();
  ```

   k. Withdraw some ether from the contract and deposit to account[0]. This returns 'undefined'.

  ```
      Faucet.deployed().then(instance => {receipt = instance.withdraw(web3.toWei(0.1,'ether'))});
  ```

   l. But we can examine the returned receipt.

```
      receipt
```
   m. Check the balance on account[0]. Should be 99591514500000000000.

  ```
      web3.eth.getBalance(web3.eth.accounts[0]).toNumber();
  ```

   n. To exit the truffle console hit control d.

   ```
   truffle(ganache)>ctrl+d

   ```

  :checkered_flag:**11) At this point, take three screenshots of Ganache. Take a screenshot of your Ganache Accounts, Blocks, and Transactions. Place these in a clearly labeled single Word or PDF document named Lab1Part2.doc or Lab1Part2.pdf. **


## Part 3  (Modified from "Mastering Ethereum" by Antonopoulos and Wood)

1) Create a new project directory named DBUC_Lab1_Part3 and cd into it.

2) Execute the command:

   ```
   truffle init

   ```
3) Run a new instance of ganache. Configure Ganache as follows:

   Select "new workspace".

   Select "add project".

   Choose the file "truffle-config.js" in your "DBUC_Lab1_Part3" project directory.

   Provide the workspace with an appropriate name.

   Save the workspace.

   On the server side (Ganache) you should now be able to view the following tabs:
   Accounts, Blocks, Transactions, Contracts, Events, and Logs. These are all now
   associated with your project.

4) Create a new contract in the contracts directory and name it Faucet.sol.

5) [Click here to see your Faucet.sol code.](../../blob/master/Faucet.sol)

6) Use Part 2 as a guide and deploy this new contract to Ganache.
      That is, repeat the following steps from Part 2: Step 6, 7,
      8, 9 and Step 10a to 10d - while replacing "myApp" with "myFaucet"
      in Step 10b.

7) Send a total of 2 ether to the contract with these two commands:

 ```
      myFaucet.send(web3.toWei(1,"ether")).then(res => { console.log(res.logs[0].event)})

 ```    
 ```
      myFaucet.send(web3.toWei(1,"ether")).then(res => { console.log(res.logs[0].event, res.logs[0].args)})

 ```

 8) Send a "withdraw" transaction to the contract. This will be a request to withdraw 2 eth.

     In your own words, describe what happens on the client.

     In your own words, describe the most recent activities on the Ganache Logs user interface.

 9)  Send a single request to withdraw 0.1 eth from the contract.

     Show the receipt that is returned from this request.

10)  Make enough withdrawals from the contract so that it (the contract) runs out of eth. We are interested in the first request that causes the following "require" statement to fail:

     require((address(this)).balance >= withdraw_amount,"Balance too small for this withdrawal");

     Force this "require" to fail and show the logs where this error is mentioned. The logs are found on the Ganache user interface.

11)  Show a screenshot showing the balance and storage associated with your Faucet contract. The balance and storage associated with a contract  are found on the Ganache user interface.

12)  Show a screenshot showing the transactions and events that are associated
     with your Faucet contract. The transactions and events associated with a contract are found on the Ganache user interface.

Part 3 Submission summary:

     Question 8 Two paragraphs
     Question 9 Transaction receipt
     Question 10 copy and paste of partial log file
     Question 11 screenshot
     Question 12 screenshot

:checkered_flag:**13) Place your submissions in a clearly labeled single Word or PDF document named Lab1Part3.doc or Lab1Part3.pdf.**

## Part 4 Using a Truffle box with MetaCoin.sol, ConvertLib.sol and the testing framework  

1) Create a new project directory named DBUC_Lab1_Part4 and cd into it.

2) Execute the command:

```
   truffle unbox metacoin

```

3) Edit the file truffle-config.js and remove most comment symbols so that
   the file appears as shown:

```
module.exports = {
  // Uncommenting the defaults below
  // provides for an easier quick-start with Ganache.
  // You can also follow this format for other networks;
  // see <http://truffleframework.com/docs/advanced/configuration>
  // for more details on how to specify configuration options!
  //
  networks: {
    development: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*"
    },
    test: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*"
    }
  }
};

```

4) Run a new instance of ganache and configure Ganache as before:

         Select "new workspace".

         Select "add project".

         Choose the file "truffle-config.js" in your "DBUC_Lab1_Part4" project directory.

         Provide the workspace with an appropriate name.

         Save the workspace.

5) Notice that you have three contracts in the contracts subdirectory. One
   of these is the migration contract and the other two are ConvertLib.sol	and
   MetaCoin.sol.

6) Study MetaCoin.sol and ConvertLib.sol and then run the following commands in order.
Note that most of these commands are 'calls' and cost no gas. The transaction sendCoin,
however, costs gas and generates a receipt. Make a copy of this receipt for submission.


```
            truffle migrate --reset
            truffle console
```

```

            metaCoinInstance = await MetaCoin.deployed()

            balance = await metaCoinInstance.getBalance.call(accounts[0])

            balance

            balance.toNumber()
```
```
            metaCoinBalance = (await metaCoinInstance.getBalance.call(accounts[0])).toNumber()

            metaCoinBalance
```

```
            metaCoinEthBalance = (await metaCoinInstance.getBalanceInEth.call(accounts[0])).toNumber()

            metaCoinEthBalance
```

```
            accountOne = accounts[0]

            accountTwo = accounts[1]

            accountOneStartingBalance = (await metaCoinInstance.getBalance.call(accountOne)).toNumber()

            accountOneStartingBalance

            accountTwoStartingBalance = (await metaCoinInstance.getBalance.call(accountTwo)).toNumber()
            accountTwoStartingBalance

            amount = 10

```
Keep a copy of the receipt generated by the sendCoin transaction.

```
            metaCoinInstance.sendCoin(accountTwo, amount, { from: accountOne })
```

```
            accountOneEndingBalance = (await metaCoinInstance.getBalance.call(accountOne)).toNumber()

            accountOneEndingBalance

            accountTwoEndingBalance = (await metaCoinInstance.getBalance.call(accountTwo)).toNumber()
            accountTwoEndingBalance

```    
7) Exit the console (ctrl-D) and examine the directory named test. It contains a file named
metacoin.js that is provided as part of the truffle box. Look over the javascript code and
compare it with the code we entered above in the console.

8) From the project directory, execute the test code by running:

 ```
truffle tests

 ```
9) Take a screenshot of the command line output. That is, show the result of running the 5 tests.

10) Add a new Event to the MetaCoin contract. This event will be fired when
sendCoin is called with an inappropriate transfer of funds. You need to
submit three items:

    a) Submit a copy of your new MetaCoin.sol contract - include your name in the
    program comments.

    b) Show a screenshot of your command line that is testing this event. That
    is, show an example of making a call where the sender has insufficient funds.

    c) Show the transaction receipt that is returned to the caller. This transaction receipt will contain the following line:

    ```
    event: 'Insufficient_Funds'

    ```


    d) Submit a screenshot of the Ganache Events screen showing the details of the Insufficient_Funds Event.  


 11) Note that, from the project directory, you can compile your Solidity code by running:

 ```
  truffle compile

 ```

Part 4 Submission summary:

              Question 6 Submit a copy of the receipt generated by sendCoin.

              Question 9. Submit a screenshot of the command line output.

              Question 10 a. Submit a modified MetaCoin contract (including your name).

              Question 10 b. Submit a command line screenshot.

              Question 10 c. Submit a copy of the receipt showing insufficient funds.

              Question 10 d. Submit a screenshot of the Ganache Events screen showing the details of the Insufficient_Funds Event.

:checkered_flag:**11) Place your submissions in a clearly labeled single Word or PDF document named Lab1Part4.doc or Lab1Part4.pdf.**



## Part 5 Creating an NFT


1) Using the directions found here, install the Interplanetary File System (IPFS)

https://docs.ipfs.io/install/command-line/#system-requirements

2) Using these directions, initialize the IPFS repository.

https://docs.ipfs.io/how-to/command-line-quick-start/

3) These instructions are modified from the video found here:
https://www.youtube.com/watch?v=IFpU4TNwXec

  a) In an empty directory named nft, run
```
     truffle init
```
  b) In the same directory run
```
  npm install @openzeppelin/contracts
```

c) Within the contracts directory, create UniqueAsset.sol.
```
// UniqueAsset.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
contract UniqueAsset is ERC721URIStorage {
  using Counters for Counters.Counter;
  Counters.Counter private _tokenIds;
  constructor() ERC721("UniqueAsset","UNA") {}
  function awardItem(address recipient, string memory metadata)
  public returns (uint256) {
    _tokenIds.increment();
    uint256 newItemId = _tokenIds.current();
    _mint(recipient,newItemId);
    _setTokenURI(newItemId,metadata);
    return newItemId;
  }
}
```
d) Within the migrations directory, create 2_deploy_contracts.js.

```
const UniqueAsset = artifacts.require("UniqueAsset");
module.exports = function(deployer) {
  deployer.deploy(UniqueAsset);
}

```
e) In a shell in the nft directory, run

```
    npm install fs
```
f) In a shell in the nft directory, run
```
    npm install @truffle/hdwallet-provider@1.2.3
```
g) Modify truffle-config.js so it has compiler version 0.8.1 and set docker to false.
```
    // Configure your compilers
    compilers: {
      solc: {
        version: "0.8.1",    // Fetch exact version from solc-bin (default: truffle's version)
        docker: false,        // Use "0.5.1" you've installed locally with docker (default: false)
        settings: {          // See the solidity docs for advice about optimization and evmVersion
        optimizer: {
        enabled: false,
        runs: 200
        },
        evmVersion: "byzantium"
        }
      }
    },
```
   h) From the nft directory, run
```
      mkdir credential
```
i) Place a simple file in the credential directory
```
      echo "This is an important credential" > credential.txt
```
j) Add the credential file to ipfs and make a copy of the content identifier (CID). The CID begins with "Qm".
```
      ipfs add credential.txt
```
k) Add this metadata file to the credential directory. Name it credentialMetaData.json. Include the CID associated with credential.txt.
```
   {
     "name" : "My cool credential",
     "description" : "This is a credential that I worked very hard to attain.",
     "file" : "https//ipfs.io/ipfs/THE_CREDENTIAL_CID_GOES_HERE"
   }
```
l) Add the metadata file to ipfs:
```
      ipfs add credentialMetaData.json
```
m) Examine your metadata file using ipfs:
```
      ipfs cat /ipfs/THE_METADATA_CID_GOES_HERE
```
n) From the nft directory, compile the nft contract:
```
   truffle compile
```

   o) Run Ganache Workspace and point to nft/truffle-config.js.

   p) From the nft directory, deploy the NFT contract to Ganache:
```
   truffle migrate
```
   q) Run the Truffle console:
```
   truffle console
```
   r) Access the contract:
```
   let contract = await UniqueAsset.deployed();
```
   s) Visit Ganache and make a copy of the first account address (include the "0x"). This becomes the first argument to the awardItem call. Use the CID of the metadata file as the second argument. Run the following command in the truffle console:
```
   let result = await contract.awardItem("ACCOUNT_ADDR_GOES_HERE","https//ipfs.io/ipfs/THE_META_DATA_CID_GOES_HERE")
```
   t) Examine the name of the contract:
```
   let nameOfToken = await contract.name()
   nameOfToken
```

   u) Examine the balance of the first account:
```
   let balance = await contract.balanceOf("ACCOUNT_ADDR_GOES_HERE")
   balance.toNumber()
```
   v) Examine the balance of the second account. Fill in the blank.

   w) Who is the owner of the Token ID 1?
```
    let owner = await contract.ownerOf("1")
    owner
```
   x) Transfer the token to the second account.
```
   account_from = "0xTHE_ADDRESS_OF_THE_FIRST_ACCOUNT"
   account_to = "0xTHE_ADDRESS_OF_THE_SECOND_ACCOUNT"
   let transfer = await contract.transferFrom(account_from, account_to, 1)
   transfer
```
   y) Who is the new owner? Show the command that you use to learn who the new owner is. Fill in the blank.

   :checkered_flag:**11)Place a copy of the transaction receipt from step 3 x) and your answer to question 3 y) in a clearly labeled single Word or PDF document named Lab1Part5.doc or Lab1Part5.pdf.**

   :checkered_flag:**Place your four submission documents and modified Metacoin contract in a single directory and zip that directory. Name the zip file <your-andrew-id>Lab1.zip. Submit this single zip file to Canvas.**   

## Grading rubric for the materials in the submission directory

    2 points for completion of Part 2
    0.25 points for correct submission of Part 2
    2 points for completion of Part 3
    0.25 points for correct submission of Part 3
    2 points for completion of Part 4
    0.25 points for correct submission of Part 4
    2 points for completion of Part 5
    0.25 points for correct submission of Part 5

## Penalty for any late  work

    -1 point per day late
