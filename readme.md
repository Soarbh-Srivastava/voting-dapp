
# **VoteSphere A Voting Dapp**

The application is extremely simple, all it does is initialize a set of contestants, let anyone vote for the candidates and display the total votes received by each candidate. The goal is not to just code an application but to learn the process of compiling, deploying and interacting with it.

I have deliberately avoided using any dapp frameworks to build this application because the frameworks abstract away lot of the details and you fail to understand the internals of the system. Also, when you do use a framework, you will have more appreciation for all the heavy lifting the framework does for you!

**The goal of this exercise is to:**

1. Set up the development environment.

2. Learn the process of writing a contract, compiling it and deploying it in your development environment.

3. Interact with the contract on the blockchain through a Node.js console.

4. Interact with the contract through a simple web page to display the vote counts and vote for candidates through the page.

_The entire application set up and build was done on Windows 11. I have set up and tested the application on macOS as well._
***
## Step 1 :

**Setting up the development environment**

- NPM version check `npm --v`
- Node version check `node -v`
- Creating file `mkdir ethereum_voting_dapp\chapter1`
- Change directory`cd ethereum_voting_dapp/chapter1`
- Installing ganache `npm install ganache-cli web3@1.2.6`
- Starting ganache `ganache-cli`

## Ganache CLI Output
```
Ganache CLI v6.3.0 (ganache-core: 2.4.0)

Available Accounts
==================
(0) 0x66d1dda957bc7a087324130c89547ce579cee563 (~100 ETH)
(1) 0xf98e7d03f312c8329d01ef0642b74e77740cc203 (~100 ETH)
(2) 0x71d4ee31cd935d17a2b00b7d8873651d6aefbf73 (~100 ETH)
(3) 0x2fdaa99748e6224e03e3e5a1a9a89abd906df671 (~100 ETH)
(4) 0xfb32d320a858747453ac1b4e23c849fecd6ebf33 (~100 ETH)
(5) 0x2c00cd8a488a8ae569553dfe90bf90d65af97630 (~100 ETH)
(6) 0xa4099d0d4a0e7ce5bf26c3f4078a001daf5cedff (~100 ETH)
(7) 0xa53c14d56683bdc775c19540679b0809bbf95a2b (~100 ETH)
(8) 0x3ba642d43a2a24a0160fa22c358cf697aa4a627f (~100 ETH)
(9) 0x81c43af78fdb4a2c8078ec37875e9616e3c5a3e4 (~100 ETH)

Private Keys
==================
(0) 0xee670c7915b5e73b6f5c82da3919701f19ed1f72a1d4f31007836930623f4a57
(1) 0xf40063c41978799ff6fa4b85d678b0f42ed6c774c35fa883a289fdc7fa0b65cb
(2) 0x5228a5e30d1be11885cd7d44d4a47b1dfa22db4033cd758a1c3677a3a0cb5383
(3) 0x3520c609b830538bd8c5d05c8f53338322411deb477308c5543f20a420fea5ec
(4) 0xa7728cbc1eee41d696cb1d252157fc66ab1726ff751f2132c387726b5c901c35
(5) 0xe459627dc49d59da76c393b40c36c674c8307ec52c24794a33ddc773f3c229fc
(6) 0xa65715e3bdc8236420491de88486ff301a19b624b006811a89742e74120eac50
(7) 0xc95ab6498d506f45b4b8ab2b04967d51e05af41059334df91d63097cb3254a3b
(8) 0xf0619031f2ba4319aae691ce34fe8cce136640fb715b70d5baa045c2d58f1b80
(9) 0x39a85154d16a1eba4a9f8514f187bcb6c8b867b790035afa9f92bf2731aebc5b

HD Wallet
==================
Mnemonic:      rather online beyond apology whale cheese game ankle together share potato leisure
Base HD Path:  m/44'/60'/0'/0/{account_index}
```
## Step 2 :

Now copy the Voting.sol smart contract and run following command in command prompt:

- Install solc@0.6.4: `npm install solc@0.6.4`
- Compile the Contract: `solcjs --bin --abi Voting.sol`
- To list the files in the current directory, run the following command:`dir`

_When you compile the code successfully using the command above, the compiler outputs 2 files that are important to understand:_

- **Voting_sol_Voting.bin:** This is the bytecode you get when the source code in Voting.sol is compiled. This is the code which will be deployed to the blockchain.

- **Voting_sol_Voting.abi:** This is an interface or template of the contract (called abi) which tells the contract user what methods are available in the contract. Whenever you have to interact with the contract in the future, you will need this abi definition.


## Connect to Local Ethereum Node

To connect to a local Ethereum node, follow these steps:
1. Open command prompt
2. `cd` to working directory
3. write following cmd.
- `node`
- `> Web3 = require('web3')`
- `> web3 = new Web3("http://127.0.0.1:8545")`

To make sure web3 object is initialized and can communicate with the blockchain, let’s query all the accounts in the blockchain. 

`> web3.eth.getAccounts(console.log)`

You should see a result like below:
```bash 
['0x9c02f5c68e02390a3ab81f63341edc1ba5dbb39e',
'0x7d920be073e92a590dc47e4ccea2f28db3f218cc',
'0xf8a9c7c65c4d1c0c21b06c06ee5da80bd8f074a9',
'0x9d8ee8c3d4f8b1e08803da274bdaff80c2204fc6',
'0x26bb5d139aa7bdb1380af0e1e8f98147ef4c406a',
'0x622e557aad13c36459fac83240f25ae91882127c',
'0xbf8b1630d5640e272f33653e83092ce33d302fd2',
'0xe37a3157cb3081ea7a96ba9f9e942c72cf7ad87b',
'0x175dae81345f36775db285d368f0b1d49f61b2f8',
'0xc26bda5f3370bdd46e7c84bdb909aead4d8f35f3']
```
To compile the contract, load the bytecode and abi from the file system in to a string like below
```bash
> bytecode = fs.readFileSync('Voting_sol_Voting.bin').toString()
> abi = JSON.parse(fs.readFileSync('Voting_sol_Voting.abi').toString())
```
**Note: Replace the ‘from’ value below with one of the addresses generated by your ganache blockchain.**
```bash
> deployedContract = new web3.eth.Contract(abi)
> deployedContract = new web3.eth.Contract(abi)
> listOfCandidates = ['Rama', 'Nick', 'Jose']
> deployedContract.deploy({
  data: bytecode,
  arguments: [listOfCandidates.map(name => web3.utils.asciiToHex(name))]
}).send({
  from: 'ENTER 1 OF 10 ACCOUNT ADDRESSES like 0xfb3....',
  gas: 1500000,
  gasPrice: web3.utils.toWei('0.00003', 'ether')
}).then((newContractInstance) => {
  deployedContract.options.address = newContractInstance.options.address
  console.log(newContractInstance.options.address)
});
```

##  Interact with the contract in the nodejs console
```bash
> deployedContract.methods.totalVotesFor(web3.utils.asciiToHex('Rama')).call(console.log)
> deployedContract.methods.voteForCandidate(web3.utils.asciiToHex('Rama')).send({from: 'YOUR ACCOUNT ADDRESS'}).then((f) => console.log(f))
> deployedContract.methods.totalVotesFor(web3.utils.asciiToHex('Rama')).call(console.log)
```

Try the above commands in your node console and you should see the vote count increment. Every time you vote for a candidate, you get back a transaction id: Example: `0xdedc7ae544c3dde74ab5a0b07422c5a51b5240603d31074f5b75c0ebc786bf53`in ganache.

## Step 3 :

Now Everything is setup you just need to Replace contract address and abi in index.js file and u are ready to go live.


<!-- add my credit info -->

## Developed By:

Soarbh Srivastava.