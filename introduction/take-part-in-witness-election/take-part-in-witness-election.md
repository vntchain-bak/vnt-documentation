# 投票合约使用教程

本文介绍投票合约在gvnt console的使用方法，投票合约的使用方式和普通的合约是一致的。如果了解普通合约的使用，很快就可以掌握投票合约的使用，下面主要介绍投票合约的使用，会捎带介绍合约的基本使用。

### 基本介绍

投票合约的作用是选出见证人。投票合约提供了以下11个功能：

1. 投票人抵押stake、取消抵押
2. 注册候选人、取消注册
3. 投票人为候选人投票、取消投票
4. 投票人设置代理人、取消代理，投票人开启代理、关闭代理
5. 绑定候选人和取消绑定候选人
6. 向选举合约捐献超级节点激励

以上每个功能对应了投票合约的1个函数，具体将在下面介绍。

### 投票合约基本信息

#### 投票合约地址

`0x0000000000000000000000000000000000000009`

#### 投票合约ABI

ABI描述了合约的每个函数的入参和输出结果。

```js
[
    {
        "name":"registerWitness",
        "inputs":[
            {
                "name":"nodeUrl",
                "type":"bytes"
            },
            {
                "name":"website",
                "type":"bytes"
            },
            {
                "name":"nodeName",
                "type":"bytes"
            },
            {
                "name":"binder",
                "type":"address"
            },
            {
                "name":"beneficiary",
                "type":"address"
            }
        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"unregisterWitness",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"voteWitnesses",
        "inputs":[
            {
                "name":"candidate",
                "type":"address[]"
            }
        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"cancelVote",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"startProxy",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"stopProxy",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"cancelProxy",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"setProxy",
        "inputs":[
            {
                "name":"proxy",
                "type":"address"
            }
        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"$stake",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"unStake",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"$depositReward",
        "inputs":[

        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"$bindCandidate",
        "inputs":[
            {
                "name":"candidate",
                "type":"address"
            },
            {
                "name":"beneficiary",
                "type":"address"
            }
        ],
        "outputs":[

        ],
        "type":"function"
    },
    {
        "name":"unbindCandidate",
        "inputs":[
            {
                "name":"candidate",
                "type":"address"
            },
            {
                "name":"beneficiary",
                "type":"address"
            }
        ],
        "outputs":[

        ],
        "type":"function"
    }
]
```

### 投票合约的使用

在console使用ABI获取投票合约实例：

```js
var abi =[{"name":"registerWitness","inputs":[{"name":"nodeUrl","type":"bytes"},{"name":"website","type":"bytes"},{"name":"nodeName","type":"bytes"},{"name":"binder","type":"address"},{"name":"beneficiary","type":"address"}],"outputs":[],"type":"function"},{"name":"unregisterWitness","inputs":[],"outputs":[],"type":"function"},{"name":"voteWitnesses","inputs":[{"name":"candidate","type":"address[]"}],"outputs":[],"type":"function"},{"name":"cancelVote","inputs":[],"outputs":[],"type":"function"},{"name":"startProxy","inputs":[],"outputs":[],"type":"function"},{"name":"stopProxy","inputs":[],"outputs":[],"type":"function"},{"name":"cancelProxy","inputs":[],"outputs":[],"type":"function"},{"name":"setProxy","inputs":[{"name":"proxy","type":"address"}],"outputs":[],"type":"function"},{"name":"$stake","inputs":[],"outputs":[],"type":"function"},{"name":"unStake","inputs":[],"outputs":[],"type":"function"},{"name":"$depositReward","inputs":[],"outputs":[],"type":"function"},{"name":"$bindCandidate","inputs":[{"name":"candidate","type":"address"},{"name":"beneficiary","type":"address"}],"outputs":[],"type":"function"},{"name":"unbindCandidate","inputs":[{"name":"candidate","type":"address"},{"name":"beneficiary","type":"address"}],"outputs":[],"type":"function"}]
var voteContract = vnt.core.contract(abi).at("0x0000000000000000000000000000000000000009");
```

#### 合约的调用方法

下面是合约调用的基本方法：

```js
tx=voteContract.$stake.sendTransaction({from:core.coinbase, value:5e18})
```

合约调用包含5部分，其中：
- voteContract：合约实例，本例中是投票合约示例
- $stake：要调用的合约函数，本例是$stake函数，其中`$`代表本函数是可转账函数，即调用合约的同时进行转账
- sendTransaction: 使用此RPC接口发送交易，即通过交易调用合约
- `{from:core.coinbase, value:5e18}`：sendTransaction的参数，详细见[core_sendTransaction](https://github.com/vntchain/vnt-documentation/blob/master/api/vnt-json-rpc-api.md#core_sendtransaction)

通过以上介绍的方法，为大家提供投票合约每个函数的调用样例。

#### 注册见证人节点

从ABI可以看出来，当前注册见证人只需要提供5个参数：
- nodename: 节点名称
- website：节点网站网址
- nodeurl：函数参数，见证人函数的参数
- binder: 与该候选节点建立联系的绑定人
- beneficiary：与该候选节点建立联系的绑定人的受益人账号，该账号接收超级节点的所有受益人地址

```js
name="greatvnt"
website="www.greatvnt.com"
nodeurl="/ip4/127.0.0.1/tcp/5211/ipfs/1kHJFKr2bxUnMr1dbeyYbYJa3RXT18cEu7cNDrHWjg8XYKB"
binder="0x02f8d9c9bb81b3a81bf13d4ec8818be5918d1250"
beneficiary="0x123456c9bb81b3a81bf13d4ec8818be591812345"
tx=voteContract.registerWitness.sendTransaction(nodeurl,website,name, binder,beneficiary,{from: core.coinbase})
```

#### 取消注册见证人节点

取消注册见证人无入参。

```js
tx=voteContract.unregisterWitness.sendTransaction({from:core.coinbase})
```
#### 绑定候选人（见证人节点）

和候选人进行绑定，需要提供和候选人一致的见证人节点账号和受益人账号。发送该交易的账号为绑定人。

需要提供2个参数：
- candidate: 见证人/超级节点/候选人的地址。
- beneficiary: 受益人账号地址，超级节点的收益都将发放到该账号。

另外，还需要填写转账金额`value`：1000万VNT，所以需要确保绑定人账号至少拥有1000万VNT。

```js
candidate="0x491f4e8d914e30b1a5e8c804789094fe30971807"
beneficiary="0x123456c9bb81b3a81bf13d4ec8818be591812345"
tx=voteContract.$bindCandidate.sendTransaction(candidate,beneficiary,{from: core.coinbase, value:1e25})
```

#### 取消绑定候选人（见证人节点）

和候选人取消绑定，需要提供和候选人一致的见证人节点账号和受益人账号，以进行匹配和验证。

需要提供2个参数：
- candidate: 见证人/超级节点/候选人的地址。
- beneficiary: 受益人账号地址，超级节点的收益都将发放到该账号。

```js
candidate="0x491f4e8d914e30b1a5e8c804789094fe30971807"
beneficiary="0x123456c9bb81b3a81bf13d4ec8818be591812345"
tx=voteContract.unbindCandidate.sendTransaction(candidate,beneficiary,{from: core.coinbase})
```

#### 投票人抵押代币

投票人在投票前需要抵押代币，参数只有1个，即本次抵押代币的数量，代币单位为Wei，示例为抵押了5个VNT。

```js
tx=voteContract.$stake.sendTransaction({from:core.coinbase, value:5e18})
```

#### 投票人取消抵押代币

投票人赎回抵押的代币。

```js
tx=voteContract.unStake.sendTransaction({from:core.coinbase})
```

#### 投票人投票

投票见证人只有1个参数：候选人账号地址的列表。示例是把票投给了自己。

> 提醒：当被投票的账号已不是候选人时，候选人无法获得票数。当所有被投账户不是候选人时，投票会执行成功，浪费你的一次投票机会。

```js
tx=voteContract.voteWitnesses.sendTransaction([core.coinbase], {from:core.coinbase})
```

#### 投票人取消投票

投票人取消对见证人的投票。

```js
tx=voteContract.cancelVote.sendTransaction({from:core.coinbase})
```

#### 开启代理

开启代理成为代理人，代表节点愿意替代别人投票。

```js
tx=voteContract.startProxy.sendTransaction({from:core.coinbase})
```

#### 关闭代理

关闭代理不再当代理人，代表不再代表其他人投票。

```js
tx=voteContract.stopProxy.sendTransaction({from:core.coinbase})
```

#### 设置代理

让代理节点替代自己投票，示例让`0x2c6822e1529e5bb080b1d0290ec035e77c0a7260`替代本账号投票。

```js
tx=voteContract.setProxy.sendTransaction("0x2c6822e1529e5bb080b1d0290ec035e77c0a7260"，{from:core.coinbase})
```

#### 取消代理

取消之前设置的代理，不让代理节点替代本节点投票。

```js
tx=voteContract.cancelProxy.sendTransaction({from:core.coinbase})
```

#### 向合约保存超级节点奖金

超级节点所获得的激励，来自选举合约中保存的激励数量。激励有2个来源：

1. 基金会捐献到选举合约。
1. 个人捐献到选举合约。

需要确保发送交易的账号有足够的捐献余额。

```js
amount=1e25 // 待转账金额，可自行设定
tx=voteContract.$depositReward.sendTransaction({from:core.coinbase, value:amount})
```

### 查询投票信息

当前提供了3个JSON RPC API可以查询投票合约信息，具体信息见[JSON RPC API文档](../../api/vnt-json-rpc-api.md)。

- `core_getVoter`：获取某个投票人的所有信息。
- `core_getAllCandidates`：获取所有见证人候选人的信息。
- `core_getRestVNTBounty`：获取当前超级节点剩余的VNT激励剩余值。
