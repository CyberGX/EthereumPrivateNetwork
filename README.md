# Setup Ethereum Private Network

Ethereum is popular framework for blockchain and cryptocurrency, For this article I assume you have basic information about BlockChain technology and Ethereum.

In this article I will explain how to setup your private Ethereum network.

### Prerequisite
- Operating System Ubuntu 17.2
- Minimum RAM 2 GB

### Setup Ethereum Private Network
**Step 1 :** Install Node Js 8.x

```bash
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ apt-get install nodejs
```
#### Step 2 : Install Ethereum geth client

```bash 
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install ethereum
```

To verify proper installation, execute following command on terminal

```bash 
$ geth version
```

**Step 3 :** create genesis.json file and copy following json block

```json
{
    "nonce": "0x0000000000000042",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "difficulty": "0x400",
    "alloc": {},
    "coinbase": "0x0000000000000000000000000000000000000000",
    "timestamp": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "extraData": "0x",
    "gasLimit": "0xffffffffff",
    "config": {
        "chainId": 4224,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    }
}
```

Following are important parameters of genesis.json

- **nonce :** A 64-bit hash, which proves, combined with the mix-hash, that a sufficient amount of computation has been carried out on this block: the Proof-of-Work (PoW).The nonce is the cryptographically secure mining proof-of-work that proves beyond reasonable doubt that a particular amount of computation has been expended in the determination of this token value. (Yellowpager, 11.5. Mining Proof-of-Work).

- **mixhash :** A 256-bit hash which proves, combined with the nonce, that a sufficient amount of computation has been carried out on this block: the Proof-of-Work (PoW). The combination of nonce and mixhash must satisfy a mathematical condition described in the Yellowpaper, 4.3.4.
- **difficulty :** A scalar value corresponding to the difficulty level applied during the nonce discovering of this block. It defines the mining Target, which can be calculated from the previous block’s difficulty level and the timestamp. The higher the difficulty, the statistically more calculations a Miner must perform to discover a valid block. This value is used to control the Block generation time of a Blockchain, keeping the Block generation frequency within a target range.
- **alloc :** Allows defining a list of pre-filled wallets. That’s an Ethereum specific functionality to handle the “Ether pre-sale” period. Since we can mine local Ether quickly, we don’t use this option.
- **coinbase :** The 160-bit address to which all rewards (in Ether) collected from the successful mining of this block have been transferred. They are a sum of the mining reward itself and the Contract transaction execution refunds. Often named “beneficiary” in the specifications, sometimes “etherbase” in the online documentation. This can be anything in the Genesis Block since the value is set by the setting of the Miner when a new Block is created.
- **timestamp :** A scalar value equal to the reasonable output of Unix time() function at this block inception. This mechanism enforces a homeostasis in terms of the time between blocks. A smaller period between the last two blocks results in an increase in the difficulty level and thus additional computation required to find the next valid block. If the period is too large, the difficulty, and expected time to the next block, is reduced. The timestamp also allows verifying the order of block within the chain (Yellowpaper, 4.3.4. (43)).
- **gasLimit :** A scalar value equal to the current chain-wide limit of Gas expenditure per block. High in our case to avoid being limited by this threshold during tests. Note: this does not indicate that we should not pay attention to the Gas consumption of our Contracts.
- **config :** Configuration to describe the chain itself. Specifically the chain ID, the consensus engines to use, as well as the block numbers of any relevant hard forks. Chainid is identifies the current chain and is used for replay protection. This is a unique value for your private chain.

**Step 4 :** Initialise geth client with genesis.json, Execute following command from directory which has genssis.json. you can access geth command from any directory.

```bash
$ geth init genesis.json
```
Following are default directory for ethereum data storage

- Mac: ~/Library/Ethereum
- Linux: ~/.ethereum
- Windows: %APPDATA%\Ethereum

**Step 5 :** Create password.sec file in any directory you want. open password.sec file in your favourite text editor and type password which you like.

**Step 6 :** create coinbase account using following command. Execute following command from directory in which has password.sec

```bash
$ geth account new —-password password.sec
```

**Step 7 :** Create startGeth.sh file (for linux/mac users) and copy following command. make startGeth.sh file as executable using $chmod a+x startGeth.sh. Make sure you provide password.sec file path

```bash
$ geth --networkid 4224 --mine --nodiscover --rpc --rpcport "8545" --port "30303" --rpccorsdomain "*" --nat "any" --rpcapi eth,web3,personal,net --unlock 0 --password "/<your direcotry>/password.sec"  --rpcaddr 0.0.0.0
```
Parameters for geth command

- networkid value Network identifier (integer, 1=Frontier, 2=Morden (disused), 3=Ropsten, 4=Rinkeby) (default: 1)
- mine Enable mining
- nodiscover Disables the peer discovery mechanism (manual peer addition)
- rpc Enable the HTTP-RPC server
- rpcport value HTTP-RPC server listening port (default: 8545)
- port value Network listening port (default: 30303)
- rpccorsdomain value Comma separated list of domains from which to accept cross origin requests (browser enforced)
- nat value NAT port mapping mechanism (any|none|upnp|pmp|extip:<IP>) (default: “any”)
rpcapi value API’s offered over the HTTP-RPC interface
- unlock value Comma separated list of accounts to unlock. 0 means 1st account or coinbase account which we have created in step 6.
- password value Password file to use for non-interactive password input
- rpcaddr value HTTP-RPC server listening interface (default: “localhost”)
 
**Step 8 :** execute startGeth.sh file

```bash
$ ./startGeth.sh
```
Congratulations.. Your private Ethereum network is up and running now.

Open a new tab in terminal and connect to the private blockchain so that your can create a account and mine some ether before deploying the smart contract

```bash
$ geth attach http://127.0.0.1:8545
```

Create and unlock you account

```bash
> personal.newAccount('password')
> personal.unlockAccount(web3.eth.coinbase, "password", 15000) # "0xfe8b247113e91f76a6563f668c82e5c0d0d78d12"
```

Start mining Ether

```bash
> miner.start() # null
```

Check how many Ether you have mined
```javascript
> web3.fromWei(eth.getBalance(eth.coinbase), "ether") // 670
```

To stop mining
```javascript
> miner.stop() // true
```

## Creating a smart contract and deploying it on private ethereum blockchain

you must install truffle from node package manager :

```bash
$ npm install -g truffle
```

Navigate to the same directory of the project and initialize truffle

```bash
$ truffle init
```

Create a new file called Hello.sol in contracts folder and paste in the following code

```javascript
pragma solidity ^0.4.15;
contract Hello {
    string public message; 
    function Hello() {
        message = "Hello, World"; 
    }
}
```
Create a new file called 2_deploy_contracts.js in migartion folder and paste in the following code
```javascript
var Hello = artifacts.require("./Hello.sol");
module.exports = function(deployer) {
    deployer.deploy(Hello);
};
```
Now to to your truffle.js and replace the current code with the following code
```javascript
module.exports = {
    rpc: {
        host:"localhost",
        port:8545
    },
    networks: {
        development: {
            host: "localhost",
            port: 8545, // port where your blockchain is running 
            network_id: "*",
            from: "f6a9eb4b8dd10ce0cd8fb8d8a54eb03d57d9c578",
            gas: 18000000
        }
    }
};
```
The from hex code which represent your account comes following line on code in Geth Javascript Console
```bash
> personal.listAccounts[0]
```
Save all your changes and run

```bash
$ truffle compile
$ truffle migrate
```
If everything works well, you will see something like this.
Test if you have successfully deployed the smart contract by initializing truffle console in a new terminal tab

```bash
$ truffle console
```

Test if you can access your contract

```bash
> var app
> Hello.deployed().then(function(instance) { app = instance; })
> app.message.call()
```

References:

- https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options
- https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum