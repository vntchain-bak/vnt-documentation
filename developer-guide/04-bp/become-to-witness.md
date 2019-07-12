# 竞选Hubble超级节点（见证人）

本文目录：

- [超级节点硬件推荐配置](#超级节点硬件推荐配置)
- [BP规范](#BP规范)
- [网络安全推荐配置](#网络安全推荐配置)
- [连接到Hubble主网](#连接到Hubble主网)
- [注册为候选节点](#注册为候选节点)
- [查看候选节点排名](#查看候选节点排名)
- [开启共识](#开启共识)
- [相关资料](#相关资料)

## 超级节点硬件推荐配置

- Platinum 8163 2.5 GHz 
- CPU：16core 2.5GHz
- 内存：64GB
- 磁盘：SSD，2TB
- 网络：50M固定带宽

## BP规范

成为超级节点应当遵守以下规范，若违反规范，社区有权对违反规范的超级节点账号实施惩罚：

1. 注册为候选节点后，应当始终保持节点在线
1. 注册超级节点时，提供必要的、准确的信息
1. 不可贿选


## 网络安全推荐配置

超级节点应当主动做好安全配置，抵御黑客攻击，持续产生区块和防止重要数据被盗。超级节点至少应当做到以下安全配置：

1. 保存好超级节点的私钥keystore文件，泄露会造成超级节点的资金损失
1. gvnt不开启RPC服务，开启RPC也有可能造成超级节点的资金损失
1. 系统防火墙只允许P2P网络端口通过，禁止掉无关端口

## 连接到Hubble主网

请看[如何加入VNT Hubble主网](../05-network/connect-to-hubble-network.md)。

## 注册为候选节点

注册超级节点需要节点调用选举合约进行注册，需要提供以下3个信息：

1. 节点名称
1. 官网地址
1. 节点的p2p地址

其中官方网址是用来做超级节点的运营与宣传所用，官网网站的根目录下面，需要放置一个可被访问到的配置文件bp.json，比如其路径为`https://www.website.com/bp.json`，该文件包含了超级节点的一些额外描述信息，这些信息可随时改变，区块链浏览器等工具可以读取这个信息，展示对超级节点进行更多的展示，文件内容和格式如下：

```json
{
	"candidate_name": "VNT Europe",
	"candidate_address": "0x02f8d9c9bb81b3a81bf13d4ec8818be5918d3650",
	"website": "https://www.vnteu.com",
	"branding": {
		"logo_256": "https://www.vnteu.com/logo-256px.png",
		"logo_1024": "https://www.vnteu.com/logo-1024px.png",
		"logo_svg": "https://www.vnteu.com/logo.svg"
	},
	"location": {
		"name": "Paris",
		"country": "France",
		"latitude": 48.8566,
		"longitude": 2.3522
	}
}
```

- candidate_name: 节点名称
- candidate_address: 节点账户地址
- website: 节点官网
- branding: 节点logo
- location: 
	- name：城市名
	- country：国家
	- latitude，longitude: 经纬度

注册超级节点有2种方式：

1. 依照《投票合约使用教程》中的[注册见证人节点](https://github.com/vntchain/vnt-documentation/blob/master/introduction/take-part-in-witness-election/take-part-in-witness-election.md#%E6%B3%A8%E5%86%8C%E8%A7%81%E8%AF%81%E4%BA%BA%E8%8A%82%E7%82%B9)操作。
1. 使用选举工具[elect](https://github.com/vntchain/elect)的`regsiter`命令注册见证人。

推荐使用方式1，因为在部署完节点，连接到主网，等区块同步完毕后，可以直接使用节点发送注册见证人的交易。

这2种方式都会讲注册见证人的交易的hash值发送出来，可以使用gvnt控制台、[区块浏览器](https://hubscan.vnt.link)查询交易是否执行成功，当交易执行成功，可以查看候选节点排名。

## 查看候选节点排名

可以在Hubble官方浏览器查询[超级节点排名](https://hubscan.vnt.link/super-node)，所有注册成功的见证人都会显示在此网页，用户通过此跳转到超级节点网站，对超级节点进行了解。

## 开启共识

成为候选节点后，当超级节点的票数排名前19名，就会成为超级节点，超级节点要负责产生区块，所以所有候选节点和超级节点都要：
1. 解锁账号。节点打包区块，会对区块、BFT消息进行签名，需要解锁账号，使用命令在控制台执行`personal.unlockAccount(core.coinbase, password, X)`解锁超级节点的账号，其中`password`为账号密码，`X`为解锁的时间，单位为秒，时间需要尽量长，不然在节点还是见证人的时候，账号被锁住导致无法产生区块，超级节点将错失区块激励。
1. 开启共识。开启共识有2种方式：
    1. 在启动命令中增加`--produce`选项。
    2. 在gvnt控制台执行`bp.start()`。

## 相关资料

1. [投票合约使用教程](https://github.com/vntchain/vnt-documentation/blob/master/introduction/take-part-in-witness-election/take-part-in-witness-election.md)
1. [如何加入VNT Hubble主网](../05-network/connect-to-hubble-network.md)
1. [选举工具：elect](https://github.com/vntchain/elect)
