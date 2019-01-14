# 前提

## 运行环境
需要安装
```
node: v8.11.2
```

## 初始化部署目录
```
mkdir deploy
cd deploy
npm init  # 该步会生成package.json
```

## 安装依赖
安装vnt.js: 0.20.7

1. 需要将该依赖写到package.json中(如下所示)

package.json:

```js
{
  "name": "wasm_test",
  "version": "1.0.0",
  "description": "vnt.js test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "vnt": "git+http://github.com/vntchain/vnt.js.git#develop"
  }
}
```

2. 然后运行：

``npm install``

如果安装失败，是因为缺少g++包，请安装。

Centos下：`sudo yum install gcc gcc-c++`

其它操作系统请自查。


# 使用方法
## 第一步：导入vnt.js库
`var Vnt = require("vnt")`

## 第二步：创建vnt provider连接
```js
var vnt = new Vnt();
vnt.setProvider(new vnt.providers.HttpProvider("http://192.168.9.61:12340"));
```

## 第三步：准备要用到的账号并打开账户
```js
var from1 = "0x122369f04f32269598789998de33e3d56e2c507a"
var pass1 = ""
var from2 = "0x3dcf0b3787c31b2bdf62d5bc9128a79c2bb18829"
var pass2 = ""
var toAddr = "0x3ea7a559e44e8cabc362ca28b6211611467c76f3"

vnt.personal.unlockAccount(from1, pass1)
vnt.personal.unlockAccount(from2, pass2)
```
>  注：这些账号的对应私钥，必须存放在所连的provider数据目录中的keystore之下，否则无法使用
>  以上代码仅是例子，请根据自己的使用需求准备账号

## 第四步：准备合约代码
从文件系统中读取代码和abi
```js
var fs = require("fs")  // 这是用到的fs库，请在代码最前面引入

//定义代码路径
var codeFile = "/Users/yanfengxi/go/src/github.com/ethereum/go-ethereum/core/wasm/testdata/erc20/erc20.wasm"
//定义abi路径
var abiFile = "/Users/yanfengxi/go/src/github.com/ethereum/go-ethereum/core/wasm/testdata/erc20/abi.json"
//读取代码数据
var wasmcode = fs.readFileSync(codeFile)
//读取abi数据
var wasmabi = fs.readFileSync(abiFile)
//将abi数据解析成json结构
var abi = JSON.parse(wasmabi.toString("utf-8"))
//将代码进行base64编译，便于传输
var code = wasmcode.toString("base64")
```

## 第五步：合约创建
```js
//这是合约创建主函数
function deployWasmContract() {
  // 将code和abi进行打包，其中abi也需要进行一下base64编码以方便传输
  var pack = {
    Code: code,
    Abi: wasmabi.toString("base64")
  }

  // 将json形式的数据包转化成字符串，然后转化成16进制的字节码形式
  var json = JSON.stringify(pack)
  var txData = vnt.toHex(json)

  // 将生成的最终代码传到Deploy2函数中
  // doDeploy函数是做合约部署的地方
  doDeploy(txData)
}

function doDeploy(txData) {
    // 使用abi初始化一个合约对象
    var contract = vnt.core.contract(abi)

    // 部署合约
    var contractReturned = contract.new(1000000000, "bitcoin", "BTC", {
        from: from1,
        data: txData,
        gas: 4000000
    }, function(err, myContract){
       if(!err) {
          if(!myContract.address) {
              console.log("transactionHash: ", myContract.transactionHash)
          } else {
              console.log("contract address: ", myContract.address)
          }
       }
     });
}
```

## 第六步：合约调用

举两个例子

不产生实际交易的情况
=================
该情况只会进行一些查询工作，不会改变链的状态，比方说查询某个账号的代币余额

```js
function GetAmount(address) {
    console.log("get ammount of address: ", address)
    // 生成合约实例
    var contract = vnt.core.contract(abi).at(contractAddr)
    // 调用合约的GetAmount方法，注意用到了call
    r = contract.GetAmount.call(address, {from: from1})
    console.log("result", r.toString())
}
```

产生实际交易的情况
===============
```js
function Transfer(from, to, amount) {
    // 生成合约实例
    var contract = vnt.core.contract(abi).at(contractAddr)

    // 调用合约的transfer方法进行转账，注意用到了sendTransaction
    contract.transfer.sendTransaction(
    to,amount,{from: from}, function(err, txid) {
        if(err) {
            console.log("error happend: ", err)
        } else {
            getTransactionReceipt(txid, function(receipt) {
                console.log("status: ", receipt.status)
                GetAmount(to)
            })
        }
    })
}
```

根据txid获取交易明细
==================
由于vnt.js是通过异步的方式来发送交易，它在发送交易后，会直接返回txid，而不会等待交易执行完成，所以，我们需要自己写一个方法，来等待交易的完成：

```js
// 该方法会每隔一秒查询一下tx的信息，直到返回一个结果，并会调用回调函数
function getTransactionReceipt(tx, cb) {
  var receipt = vnt.core.getTransactionReceipt(tx)
  if(!receipt) {
      setTimeout(function () {
          getTransactionReceipt(tx, cb)
      }, 1000);
  } else {
      cb(receipt)
  }
}
```


# 参考资料
[VNT Javascript API](https://github.com/vntchain/vnt.js/blob/master/doc/api-reference.md)
