# 如何加入VNT Hubble主网

本文目录：
- [主网基本信息](#主网基本信息)
- [加入主网](#加入主网)
- [其他资料](#其他资料)


## 主网基本信息
VNT Hubble主由19个见证人节点组成，负责执行交易和打包区块，部署了3个公共全节点，并开启了RPC服务，查询和发送交易时可以使用这些公共全节点。

**待定**

19个初始的见证人P2P地址：
```
```

3个公共全节点的P2P地址:
```
```

3个公共全节点的RPC信息是：

**待定**
```
```

## 加入主网

我们欢迎任何人加入到Hubble主网，加入主网的步骤如下：

第一步：部署节点首先要克隆`go-vnt`源码，具体安装和编译请关注go-vnt项目[README.md](<https://github.com/vntchain/go-vnt#%E4%BB%8E%E6%BA%90%E7%A0%81%E5%AE%89%E8%A3%85gvnt>)。

第二步：创建并进入节点目录。

```bash
cd ~
mkdir vntnode
cd vntnode
```

第三步：使用`init.json`文件初始化节点，测试网`init.json`内容如下：

**待定**
```json
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

第五步：使用任一公共全节点的p2p地址作为bootnode，启动节点：

**待定：填写全节点p2p地址**
```bash
gvnt --networkid 2 --datadir . --port 3001 --vntbootnode "" --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 8888 --rpcapi="db,core,net,vnt,personal" console
```



成功执行以上5步，你应当已经连接上了VNT Hubble主网，并且开始同步区块，区块同步可采用以下方法查看：


通过`attach`命令连接到节点：
```bash
$ cd vntnode
$ gvnt attach gvnt.ipc
```

查看同步：
```
> core.syncing
{
  currentBlock: 1992176,
  highestBlock: 2003529,
  knownStates: 0,
  pulledStates: 0,
  startingBlock: 1990059
}
```


## 其他资料

如果你想成为Hubble超级节点，请查考[如何成为超级节点](./../04-bp/01.become-to-witness.md)。