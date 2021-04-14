# 如何加入VNT Hubble测试网

本文目录：
- [测试网基本信息](#测试网基本信息)
- [加入测试网](#加入测试网)
- [测试网水龙头](#测试网水龙头)
- [其他资料](#其他资料)


## 测试网基本信息

VNT Hubble测试网共部署了19个见证人节点，负责执行交易和打包区块，部署了3个公共全节点，并开启了RPC服务，查询和发送交易时可以使用这些公共全节点。

19个初始的见证人P2P地址：

```bash
"/ip4/192.168.9.106/tcp/3002/ipfs/1kHQpRW4VZbDPx3HTBKFHotHekoHRzncQiosfT8roc5nsSR",
"/ip4/192.168.9.113/tcp/3002/ipfs/1kHP5ZqyCSYieopq1BkJMYvTvvJNwUXuDCLMRq3qVRG1hwJ",
"/ip4/192.168.9.107/tcp/3002/ipfs/1kHBc6rBtQh4Y4YPh4daLS8LYALsWQ6G8aKf633R2EhzBQc",
"/ip4/192.168.9.114/tcp/3002/ipfs/1kHjPSvoBZzA51Dv8DeZVgqjhMeDZWuwNkQirXsQeeBKzTc",
"/ip4/192.168.9.124/tcp/3002/ipfs/1kHktVU6Qg5RgagNN4vzzCnr6LMrjsTh5CtrCbmztqc2Rrt",
"/ip4/192.168.9.112/tcp/3002/ipfs/1kHLejtggY19L1Qa9MVZ1gnsRNNqqTprAiKQYqK4wBy74nK",
"/ip4/192.168.9.136/tcp/3002/ipfs/1kHDzrP344t2NmAK8cmCChJm6aL4URSibn6Tbdg1VkMY1mR"
```

一个公共引导节点的P2P地址：
```bash
"/ip4/47.111.100.232/tcp/3002/ipfs/1kHhAQ9chwHuc6C3RAm3NZziu5PDY8VLBkueJ6vq7b3tSVU"
```

3个公共全节点的RPC信息是：

```bash
http://101.37.164.86:8880
http://47.111.100.232:8880
```

## 加入测试网

除了使用公共节点外，对于开发者还有更好的选择是，部署私有的全节点，该全节点可以部署在局域网内网和公网环境，但RPC信息不对外透露，实现了独自使用的目的。

第一步：部署节点首先要克隆`go-vnt`源码，具体安装和编译请关注go-vnt项目[README.md](<https://github.com/vntchain/go-vnt#%E4%BB%8E%E6%BA%90%E7%A0%81%E5%AE%89%E8%A3%85gvnt>)。

第二步：创建并进入节点目录。

```bash
cd ~
mkdir vntnode
cd vntnode
```

第三步：使用`init.json`文件初始化节点，测试网`init.json`内容如下：

```json
{
    "config": {
        "chainId": 2,
        "dpos": {
            "period": 2,
            "witnessesnum": 7,
            "witnessesUrl": [
                "/ip4/192.168.9.106/tcp/3002/ipfs/1kHQpRW4VZbDPx3HTBKFHotHekoHRzncQiosfT8roc5nsSR",
                "/ip4/192.168.9.113/tcp/3002/ipfs/1kHP5ZqyCSYieopq1BkJMYvTvvJNwUXuDCLMRq3qVRG1hwJ",
                "/ip4/192.168.9.107/tcp/3002/ipfs/1kHBc6rBtQh4Y4YPh4daLS8LYALsWQ6G8aKf633R2EhzBQc",
                "/ip4/192.168.9.114/tcp/3002/ipfs/1kHjPSvoBZzA51Dv8DeZVgqjhMeDZWuwNkQirXsQeeBKzTc",
                "/ip4/192.168.9.124/tcp/3002/ipfs/1kHktVU6Qg5RgagNN4vzzCnr6LMrjsTh5CtrCbmztqc2Rrt",
                "/ip4/192.168.9.112/tcp/3002/ipfs/1kHLejtggY19L1Qa9MVZ1gnsRNNqqTprAiKQYqK4wBy74nK",
                "/ip4/192.168.9.136/tcp/3002/ipfs/1kHDzrP344t2NmAK8cmCChJm6aL4URSibn6Tbdg1VkMY1mR"
            ]
        }
    },
    "timestamp": "0x5b45b949",
    "extraData": "0x",
    "gasLimit": "0x47b760",
    "difficulty": "0x1",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {
        "0x02f8d9c9bb81b3a81bf13d4ec8818be5918d3658": {
            "balance": "0x9b18ab5df7180b6b8000000"
        },
        "0xa0959738e3555c54577cca3c721b674c5c18072e": {
            "balance": "0x6765c793fa10079d0000000"
        },
        "0xf3e3b67550a6f37f596a1ba2e7357e946c82c905": {
            "balance": "0x6765c793fa10079d0000000"
        },
        "0x838c5c23372ccd88fc5fc4943e2eda5f3a0ec35e": {
            "balance": "0x6765c793fa10079d0000000"
        }
    },
    "witnesses": [
       "0xb300a4b899e57263cdc6aa851995bf33366e862c",
       "0x88cbe08dd0a90d85102976f810b56dfed8728f55",
       "0x99e8263ceffea1b1e5b136a3a133f5064501e45d",
       "0x2d39a693b2d65fa709b54a6d72f82623feaf71b9",
       "0x41c7bba7c90783e69f09d63c9986d7bc9424c144",
       "0xe840a1d3c904392d421fc5b460ef09b547de5c4e",
       "0x800cf4b8b3d8f549299d291fb66497ce6653c2ca"
    ],
    "number": "0x0",
    "gasUsed": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

创建`init.json`文件，并写入以上内容。然后使用`init.json`进行初始化：

```bash
gvnt init init.json --datadir .
```

第四步：创建gvnt账号，可以设置密码：

```bash
gvnt account new .
```

如果你已经有账号，可以直接将账号keystore文件拷贝到`vntnode/keystore`目录。

第五步：使用公共全节点的p2p地址作为bootnode，启动私有节点：

```bash
gvnt --networkid 2 --datadir . --port 3001 --vntbootnode "/ip4/47.111.100.232/tcp/3002/ipfs/1kHhAQ9chwHuc6C3RAm3NZziu5PDY8VLBkueJ6vq7b3tSVU" --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 8880 --rpcapi="db,core,net,vnt,personal" console
```



成功执行以上5步，你应当已经连接上了VNT Hubble测试网，并且开始同步区块，区块同步可采用以下方法查看：

通过`attach`命令连接到节点：
```bash
$ cd vntnode
$ gvnt attach gvnt.ipc
```

查看同步：
```bash
> core.syncing
{
  currentBlock: 1992176,
  highestBlock: 2003529,
  knownStates: 0,
  pulledStates: 0,
  startingBlock: 1990059
}
```

## 测试网水龙头

在测试网上进行开发和测试，需要使用VNT测试代币，如果你的账号没有代币，或代币不足，可在[测试网水龙头](https://hubscan.vnt.link/faucet)申请测试代币。

## 其他资料

1. 如果你想成为Hubble超级节点，请查看[如何成为超级节点](https://github.com/vntchain/vnt-documentation/blob/master/developer-guide/04-bp/become-to-witness.md)。
2. 如果你想加入Hubble主网，成为全节点，请查看[如何加入Hubble主网](https://github.com/vntchain/vnt-documentation/blob/master/developer-guide/05-network/connect-to-hubble-network.md)。
