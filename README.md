# Lamina1 Chain Configuration and Integration Guide

## Overview
Lamina1 is a custom Avalanche subnet using the Ethereum Virtual Machine (EVM). This guide provides essential information for integrating with the Lamina1 blockchain, including API endpoints, node setup, and other important details for exchange integration and wallet support.

## Chainlist
All 4 Lamina1 Chains are on Chainlist
- [**Mainnet Primary Chain**](https://chainlist.org/chain/10849)
- [**Mainnet Identity Chain**](https://chainlist.org/chain/10850)
- [**Testnet Primary Chain**](https://chainlist.org/chain/764984)
- [**Testnet Identity Chain**](https://chainlist.org/chain/767368)

## Lamina1 Mainnet Information
- **Website:** [www.lamina1.com](https://www.lamina1.com)
- **Digital Coin Name:** L1
- **Symbol:** L1
- **Chain Name:** Lamina1
- **Chain ID:** 10849
- **Public RPC:** [https://subnets.avax.network/lamina1/mainnet/rpc](https://subnets.avax.network/lamina1/mainnet/rpc)
- **Block Explorer:** [explorer.lamina1.com](https://explorer.lamina1.com)
- **GitHub Repository:** [github.com/lamina1](https://github.com/lamina1)

## Lamina1 Testnet (Fuji) Information
- **Website:** [www.fuji.hubdemo.lamina1.com](https://fuji.hubdemo.lamina1.com/)
- **Digital Coin Name:** L1 Test
- **Symbol:** L1T
- **Chain Name:** Lamina1 Testnet
- **Chain ID:** 764984
- **Public RPC:** [https://subnets.avax.network/lamina1tes/testnet/rpc](https://subnets.avax.network/lamina1tes/testnet/rpc)
- **Block Explorer:** [https://subnets-test.avax.network/lamina1tes](https://subnets-test.avax.network/lamina1tes)

## Node Installation and Configuration
To run a Lamina1 node, follow the Avalanche subnet node installation documentation:
- **Node Installation Guide:** [Avalanche Subnet Node Installation](https://docs.avax.network/nodes/run/subnet-node)
- **Disk Size Requirement:** <50 GB is needed initially when only syncing the Lamina1 chain and the Avalanche P-Chain. <128 GB is probably safe for the next year or so. Avalanche recommends 1 TB SSD generally for validators. 
- **Configuration Directory:** [Avalanche Node Configuration](https://docs.avax.network/nodes/configure/avalanchego-config-flags#data-directory)

### Customizing RPC Port and Data Directory
- **RPC Port Configuration:** [RPC Port Configuration](https://docs.avax.network/nodes/configure/avalanchego-config-flags#--http-port-int)
- **Database Configuration:** [Database Configuration](https://docs.avax.network/nodes/configure/avalanchego-config-flags#database)

### Upgrades
The Lamina1 subnet can undergo upgrades, which require an upgrade file to be provided to `avalanchego`. This is done according to the standard procedure of upgrading a subnet EVM to enable new precompiles for example.

The following link from avalanche docs provides a good overview of the upgrade procedure: https://docs.avax.network/build/subnet/upgrade/upgrade-precompile

Essentially, in order to sync an upgrade, a file named `upgrade.json` has to be created and placed in the chain config directory of `avalanchego`. When using default locations, for the Lamina1 main subnet, the file should be placed at: `~/.avalanchego/configs/chains/UhReZTXT8Cqsjat9ghRtCe5kBQPQexQB5zG5Fvf3egrdYfyoJ/upgrade.json`.

Currently, there has been one upgrade to the Lamina1 subnet, executed on 24 July 2024.

The contents of the `upgrade.json` file are as follows:
```json
{
    "precompileUpgrades": [
        {
            "rewardManagerConfig": {
                "adminAddresses": ["0x444b4a012a240bc0b898e91c70152caee8be26fb"],
                "blockTimestamp": 1721779200
            }
        }
    ]
}
```

The contents of this file will be updated in this repo whenever new upgrades are executed.

## API Information
Lamina1 supports standard EVM JSON-RPC methods and you can use the following public API endpoints:
- **Testnet (Fuji):** [https://subnets.avax.network/lamina1tes/testnet/rpc](https://subnets.avax.network/lamina1tes/testnet/rpc)
- **Mainnet:** [https://subnets.avax.network/lamina1/mainnet/rpc](https://subnets.avax.network/lamina1/mainnet/rpc)

Alternatively you can use libraries like `ethers` or `web3.js` that interact with EVM chains. 

See the RPC API Instructions.md document for more detailed information.

### Common API Methods
#### Get Account Balance
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": ["0xYourAccountAddress", "latest"],
    "id": 1
}
```
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xYourAccountAddress", "latest"],"id":1}' -H "Content-Type: application/json" http://localhost:8545
```

#### Get Latest Block Number
```json
{
    "jsonrpc": "2.0",
    "method": "eth_blockNumber",
    "params": [],
    "id": 1
}
```
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' -H "Content-Type: application/json" http://localhost:8545
```

#### Get Transaction by Hash
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionByHash",
    "params": ["0xYourTransactionHash"],
    "id": 1
}
```
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xYourTransactionHash"],"id":1}' -H "Content-Type: application/json" http://localhost:8545
```

#### Verify Transaction Receipt
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionReceipt",
    "params": ["0xYourTransactionHash"],
    "id": 1
}
```
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionReceipt","params":["0xYourTransactionHash"],"id":1}' -H "Content-Type: application/json" http://localhost:8545
```

**Note:** `status: "0x1"` means the transaction was successful, while `status: "0x0"` means it failed.

#### Transfer Process
To transfer L1 tokens, you must sign the transaction offline and broadcast it online.

**Sign Transaction Offline (using web3.js):**
```javascript
const Web3 = require('web3');
const web3 = new Web3();

const account = web3.eth.accounts.privateKeyToAccount('0xYourPrivateKey');

const tx = {
    to: '0xRecipientAddress',
    value: web3.utils.toWei('0.1', 'ether'),
    gas: 2000000
};

const signedTx = account.signTransaction(tx);
```

**Broadcast Signed Transaction:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": ["0xSignedTransactionData"],
    "id": 1
}
```
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":["0xSignedTransactionData"],"id":1}' -H "Content-Type: application/json" http://localhost:8545
```

## Network Details
- **Block Producing Rate:** Average block time ~2 seconds (blocks are only produced when there are transactions to process)
- **Transaction Precision:** 18 decimal places
- **Address Format:** Addresses begin with 0x followed by 40 alphanumeric characters

## Wallet Information
- **Current Wallet:** [Lamina1 Wallet (Early Access)](https://eap.hubdemo.lamina1.com)
- **Official Wallet (from May 28, 2024):** [Lamina1 Wallet](https://www.lamina1.com)

## Additional Information
- **Account Memo Function:** EVM-based, similar to standard Ethereum accounts
- **Creating Account:** Follow standard EVM account creation processes
- **Preventive Measures to Avoid Chain Forking:** Not needed due to PoS mechanism
- **Restoring and Recovering Accounts:** Use standard Ethereum account recovery methods (e.g., mnemonic phrases, private keys)
- **Cross-Chain Bridge:** Not currently implemented; earliest possible date is June 15, 2024, subject to Open Metaverse Foundation (OMF) decision
- **Proof of Stake:** Prevents 51% attacks inherently
- **Rollback Policy:** No rollback
- **Transaction Timeout Mechanism:** No timeout mechanism for transactions in the tx pool
- **Rent or Reservation:** No rent or reservation requirements
- **Node IP Whitelisting:** Not required

For further technical details, you can refer to the following resources:
- **Standard EVM JSON-RPC Documentation:** [Ethereum JSON-RPC](https://geth.ethereum.org/docs/interacting-with-geth/rpc)
- **Avalanche C-Chain API Documentation:** [Avalanche C-Chain API](https://docs.avax.network/reference/avalanchego/c-chain/api)
- **Avalanche Subnet EVM API Documentation:** [Avalanche Subnet EVM API](https://docs.avax.network/reference/subnet-evm/api)
