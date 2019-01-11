# VNT Chain JSON RPC API

## Dapp APIs

`Dapp APIs`本质上是由`JSON RPC`提供的，所以又称做`JSON RPC API`。

`JSON`是一种轻量的数据交换格式。`JSON-RPC`是一种无状态，轻量的`RPC`协议。本规范主要定义了几种数据结构，以及围绕它的处理规则。该协议与传输方式无关，因为这些原理可以在同一进程，通过`socket`，通过`HTTP`，或者通过其它多种消息传输环境中都一样使用。它使用JSON(`RFC-4627`)作为数据格式。

### JavaScript API

在一个JavaScript程序内部和vntchain节点通信，需要使用`vnt.js`库，它提供了使用RPC方法的简单接口。

### JSON-RPC路径

默认的`JSON-RPC`监听路径为：

|Client	| URL |
| --- | --- |
| Go		|http://localhost:8545|

你可以使用`--rpc`标记来启用`HTTP JSON-RPC`功能：

```bash
$ gvnt --rpc
```

要想修改默认的地址(`localhost`)和端口(`8545`)，可以使用如下命令：

```bash
$ gvnt --rpc --rpcaddr <ip> --rpcport <portnumber>
```

如果通过一个浏览器访问RPC，跨域访问(`CORS`)需要启用并配上合适的域名。否则，JavaScript调用将会受限于同源策略导致请求失败：

```bash
$ gvnt --rpc --rpccorsdomain "http://localhost:3000"
```

`JSON RPC`也可以在`gvnt console`中，使用`admin.startRPC(addr, port)`命令来启用。

### JSON-RPC支持特性

|	| go-vnt |
| --- | --- |
| JSON-RPC 1.0		||
| JSON-RPC 2.0		|✓|
| Batch requests		|✓|
| HTTP		|✓|
| IPC		|✓|
| WS		|✓|

### 十六进制值编码

目前有两种关键数据类型通过JSON传输：无格式的字节数组，和数字。它们都通过十六进制编码进行传递，但两者有不同的格式化要求：

当编码数量类型时（整数，数字）：编码为十六进制，前缀加`0x`，最紧凑的表示（有略微例外：`0`需被表达为`0x0`）。例如：

* `0x41`：十进制为`65`
* `0x400`：十进制为`1024`
* 错误编码：`0x`，总是至少要有一个数字，所以0应该是`0x0`
* 错误编码：`0x0400`，不允许最前面以0开头，要最紧凑的表示
* 错误编码：`ff`，必须以`0x`为前缀

当编码无格式数据时（字节数组，账户地址，哈希值，字节码数组）：编码为十六进制，前缀加`0x`，一个字节用2个十六进制数表示。例如：

* `0x41`：1个字节，即为`A`
* `0x004200`：3个字节，即为`\0B\0`
* `0x`：0个字节，即为`""`
* 错误编码：`0xf0f0f`，必须要有偶数个数字
* 错误编码：`004200`，必须要以前缀`0x`开头

### 默认区块参数

以下这些方法有一个额外的默认区块参数：

* `core_getBalance`
* `core_getCode`
* `core_getTransactionCount`
* `core_getStorageAt`
* `core_call`

当在vntchain状态上请求操作时，最后一个默认区块参数决定了要操作的区块高度。

如下选项可供默认区块参数选择使用：

* 十六进制字符串：一个整数表示的区块编号
* 字符串`earliest`：对应 最早的 / 创世 区块
* 字符串`latest`：对应最新挖出的区块
* 字符串`pending`：对应处于pending的 状态 / 交易（`pendingTxList`）

### Curl样例说明

下面的curl选项也许会返回节点拒绝内容类型的响应，这是因为`--data`选项设置了内容类型为`application/x-www-form-urlencoded`。如果你的节点出现了这种响应，则需要在调用一开始，手动将请求头替换为`-H "Content-Type:application/json"`。

样例并没有包含 `URL/IP + 端口` 的组合，实际情况中，它们必须是curl的最后一个参数，例如：`127.0.0.1:8545`

### JSON-RPC方法

* [vnt_clientVersion](#vnt_clientversion)
* [vnt_sha3](#vnt_sha3)
* [net_version](#net_version)
* [net_peerCount](#net_peercount)
* [net_listening](#net_listening)
* [core_protocolVersion](#core_protocolversion)
* [core_syncing](#core_syncing)
* [core_coinbase](#core_coinbase)
* [core_mining](#core_mining)
* [core_gasPrice](#core_gasprice)
* [core_accounts](#core_accounts)
* [core_blockNumber](#core_blocknumber)
* [core_getBalance](#core_getbalance)
* [core_getStorageAt](#core_getstorageat)
* [core_getTransactionCount](#core_gettransactioncount)
* [core_getcore_BlockTransactionCountByHash](#core_getblocktransactioncountbyhash)
* [core_getBlockTransactionCountByNumber](#core_getblocktransactioncountbynumber)
* [core_getCode](#core_getcode)
* [core_sign](#core_sign)
* [core_sendTransaction](#core_sendtransaction)
* [core_sendRawTransaction](#core_sendrawtransaction)
* [core_call](#core_call)
* [core_estimateGas](#core_estimategas)
* [core_getBlockByHash](#core_getblockbyhash)
* [core_getBlockByNumber](#core_getblockbynumber)
* [core_getTransactionByHash](#core_gettransactionbyhash)
* [core_getTransactionByBlockHashAndIndex](#core_gettransactionbyblockhashandindex)
* [core_getTransactionByBlockNumberAndIndex](#core_gettransactionbyblocknumberandindex)
* [core_getTransactionReceipt](#core_gettransactionreceipt)
* [core_newFilter](#core_newfilter)
* [core_newBlockFilter](#core_newblockfilter)
* [core_newPendingTransactionFilter](#core_newpendingtransactionfilter)
* [core_uninstallFilter](#core_uninstallfilter)
* [core_getFilterChanges](#core_getfilterchanges)
* [core_getFilterLogs](#core_getfilterlogs)
* [core_getLogs](#core_getlogs)
* [shh_post](#shh_post)
* [shh_version](#shh_version)
* [core_getAllCandidates](#shh_getmessages)
* [core_getVoter](#core_getVoter)
* [core_getRestVNTBounty](#core_getRestVNTBounty)

### JSON RPC API手册

***

#### vnt_clientVersion

返回当前客户端版本

##### 参数

无

##### 返回

`string` - 当前客户端版本。

##### 举例

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"vnt_clientVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "Mist/v0.9.3/darwin/go1.4.1"
}
```

***

#### vnt_sha3

Returns Keccak-256 (*not* the standardized SHA3-256) of the given data.

##### Parameters

1. `DATA` - the data to convert into a SHA3 hash.

```js
params: [
  "0x68656c6c6f20776f726c64"
]
```

##### Returns

`DATA` - The SHA3 result of the given string.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"vnt_sha3","params":["0x68656c6c6f20776f726c64"],"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

***

#### net_version

Returns the current network id.

##### Parameters
none

##### Returns

`String` - The current network id.
- `"1"`: Ethereum Mainnet
- `"2"`: Morden Testnet  (deprecated)
- `"3"`: Ropsten Testnet
- `"4"`: Rinkeby Testnet
- `"42"`: Kovan Testnet

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "3"
}
```

***

#### net_listening

Returns `true` if client is actively listening for network connections.

##### Parameters
none

##### Returns

`Boolean` - `true` when listening, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result":true
}
```

***

#### net_peerCount

Returns number of peers currently connected to the client.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the number of connected peers.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": "0x2" // 2
}
```

***

#### core_protocolVersion

Returns the current vntchain protocol version.

##### Parameters
none

##### Returns

`String` - The current vntchain protocol version.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_protocolVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "54"
}
```

***

#### core_syncing

Returns an object with data about the sync status or `false`.


##### Parameters
none

##### Returns

`Object|Boolean`, An object with sync status data or `FALSE`, when not syncing:
  - `startingBlock`: `QUANTITY` - The block at which the import started (will only be reset, after the sync reached his head)
  - `currentBlock`: `QUANTITY` - The current block, same as core_blockNumber
  - `highestBlock`: `QUANTITY` - The estimated highest block

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_syncing","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": {
    startingBlock: '0x384',
    currentBlock: '0x386',
    highestBlock: '0x454'
  }
}
// Or when not syncing
{
  "id":1,
  "jsonrpc": "2.0",
  "result": false
}
```

***

#### core_coinbase

Returns the client coinbase address.


##### Parameters
none

##### Returns

`DATA`, 20 bytes - the current coinbase address.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_coinbase","params":[],"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0xc94770007dda54cF92009BFF0dE90c06F603a09f"
}
```

***

#### core_mining

Returns `true` if client is actively mining new blocks.

##### Parameters
none

##### Returns

`Boolean` - returns `true` of the client is mining, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_mining","params":[],"id":71}'

// Result
{
  "id":71,
  "jsonrpc": "2.0",
  "result": true
}

```

***

#### core_gasPrice

Returns the current price per gas in wei.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the current gas price in wei.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_gasPrice","params":[],"id":73}'

// Result
{
  "id":73,
  "jsonrpc": "2.0",
  "result": "0x09184e72a000" // 10000000000000
}
```

***

#### core_accounts

Returns a list of addresses owned by client.


##### Parameters
none

##### Returns

`Array of DATA`, 20 Bytes - addresses owned by the client.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_accounts","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": ["0xc94770007dda54cF92009BFF0dE90c06F603a09f"]
}
```

***

#### core_blockNumber

Returns the number of most recent block.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the current block number the client is on.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_blockNumber","params":[],"id":1}'

// Result
{
  "id":83,
  "jsonrpc": "2.0",
  "result": "0xc94" // 1207
}
```

***

#### core_getBalance

Returns the balance of the account of given address.

##### Parameters

1. `DATA`, 20 Bytes - address to check for balance.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: [
   '0xc94770007dda54cF92009BFF0dE90c06F603a09f',
   'latest'
]
```

##### Returns

`QUANTITY` - integer of the current balance in wei.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getBalance","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f", "latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58" // 158972490234375000
}
```

***

#### core_getStorageAt

Returns the value from a storage position at a given address.

##### Parameters

1. `DATA`, 20 Bytes - address of the storage.
2. `QUANTITY` - integer of the position in the storage.
3. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`DATA` - the value at this storage position.

##### Example
Calculating the correct position depends on the storage to retrieve. Consider the following contract deployed at `0x295a70b2de5e3953354a6a8344e616ed314d7251` by address `0x391694e7e0b0cce554cb130d723a9d27458f9298`.

```
contract Storage {
    uint pos0;
    mapping(address => uint) pos1;

    function Storage() {
        pos0 = 1234;
        pos1[msg.sender] = 5678;
    }
}
```

Retrieving the value of pos0 is straight forward:

```js
curl -X POST --data '{"jsonrpc":"2.0", "method": "core_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x0", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x00000000000000000000000000000000000000000000000000000000000004d2"}
```

Retrieving an element of the map is harder. The position of an element in the map is calculated with:
```js
keccack(LeftPad32(key, 0), LeftPad32(map position, 0))
```

This means to retrieve the storage on pos1["0x391694e7e0b0cce554cb130d723a9d27458f9298"] we need to calculate the position with:
```js
keccak(decodeHex("000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"))
```
The gvnt console which comes with the vnt library can be used to make the calculation:
```js
> var key = "000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"
undefined
> vnt.sha3(key, {"encoding": "hex"})
"0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9"
```
Now to fetch the storage:
```js
curl -X POST --data '{"jsonrpc":"2.0", "method": "core_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x000000000000000000000000000000000000000000000000000000000000162e"}

```

***

#### core_getTransactionCount

Returns the number of transactions *sent* from an address.


##### Parameters

1. `DATA`, 20 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: [
   '0xc94770007dda54cF92009BFF0dE90c06F603a09f',
   'latest' // state at the latest block
]
```

##### Returns

`QUANTITY` - integer of the number of transactions send from this address.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getTransactionCount","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f","latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### core_getBlockTransactionCountByHash

Returns the number of transactions in a block from a block matching the given block hash.


##### Parameters

1. `DATA`, 32 Bytes - hash of a block.

```js
params: [
   '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238'
]
```

##### Returns

`QUANTITY` - integer of the number of transactions in this block.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getBlockTransactionCountByHash","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xc" // 11
}
```

***

#### core_getBlockTransactionCountByNumber
> >
Returns the number of transactions in a block matching the given block number.


##### Parameters

1. `QUANTITY|TAG` - integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).

```js
params: [
   '0xe8', // 232
]
```

##### Returns

`QUANTITY` - integer of the number of transactions in this block.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getBlockTransactionCountByNumber","params":["0xe8"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

***

#### core_getCode

Returns code at a given address.


##### Parameters

1. `DATA`, 20 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter).

```js
params: [
   '0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b',
   '0x2'  // 2
]
```

##### Returns

`DATA` - the code from the given address.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getCode","params":["0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b", "0x2"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```

***

#### core_sign

The sign method calculates an Ethereum specific signature with: `sign(keccak256("\x19Ethereum Signed Message:\n" + len(message) + message)))`.

By adding a prefix to the message makes the calculated signature recognisable as an Ethereum specific signature. This prevents misuse where a malicious DApp can sign arbitrary data (e.g. transaction) and use the signature to impersonate the victim.

**Note** the address to sign with must be unlocked.

##### Parameters
account, message

1. `DATA`, 20 Bytes - address.
2. `DATA`, N Bytes - message to sign.

##### Returns

`DATA`: Signature

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_sign","params":["0x9b2055d370f73ec7d8a03e965129118dc8f5bf83", "0xdeadbeaf"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa3f20717a250c2b0b729b7e5becbff67fdaef7e0699da4de7ca5895b02a170a12d887fd3b17bfdce3481f10bea41f45ba9f709d39ce8325427b57afcfc994cee1b"
}
```

An example how to use solidity ecrecover to verify the signature calculated with `core_sign` can be found [here](https://gist.github.com/bas-vk/d46d83da2b2b4721efb0907aecdb7ebd). The contract is deployed on the testnet Ropsten and Rinkeby.

***

#### core_sendTransaction

Creates new message call transaction or a contract creation, if the data field contains code.

##### Parameters

1. `Object` - The transaction object
  - `from`: `DATA`, 20 Bytes - The address the transaction is send from.
  - `to`: `DATA`, 20 Bytes - (optional when creating new contract) The address the transaction is directed to.
  - `gas`: `QUANTITY`  - (optional, default: 90000) Integer of the gas provided for the transaction execution. It will return unused gas.
  - `gasPrice`: `QUANTITY`  - (optional, default: To-Be-Determined) Integer of the gasPrice used for each paid gas
  - `value`: `QUANTITY`  - (optional) Integer of the value sent with this transaction
  - `data`: `DATA`  - The compiled code of a contract OR the hash of the invoked method signature and encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI)
  - `nonce`: `QUANTITY`  - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.

```js
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
  "gas": "0x76c0", // 30400
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [core_getTransactionReceipt](#core_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_sendTransaction","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

#### core_sendRawTransaction

Creates new message call transaction or a contract creation for signed transactions.

##### Parameters

1. `DATA`, The signed transaction data.

```js
params: ["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"]
```

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [core_getTransactionReceipt](#core_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_sendRawTransaction","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

#### core_call

Executes a new message call immediately without creating a transaction on the block chain.


##### Parameters

1. `Object` - The transaction call object
  - `from`: `DATA`, 20 Bytes - (optional) The address the transaction is sent from.
  - `to`: `DATA`, 20 Bytes  - The address the transaction is directed to.
  - `gas`: `QUANTITY`  - (optional) Integer of the gas provided for the transaction execution. core_call consumes zero gas, but this parameter may be needed by some executions.
  - `gasPrice`: `QUANTITY`  - (optional) Integer of the gasPrice used for each paid gas
  - `value`: `QUANTITY`  - (optional) Integer of the value sent with this transaction
  - `data`: `DATA`  - (optional) Hash of the method signature and encoded parameters. For details see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI)
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`DATA` - the return value of executed contract.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_call","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x"
}
```

***

#### core_estimateGas

Generates and returns an estimate of how much gas is necessary to allow the transaction to complete. The transaction will not be added to the blockchain. Note that the estimate may be significantly more than the amount of gas actually used by the transaction, for a variety of reasons including EVM mechanics and node performance.

##### Parameters

See [core_call](#core_call) parameters, expect that all properties are optional. If no gas limit is specified geth uses the block gas limit from the pending block as an upper bound. As a result the returned estimate might not be enough to executed the call/transaction when the amount of gas is higher than the pending block gas limit.

##### Returns

`QUANTITY` - the amount of gas used.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_estimateGas","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x5208" // 21000
}
```

***

#### core_getBlockByHash

Returns information about a block by hash.


##### Parameters

1. `DATA`, 32 Bytes - Hash of a block.
2. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   true
]
```

##### Returns

`Object` - A block object, or `null` when no block was found:

  - `number`: `QUANTITY` - the block number. `null` when its pending block.
  - `hash`: `DATA`, 32 Bytes - hash of the block. `null` when its pending block.
  - `parentHash`: `DATA`, 32 Bytes - hash of the parent block.
  - `logsBloom`: `DATA`, 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block.
  - `transactionsRoot`: `DATA`, 32 Bytes - the root of the transaction trie of the block.
  - `stateRoot`: `DATA`, 32 Bytes - the root of the final state trie of the block.
  - `receiptsRoot`: `DATA`, 32 Bytes - the root of the receipts trie of the block.
  - `difficulty`: `QUANTITY` - integer of the difficulty for this block.
  - `totalDifficulty`: `QUANTITY` - integer of the total difficulty of the chain until this block.
  - `extraData`: `DATA` - the "extra data" field of this block.
  - `size`: `QUANTITY` - integer the size of this block in bytes.
  - `gasLimit`: `QUANTITY` - the maximum gas allowed in this block.
  - `gasUsed`: `QUANTITY` - the total used gas by all transactions in this block.
  - `timestamp`: `QUANTITY` - the unix timestamp for when the block was collated.
  - `transactions`: `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getBlockByHash","params":["0xf1972fbcb3a246cbb34f543efa53fbec7f6bc541045b821539ed3e1038eb757c", true],"id":1}'

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "CmtMsges": [],
        "difficulty": "0x1",
        "extraData": "0x",
        "gasLimit": "0x47b760",
        "gasUsed": "0x0",
        "hash": "0x82db2ba52b853964f95c008893d64fc2b722302ebc0be609dee05aafe795ae18",
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "number": "0x10",
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "producer": "0x0000000000000000000000000000000000000000",
        "receiptsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "signature": "0x",
        "size": "0x208",
        "stateRoot": "0xb7a4d27b55c098f4ae1603abe3900e184efbf285fdb31cbca4ec85d95996533a",
        "timestamp": "0x5b45b949",
        "totalDifficulty": "0x1",
        "transactions": [],
        "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
        "witnesses": [
            "0x122369f04f32269598789998de33e3d56e2c507a",
            "0x42a875ac43f2b4e6d17f54d288071f5952bf8911",
            "0x3dcf0b3787c31b2bdf62d5bc9128a79c2bb18829",
            "0xbf66d398226f200467cd27b14e85b25a8c232384"
        ]
    }
}
```

***

#### core_getBlockByNumber

Returns information about a block by block number.

##### Parameters

1. `QUANTITY|TAG` - integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).
2. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
   '0x2', // 2
   true
]
```

##### Returns

See [core_getBlockByHash](#core_getblockbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getBlockByNumber","params":["0x2", true],"id":1}'
```

Result see [core_getBlockByHash](#core_getblockbyhash)

***

#### core_getTransactionByHash

Returns the information about a transaction requested by transaction hash.


##### Parameters

1. `DATA`, 32 Bytes - hash of a transaction

```js
params: [
   "0x88df016429689c079f3b2f6ad39fa052532c56795b733da78a91ebe6a713944b"
]
```

##### Returns

`Object` - A transaction object, or `null` when no transaction was found:

  - `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
  - `blockNumber`: `QUANTITY` - block number where this transaction was in. `null` when its pending.
  - `from`: `DATA`, 20 Bytes - address of the sender.
  - `gas`: `QUANTITY` - gas provided by the sender.
  - `gasPrice`: `QUANTITY` - gas price provided by the sender in Wei.
  - `hash`: `DATA`, 32 Bytes - hash of the transaction.
  - `input`: `DATA` - the data send along with the transaction.
  - `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
  - `to`: `DATA`, 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
  - `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block. `null` when its pending.
  - `value`: `QUANTITY` - value transferred in Wei.
  - `v`: `QUANTITY` - ECDSA recovery id
  - `r`: `DATA`, 32 Bytes - ECDSA signature r
  - `s`: `DATA`, 32 Bytes - ECDSA signature s

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getTransactionByHash","params":["0x88df016429689c079f3b2f6ad39fa052532c56795b733da78a91ebe6a713944b"],"id":1}'

// Result
{
  "jsonrpc":"2.0",
  "id":1,
  "result":{
    "blockHash":"0x1d59ff54b1eb26b013ce3cb5fc9dab3705b415a67127a003c3e61eb445bb8df2",
    "blockNumber":"0x5daf3b", // 6139707
    "from":"0xa7d9ddbe1f17865597fbd27ec712455208b6b76d",
    "gas":"0xc350", // 50000
    "gasPrice":"0x4a817c800", // 20000000000
    "hash":"0x88df016429689c079f3b2f6ad39fa052532c56795b733da78a91ebe6a713944b",
    "input":"0x68656c6c6f21",
    "nonce":"0x15", // 21
    "to":"0xf02c1c8e6114b1dbe8937a39260b5b0a374432bb",
    "transactionIndex":"0x41", // 65
    "value":"0xf3dbb76162000", // 4290000000000000
    "v":"0x25", // 37
    "r":"0x1b5e176d927f8e9ab405058b2d2457392da3e20f328b16ddabcebc33eaac5fea",
    "s":"0x4ba69724e8f69de52f0125ad8b3c5c2cef33019bac3249e2c0a2192766d1721c"
  }
}
```

***

#### core_getTransactionByBlockHashAndIndex

Returns information about a transaction by block hash and transaction index position.


##### Parameters

1. `DATA`, 32 Bytes - hash of a block.
2. `QUANTITY` - integer of the transaction index position.

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   '0x0' // 0
]
```

##### Returns

See [core_getTransactionByHash](#core_gettransactionbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getTransactionByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}'
```

Result see [core_getTransactionByHash](#core_gettransactionbyhash)

***

#### core_getTransactionByBlockNumberAndIndex

Returns information about a transaction by block number and transaction index position.


##### Parameters

1. `QUANTITY|TAG` - a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).
2. `QUANTITY` - the transaction index position.

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

##### Returns

See [core_getTransactionByHash](#core_gettransactionbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getTransactionByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}'
```

Result see [core_getTransactionByHash](#core_gettransactionbyhash)

***

#### core_getTransactionReceipt

Returns the receipt of a transaction by transaction hash.

**Note** That the receipt is not available for pending transactions.


##### Parameters

1. `DATA`, 32 Bytes - hash of a transaction

```js
params: [
   '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238'
]
```

##### Returns

`Object` - A transaction receipt object, or `null` when no receipt was found:

  - `transactionHash `: `DATA`, 32 Bytes - hash of the transaction.
  - `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block.
  - `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in.
  - `blockNumber`: `QUANTITY` - block number where this transaction was in.
  - `from`: `DATA`, 20 Bytes - address of the sender.
  - `to`: `DATA`, 20 Bytes - address of the receiver. null when it's a contract creation transaction.
  - `cumulativeGasUsed `: `QUANTITY ` - The total amount of gas used when this transaction was executed in the block.
  - `gasUsed `: `QUANTITY ` - The amount of gas used by this specific transaction alone.
  - `contractAddress `: `DATA`, 20 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
  - `logs`: `Array` - Array of log objects, which this transaction generated.
  - `logsBloom`: `DATA`, 256 Bytes - Bloom filter for light clients to quickly retrieve related logs.

It also returns _either_ :

  - `root` : `DATA` 32 bytes of post-transaction stateroot (pre Byzantium)
  - `status`: `QUANTITY` either `1` (success) or `0` (failure)


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getTransactionReceipt","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}'

// Result
{
"id":1,
"jsonrpc":"2.0",
"result": {
     transactionHash: '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238',
     transactionIndex:  '0x1', // 1
     blockNumber: '0xb', // 11
     blockHash: '0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b',
     cumulativeGasUsed: '0x33bc', // 13244
     gasUsed: '0x4dc', // 1244
     contractAddress: '0xb60e8dd61c5d32be8058bb8eb970870f07233155', // or null, if none was created
     logs: [{
         // logs as returned by getFilterLogs, etc.
     }, ...],
     logsBloom: "0x00...0", // 256 byte bloom filter
     status: '0x1'
  }
}
```

***

#### core_newFilter

Creates a filter object, based on filter options, to notify when the state changes (logs).
To check if the state has changed, call [core_getFilterChanges](#core_getfilterchanges).

##### A note on specifying topic filters:
Topics are order-dependent. A transaction with a log with topics [A, B] will be matched by the following topic filters:
* `[]` "anything"
* `[A]` "A in first position (and anything after)"
* `[null, B]` "anything in first position AND B in second position (and anything after)"
* `[A, B]` "A in first position AND B in second position (and anything after)"
* `[[A, B], [A, B]]` "(A OR B) in first position AND (A OR B) in second position (and anything after)"

##### Parameters

1. `Object` - The filter options:
  - `fromBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `toBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `address`: `DATA|Array`, 20 Bytes - (optional) Contract address or a list of addresses from which logs should originate.
  - `topics`: `Array of DATA`,  - (optional) Array of 32 Bytes `DATA` topics. Topics are order-dependent. Each topic can also be an array of DATA with "or" options.

```js
params: [{
  "fromBlock": "0x1",
  "toBlock": "0x2",
  "address": "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b", null, ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b", "0x0000000000000000000000000aff3454fce5edbc8cca8697c15331677e6ebccc"]]
}]
```

##### Returns

`QUANTITY` - A filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_newFilter","params":[{"topics":["0x0000000000000000000000000000000000000000000000000000000012341234"]}],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### core_newBlockFilter

Creates a filter in the node, to notify when a new block arrives.
To check if the state has changed, call [core_getFilterChanges](#core_getfilterchanges).

##### Parameters
None

##### Returns

`QUANTITY` - A filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_newBlockFilter","params":[],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```

***

#### core_newPendingTransactionFilter

Creates a filter in the node, to notify when new pending transactions arrive.
To check if the state has changed, call [core_getFilterChanges](#core_getfilterchanges).

##### Parameters
None

##### Returns

`QUANTITY` - A filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_newPendingTransactionFilter","params":[],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```

***

#### core_uninstallFilter

Uninstalls a filter with given id. Should always be called when watch is no longer needed.
Additonally Filters timeout when they aren't requested with [core_getFilterChanges](#core_getfilterchanges) for a period of time.


##### Parameters

1. `QUANTITY` - The filter id.

```js
params: [
  "0xb" // 11
]
```

##### Returns

`Boolean` - `true` if the filter was successfully uninstalled, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_uninstallFilter","params":["0xb"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### core_getFilterChanges

Polling method for a filter, which returns an array of logs which occurred since last poll.


##### Parameters

1. `QUANTITY` - the filter id.

```js
params: [
  "0x16" // 22
]
```

##### Returns

`Array` - Array of log objects, or an empty array if nothing has changed since last poll.

- For filters created with `core_newBlockFilter` the return are block hashes (`DATA`, 32 Bytes), e.g. `["0x3454645634534..."]`.
- For filters created with `core_newPendingTransactionFilter ` the return are transaction hashes (`DATA`, 32 Bytes), e.g. `["0x6345343454645..."]`.
- For filters created with `core_newFilter` logs are objects with following params:

  - `removed`: `TAG` - `true` when the log was removed, due to a chain reorganization. `false` if its a valid log.
  - `logIndex`: `QUANTITY` - integer of the log index position in the block. `null` when its pending log.
  - `transactionIndex`: `QUANTITY` - integer of the transactions index position log was created from. `null` when its pending log.
  - `transactionHash`: `DATA`, 32 Bytes - hash of the transactions this log was created from. `null` when its pending log.
  - `blockHash`: `DATA`, 32 Bytes - hash of the block where this log was in. `null` when its pending. `null` when its pending log.
  - `blockNumber`: `QUANTITY` - the block number where this log was in. `null` when its pending. `null` when its pending log.
  - `address`: `DATA`, 20 Bytes - address from which this log originated.
  - `data`: `DATA` - contains the non-indexed arguments of the log.
  - `topics`: `Array of DATA` - Array of 0 to 4 32 Bytes `DATA` of indexed log arguments. (In *solidity*: The first topic is the *hash* of the signature of the event (e.g. `Deposit(address,bytes32,uint256)`), except you declared the event with the `anonymous` specifier.)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getFilterChanges","params":["0x16"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "logIndex": "0x1", // 1
    "blockNumber":"0x1b4", // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
    "transactionIndex": "0x0", // 0
    "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },{
      ...
    }]
}
```

***

#### core_getFilterLogs

Returns an array of all logs matching filter with given id.


##### Parameters

1. `QUANTITY` - The filter id.

```js
params: [
  "0x16" // 22
]
```

##### Returns

See [core_getFilterChanges](#core_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getFilterLogs","params":["0x16"],"id":74}'
```

Result see [core_getFilterChanges](#core_getfilterchanges)

***

#### core_getLogs

Returns an array of all logs matching a given filter object.

##### Parameters

1. `Object` - The filter options:
  - `fromBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `toBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `address`: `DATA|Array`, 20 Bytes - (optional) Contract address or a list of addresses from which logs should originate.
  - `topics`: `Array of DATA`,  - (optional) Array of 32 Bytes `DATA` topics. Topics are order-dependent. Each topic can also be an array of DATA with "or" options.
  - `blockhash`:  `DATA`, 32 Bytes - (optional) `blockHash` is a filter option which restricts the logs returned to the single block with the 32-byte hash `blockHash`.  Using `blockHash` is equivalent to `fromBlock` = `toBlock` = the block number with hash `blockHash`.  If `blockHash` is present in the filter criteria, then neither `fromBlock` nor `toBlock` are allowed.

```js
params: [{
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]
}]
```

##### Returns

See [core_getFilterChanges](#core_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getLogs","params":[{"topics":["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]}],"id":74}'
```

Result see [core_getFilterChanges](#core_getfilterchanges)

***

#### shh_version

Returns the current whisper protocol version.

##### Parameters
none

##### Returns

`String` - The current whisper protocol version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_version","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "2"
}
```

***

#### shh_post

Sends a whisper message.

##### Parameters

1. `Object` - The whisper post object:
  - `from`: `DATA`, 60 Bytes - (optional) The identity of the sender.
  - `to`: `DATA`, 60 Bytes - (optional) The identity of the receiver. When present whisper will encrypt the message so that only the receiver can decrypt it.
  - `topics`: `Array of DATA` - Array of `DATA` topics, for the receiver to identify messages.
  - `payload`: `DATA` - The payload of the message.
  - `priority`: `QUANTITY` - The integer of the priority in a range from ... (?).
  - `ttl`: `QUANTITY` - integer of the time to live in seconds.

```js
params: [{
  from: "0x04f96a5e25610293e42a73908e93ccc8c4d4dc0edcfa9fa872f50cb214e08ebf61a03e245533f97284d442460f2998cd41858798ddfd4d661997d3940272b717b1",
  to: "0x3e245533f97284d442460f2998cd41858798ddf04f96a5e25610293e42a73908e93ccc8c4d4dc0edcfa9fa872f50cb214e08ebf61a0d4d661997d3940272b717b1",
  topics: ["0x776869737065722d636861742d636c69656e74", "0x4d5a695276454c39425154466b61693532"],
  payload: "0x7b2274797065223a226d6",
  priority: "0x64",
  ttl: "0x64",
}]
```

##### Returns

`Boolean` - returns `true` if the message was send, otherwise `false`.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_post","params":[{"from":"0xc931d93e97ab07fe42d923478ba2465f2..","topics": ["0x68656c6c6f20776f726c64"],"payload":"0x68656c6c6f20776f726c64","ttl":0x64,"priority":0x64}],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### core_getAllCandidates

Returns a list of all the witness candidates.

##### Parameters

None

##### Returns

`DATA` - a list of all the witness candidates.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getAllCandidates","params":[],"id":1}'

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "Owner": "0x42a875Ac43f2b4e6D17f54D288071f5952bF8911",
            "Active": true,
            "Url": "/ip4/127.0.0.1/tcp/5213/ipfs/1kHHWuQNUVV2wgE8SqzQjWhiFQcfpkP5tRVTdJXAPWVj4nR",
            "VoteCount": 0,
            "TotalBounty": 220500000000000000000,
            "ExtractedBounty": 0,
            "LastExtractTime": 0
        },
        {
            "Owner": "0x122369F04f32269598789998de33e3d56E2C507a",
            "Active": true,
            "Url": "/ip4/127.0.0.1/tcp/5210/ipfs/1kHcch6yuBCgC5nPPSK3Yp7Es4c4eenxAeK167pYwUvNjRo",
            "VoteCount": 0,
            "TotalBounty": 2.0715e+21,
            "ExtractedBounty": 1.1535e+21,
            "LastExtractTime": 1545359465
        },
        {
            "Owner": "0xbf66D398226F200467cD27B14e85b25a8c232384",
            "Active": true,
            "Url": "/ip4/127.0.0.1/tcp/5213/ipfs/1kHHWuQNUVV2wgE8SqzQjWhiFQcfpkP5tRVTdJXAPWVj4nR",
            "VoteCount": 0,
            "TotalBounty": 202500000000000000000,
            "ExtractedBounty": 0,
            "LastExtractTime": 0
        },
        {
            "Owner": "0x3DcF0b3787C31B2bdF62d5bC9128A79c2bb18829",
            "Active": true,
            "Url": "/ip4/127.0.0.1/tcp/5211/ipfs/1kHJFKr2bzUnMr1NbeyYbYJa3RXT18cEu7cNDrHWjg8XYKB",
            "VoteCount": 0,
            "TotalBounty": 247500000000000000000,
            "ExtractedBounty": 0,
            "LastExtractTime": 0
        }
    ]
}
```

***

#### core_getVoter

Returns a voter's information, stake information included.

##### Parameters

1. `DATA`, 20 Bytes - address of voter.

##### Returns

`DATA` - a voter's information.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getVoter","params":["0x122369f04f32269598789998de33e3d56e2c507a"],"id":1}'

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "Owner": "0x122369f04f32269598789998de33e3d56e2c507a",
        "IsProxy": false,
        "ProxyVoteCount": 0,
        "Proxy": "0x0000000000000000000000000000000000000000",
        "LastVoteCount": 9,
        "LastVoteTimeStamp": 1545039733,
        "VoteCandidates": [
            "0x122369f04f32269598789998de33e3d56e2c507a"
        ],
        "StakeCount": 10,
        "LastStakeTimeStamp": 1545039795
    }
}
```

***

#### core_getRestVNTBounty

Returns a value of the left VNT bounty.

##### Parameters

None.

##### Returns

 `QUANTITY` - integer of the left VNT bounty in wei.

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"core_getRestVNTBounty","params":[],"id":1}'

// Result
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": 9.99998914e+26
}
```
