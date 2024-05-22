# EVM JSON-RPC API Instructions

## Regular Explanation of Accounts or Validating Accounts API

**Get Account Information:**

To get basic account information, such as the balance, you can use the `eth_getBalance` method. This will return the balance of the account in wei (the smallest unit of Ether).

**Example Request:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": ["0xYourAccountAddress", "latest"],
    "id": 1
}
```

**Example `curl` Command:**
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xDFfbcc6D4f053110Cf8efDe232A57396446cAdb7", "latest"],"id":1}' -H "Content-Type: application/json" https://subnets.avax.network/lamina1/mainnet/rpc
```

**Example Response:**
```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x0234c8a3397aab58" // 158972490234375000 wei
}
```

## Access the Best Height API

To get the latest block number (best height), you use the `eth_blockNumber` method.

**Example Request:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_blockNumber",
    "params": [],
    "id": 1
}
```

**Example `curl` Command:**
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' -H "Content-Type: application/json" https://subnets.avax.network/lamina1/mainnet/rpc
```

**Example Response:**
```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x4b7" // 1207 in decimal
}
```

## Access the Account History API

To get the transaction history of an account, you typically need to rely on an external service, as the Ethereum JSON-RPC API does not provide an endpoint to fetch the transaction history directly.

Services like Etherscan provide an API to get the transaction history of an account. If you're running your own node, you might need to use event logs or trace transaction calls to get this data.

## Access the Detailed Information of a Trade

To get detailed information about a specific transaction, you use the `eth_getTransactionByHash` method.

**Example Request:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionByHash",
    "params": ["0xYourTransactionHash"],
    "id": 1
}
```

**Example `curl` Command:**
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0x93bb57b0d126f053bae3a7621210adde9aa1ee3f29b6adb382577480bf0e7ff9"],"id":1}' -H "Content-Type: application/json" https://subnets.avax.network/lamina1/mainnet/rpc
```

**Example Response:**
```json
{
    "jsonrpc":"2.0",
    "id":1,
    "result":{
        "blockHash":"0x2e0f02b271594ae21ce02411d154bb88b9dbacc5c7a5570b2816838d5457af85",
        "blockNumber":"0x108",
        "from":"0xdffbcc6d4f053110cf8efde232a57396446cadb7",
        "gas":"0x5208",
        "gasPrice":"0x62b85e900",
        "maxFeePerGas":"0x7558bdb00",
        "maxPriorityFeePerGas":"0x59682f00",
        "hash":"0x93bb57b0d126f053bae3a7621210adde9aa1ee3f29b6adb382577480bf0e7ff9",
        "input":"0x",
        "nonce":"0x3",
        "to":"0xdf2b352dd57f42d358345fb793ef43e95b7ad3de",
        "transactionIndex":"0x0",
        "value":"0x8ac7230489e80000",
        "type":"0x2",
        "accessList":[],
        "chainId":"0x2a61",
        "v":"0x1",
        "r":"0xbab27474749a44d67c8b5382dd3e3f7dd46f25828e66168849cf5df0fa1556bd",
        "s":"0x5c953b0bbd24facde92a15a76ce4ff7eb134c6cbfa3e07d6ee1a454130a339f5",
        "yParity":"0x1"
    }
}
```

## Determine a Trade is Accurate to Avoid Cheating Recharge

To ensure a transaction is accurate and avoid cheating, you should verify:
- The transaction's `from` and `to` addresses are correct.
- The `value` matches the expected amount.
- The transaction receipt indicates a successful execution (see "status": "0x1"").

**Verify Transaction Receipt:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionReceipt",
    "params": ["0xYourTransactionHash"],
    "id": 1
}
```

**Example `curl` Command:**
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionReceipt","params":["0x93bb57b0d126f053bae3a7621210adde9aa1ee3f29b6adb382577480bf0e7ff9"],"id":1}' -H "Content-Type: application/json" https://subnets.avax.network/lamina1/mainnet/rpc
```

**Example Response:**
```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "transactionHash": "0x...",
        "transactionIndex": "0x1",
        "blockHash": "0x...",
        "blockNumber": "0xb",
        "from": "0x...",
        "to": "0x...",
        "cumulativeGasUsed": "0x33bc",
        "gasUsed": "0x4dc",
        "contractAddress": null,
        "logs": [],
        "status": "0x1"
    }
}
```

## Transfer Process: Sign Transaction Offline and Broadcast It Online

**Sign Transaction Offline:**

First, construct the transaction object and sign it using your private key. This can be done using libraries like `ethers` or `web3.js`.

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

Once you have the signed transaction, broadcast it using `eth_sendRawTransaction`.

**Example Request:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": ["0xSignedTransactionData"],
    "id": 1
}
```

**Example `curl` Command:**
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":["0xSignedTransactionData"],"id":1}' -H "Content-Type: application/json" https://subnets.avax.network/lamina1/mainnet/rpc
```

**Example Response:**
```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xTransactionHash"
}
```

## Access the Balance of an Account

As in the first example, use the `eth_getBalance` method to access the balance of an account.

**Example Request:**
```json
{
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": ["0xYourAccountAddress", "latest"],
    "id": 1
}
```

**Example `curl` Command:**
```sh
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xDFfbcc6D4f053110Cf8efDe232A57396446cAdb7", "latest"],"id":1}' -H "Content-Type: application/json" https://subnets.avax.network/lamina1/mainnet/rpc
```

**Example Response:**
```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x0234c8a3397aab58" // 158972490234375000 wei
}
```