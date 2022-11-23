---
title: hardhat-bsc-demo
slug: hardhat-bsc-demo
author: [jianchengwang]
date: 2021-09-29
excerpt: "币安智能链是一种创新的解决方案，为币安链带来可编程性和互操作性。币安智能链依赖于一个由21个验证者组成的系统，该系统具有权益证明(PoSA)共识，可以支持更短的区块时间和更低的费用。最具粘合性的验证器候选区块将成为验证器和生产区块。"
draft: false
tags: 
- blockchain
---

币安智能链是一种创新的解决方案，为币安链带来可编程性和互操作性。币安智能链依赖于一个由21个验证者组成的系统，该系统具有权益证明(PoSA)共识，可以支持更短的区块时间和更低的费用。最具粘合性的验证器候选区块将成为验证器和生产区块。双符号检测和其他斩控逻辑保证了安全性、稳定性和链的终结性。以上来自复制于官网，感兴趣的可以自行查阅，[binance-smart-chain](https://docs.binance.org/smart-chain/guides/bsc-intro.html)

本文只简单介绍一下，通过`hardhat`创建一个智能合约代币，然后部署到币安智能链的流程。

## MetaMask

首先安装一下以太坊钱包，这里，可以到[谷歌商店安装插件](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn)，然后创建账号，注意保存好助记符`mnemonic`

新增一个币安的测试链网络，参考官方开发文档，[rpc配置](https://docs.binance.org/smart-chain/developer/rpc.html)

1. 网络名称： BSC Testnet  (自定义)
2. PRC URL： https://data-seed-prebsc-1-s1.binance.org:8545
3.  链ID：97
4. 代币符号： BNB
5. 区块链浏览器：https://testnet.bscscan.com/

然后可以到这个网站24小时领取一个BNB，[https://testnet.binance.org/faucet-smart ](https://testnet.binance.org/faucet-smart)，用于支付`gas`，

如果是`ETH`可以到这个网站免费领取，https://faucet.ropsten.be/

## Develop Enviroment

因为币安链是基于以太坊的，所以智能合约的开发语言是[solidity](https://docs.soliditylang.org/en/v0.8.8/)，你可以使用[remix](https://remix.ethereum.org/)在线编辑，

我这里在本地进行开发，基于[hardhat](https://hardhat.org/)框架开发，合约类型为[ERC20](https://eips.ethereum.org/EIPS/eip-20)，为了简便开发，可以到[openzeppelin](https://docs.openzeppelin.com/openzeppelin/)根据需要生成对应的智能合约模板，

下面根据[官方文档](https://docs.binance.org/smart-chain/developer/deploy/hardhat.html)，设置下基础环境，

```shell
# install node.js
# install hardhat
npm install --save-dev hardhat
# install recommand dependencies
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
# mkdir project
mkdir hardhat-demo
cd hardhat-demo
# init
npx hardhat
Once this project is initialized, you'll now have a project structure with the following items:
contracts/: Directory for Solidity contracts
scripts/: Directory for scriptable deployment files
test/: Directory for test files for testing your application and contracts
hardhat-config.js: Hardhat configuration file
```

这里本地使用`vscode`进行开发，搜索下solidity相关插件安装支持语法高亮，自此开发环境搭建完成。

## Smart Contract

代币合约代码，这个根据业务需要自行编辑，这里不多赘述

### compile

```shell
npx hardhat compile
```

### test

```shell
npx hardhat test
```

### deploying

首先在`hardhat.config.js`配置下网络信息，

```js
require("@nomiclabs/hardhat-waffle");
require('@nomiclabs/hardhat-ethers');
const { mnemonic } = require('./secrets.json');

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async () => {
  const accounts = await ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  defaultNetwork: "mainnet",
  networks: {
    localhost: {
      url: "http://127.0.0.1:8545"
    },
    hardhat: {
    },
    testnet: {
      url: "https://data-seed-prebsc-1-s1.binance.org:8545",
      chainId: 97,
      gasPrice: 20000000000,
      accounts: {mnemonic: mnemonic}
    },
    mainnet: {
      url: "https://bsc-dataseed.binance.org/",
      chainId: 56,
      gasPrice: 20000000000,
      accounts: {mnemonic: mnemonic}
    }
  },
  solidity: {
  version: "0.5.16",
  settings: {
    optimizer: {
      enabled: true
    }
   }
  },
  paths: {
    sources: "./contracts",
    tests: "./test",
    cache: "./cache",
    artifacts: "./artifacts"
  },
  mocha: {
    timeout: 20000
  }
};
```

这里的`mnemonic`就是我们之前创建的以太坊的钱包助记符，

然后我们可以部署到测试网络，

```shell
npx hardhat run --network testnet scripts/deploy.js
```

部署成功的话，我们就可以通过代币合约地址，在metamask上面添加代币信息了，

### verify

如果代币智能合约没校验的话，是不能进行`read`，`write`操作的，

需要安装插件

```shell
npm install --save-dev @nomiclabs/hardhat-etherscan
```

然后配置文件，`hardhat.config.js`引入，并添加[Bscscan API key](https://bscscan.com/myapikey)

```js
require("@nomiclabs/hardhat-etherscan");

module.exports = {
  // ...
  etherscan: {
    apiKey: apiKey
  }
}
```

执行校验命令，

```shell
npx hardhat verify --network testnet 0x25F547DeE6A315dc841830B7bB72fC9CE6EbF420
```

`0x25F547DeE6A315dc841830B7bB72fC9CE6EbF420`为代币合约地址

执行后，可以看到如下输出，

```shell
Nothing to compile
Compiling 1 file with 0.8.2
Successfully submitted source code for contract
contracts/PerformanceToken.sol:Token at 0x25F547DeE6A315dc841830B7bB72fC9CE6EbF420
for verification on Etherscan. Waiting for verification result...

Successfully verified contract Token on Etherscan.
https://testnet.bscscan.com/address/0x25F547DeE6A315dc841830B7bB72fC9CE6EbF420#code
```

我们可以到地址[https://testnet.bscscan.com/address/0x25F547DeE6A315dc841830B7bB72fC9CE6EbF420#code](https://testnet.bscscan.com/address/0x25F547DeE6A315dc841830B7bB72fC9CE6EbF420#code)

就可以对代币合约进行，`read`，`write`操作了，

## Local Dev

### localhost network

hardhat内置了eth的节点网络，方便本地开发，

```shell
# kill 8545
lsof -i :8545
# or window
netstat -aon|findstr "8545"
tasklist|findstr "23292"
taskkill /f /t /im "23292"

# 启动localhost网络
npx hardhat node
# 复制初始的钱包密钥，导入metamask

# 部署到本地网络
npx hardhat run --network localhost scripts/deploy.js
```

### build java

首先要编译输出abi，

```shell
# 我这边使用win10，所以这边用docker安装
 docker run ethereum/solc:stable <smart-contract>.sol –-bin –-abi –-optimize -o <output-dir>
 # 例子
docker run --rm -v /solidity/build:/sources -v /solidity/build/output:/output ethereum/solc:stable -o /output --abi --bin /sources/Token.sol
```

我们这边后端使用java调用，

这里用到https://github.com/web3j/web3j这个类库，

所以我们需要把智能合约编译，并生成java的封装类，这边使用官方提供的代码工具类`SolidityFunctionWrapperGenerator `

```shell
org.web3j.codegen.SolidityFunctionWrapperGenerator -b /path/to/<smart-contract>.bin -a /path/to/<smart-contract>.abi -o /path/to/src/main/java -p com.your.organisation.name
```

```java
   List<String> options = new ArrayList<>();
        options.add("-b");
        options.add(TokenClassGen.class.getResource("/Token.bin").getPath());
        options.add("-a");
        options.add(TokenClassGen.class.getResource("/Token.abi").getPath());
        options.add("-o");
        options.add(TokenClassGen.class.getResource("/").getPath());
        options.add("-p");
        options.add("com.test.contract");
        SolidityFunctionWrapperGenerator.main(options.toArray(new String[options.size()]));
```

## Private Chain

### 相关文档

- BSC官网：https://www.binance.org/cn
- BSC链github地址：https://github.com/binance-chain/bsc/releases
- 主网区块链浏览器：https://bscscan.com/
- 主网链官方部署文档：:https://docs.binance.org/smart-chain/developer/fullnode.html
- 手续费（gasprice）：https://bscgas.info/gas
- 创世文件及配置文件参考：https://api.github.com/repos/binance-chain/bsc/releases/latest
- 开发者文档：https://docs.binance.org/index.html
- 公共节点：https://docs.binance.org/smart-chain/developer/rpc.html
- 生态系统：https://github.com/binance-chain/bsc-ecosystem

### BSC开发镜像生成

```shell
mkdir /opt/docker/bsc-private
```

下载基础镜像

```shell
docker pull buildpack-deps:jessie-curl
```

编写`Dockerfile`

```dockerfile
FROM buildpack-deps:jessie-curl
RUN wget -P /usr/local/bin/ https://github.com/binance-chain/bsc/releases/download/v1.1.3/geth_linux && mv /usr/local/bin/geth_linux /usr/local/bin/geth && chmod +x /usr/local/bin/geth
EXPOSE 8545 8546 30303 30303/udp
WORKDIR /data
ENTRYPOINT ["geth"]
```

如果github下载不了，也可以先下载到本地

```shell
proxychains4 wget https://github.com/binance-chain/bsc/releases/download/v1.1.3/geth_linux
```

```dockerfile
FROM buildpack-deps:jessie-curl
ADD geth_linux /usr/local/bin/
RUN mv /usr/local/bin/geth_linux /usr/local/bin/geth && chmod +x /usr/local/bin/geth
EXPOSE 8545 8546 30303 30303/udp
WORKDIR /data
ENTRYPOINT ["geth"]
```

生成私链镜像

```shell
docker build . -t private_bsc:v1.1.3
```

### BSC容器生成

#### 配置文件编写

参考: https://github.com/binance-chain/bsc/releases/download/v1.1.3/mainnet.zip

```toml
[Eth]
NetworkId = 1024
NoPruning = false
NoPrefetch = false
LightPeers = 100
UltraLightFraction = 75
TrieTimeout = 100000000000
EnablePreimageRecording = false
EWASMInterpreter = ""
EVMInterpreter = ""

[Eth.Miner]
GasFloor = 30000000
GasCeil = 40000000
GasPrice = 1000000000
Recommit = 10000000000
Noverify = false

[Eth.TxPool]
Locals = []
NoLocals = true
Journal = "transactions.rlp"
Rejournal = 3600000000000
PriceLimit = 1000000000
PriceBump = 10
AccountSlots = 512
GlobalSlots = 10000
AccountQueue = 256
GlobalQueue = 5000
Lifetime = 10800000000000

[Eth.GPO]
Blocks = 20
Percentile = 60
OracleThreshold = 20

[Node]
IPCPath = "geth.ipc"
HTTPHost = "0.0.0.0"
NoUSB = true
InsecureUnlockAllowed = false
HTTPPort = 8545
HTTPVirtualHosts = ["*"]
HTTPModules = ["eth", "net", "web3", "txpool", "parlia"]
WSPort = 8546
WSModules = ["net", "web3", "eth"]
WSHost = "0.0.0.0"
WSOrigins = ["*"]


[Node.P2P]
MaxPeers = 30
NoDiscovery = false
ListenAddr = ":30311"
EnableMsgEvents = false

[Node.HTTPTimeouts]
ReadTimeout = 30000000000
WriteTimeout = 30000000000
IdleTimeout = 120000000000

[Node.LogConfig]
FilePath = "bsc.log"
MaxBytesSize = 10485760
Level = "info"
FileRoot = ""
```

> config.toml 文件可创建在/opt/docker/bsc-private

注：本次部署最重要的目的是需要BSC开发网节点支持websocket协议，配置文件中需要注意以下参数：

```yaml
WSPort = 8546                         //指定WebSockets-RPC服务端口，默认值8546
WSModules = ["net", "web3", "eth"]    //设定开放给WebSockets-RPC的接口，默认只开放eth、net、web3
WSHost = "0.0.0.0"                    //指定WebSockets-RPC服务监听地址，默认值localhost
WSOrigins = ["*"]                     //指定WebSockets-RPC服务允许从哪些域过来的跨域请求，*表示接受表示所有的域
```

#### 创建一个owner账户

使用MetaMask或者其他工具预先创建一个账户地址，并保存好私钥，后面这个地址会配置在genesis文件中，并用做挖矿coinbase地址及初始化地址

#### 创建genesis.json

参考: https://github.com/binance-chain/bsc/releases/download/v1.1.3/mainnet.zip

**根据需要修改特定的字段，常规的比如**

- chainId 修改为自己的独有链id
- coinbase 修改为预先创建的地址
- alloc中的efe9fc587858ce20ee391834dc6d58fa6c18278c修改为初始资金接收地址，balance根据业务需要，计算相应预先发行数量（精度18位）的16进制

**对于共识参数，可以根据需要再做修改**

```yaml
"congress": {
    "period": 3, // 出块间隔（秒）
    "epoch": 200 // 出块顺序刷新间隔（块数）,epoch为一个周期设定，单位是block，每个epoch结束的时候，会对验证人进行相应调整；
}
```

**genesis.json 文件创建**

```
config：配置块定义我们的自定义链的设置，并具有创建私有区块链的某些属性。
chainId ：标识我们的区块链，主要的以太坊链有自己的ID，但我们会将它设置为我们私有链的唯一值。 
homesteadBlock：Homestead是以太坊平台的第二个主要版本，也是以太坊的第一个生产版本。它包括几个协议更改。由于我们已经在Homestead版本，因此该属性为0。- eip155Block/eip158Block：Homestead版本发布时带有一些向后不兼容的协议更改，因此需要硬分叉。通过以太坊改进提案（EIPs）提出的这些协议变更/改进。然而，我们的链条不会因为这些变化而难以分解，所以保留为0。 
difficulty：此值用于控制区块链的块生成时间。难度越高，Miner在发现有效块时必须执行的统计更多计算。在我们的测试网络中，我们将保持此值低以避免在测试期间等待，因为需要生成有效块来执行交易处理区块链。 
gasLimit：此值指定每块的“gas”支出的当前链范围限制。gas是以太坊在交易过程中消耗的燃料。我们将在这种情况下将此值标记得足够高，以避免在测试期间受到限制。 
alloc：这是你可以创建你的钱包并用假ether预填充的地方。
```

```json
{
  "config": {
    "chainId": 1024,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "muirGlacierBlock": 0,
    "congress": {
     "period": 2,
      "epoch": 20000
    }
  },     
     "nonce": "0x0000000000000061",
     "timestamp": "0x0",
     "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
     "gasLimit": "0x8000000",
     "difficulty": "0x10",
     "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
     "coinbase": "0x57a8f14369c39379b8c860aca78ca33c641b81c9",
     "alloc": {
       "0x57a8f14369c39379b8c860aca78ca33c641b81c9":{
       "balance": "6660010000000000000000000000"
   	      }	
     }
}
```

> 注：genesis.json 应创建在/opt/docker/bsc-private

#### 初始化geth

将上面修改好的`geth.toml`和`genesis.json`以及预生成的账户地址`keystore`文件放到规划好的位置，我们此时放在 `/opt/docker/bsc-private/`

- 初始化geth

```shell
cd /opt/docker/bsc-private
mkdir -p bsc/keystore
# 将预生成的账户地址keystore文件拷贝至初始数据文件中
cp UTC--* bsc/keystore/
# 初始化geth
docker run --rm -v $(pwd):/data private_bsc:v1.1.3 init /data/genesis.json --datadir /data/bsc
```

或者可以将以上步骤编写为一个脚本，例如：

```shell
# vim /opt/docker/bsc-private/genesis.sh 

#!/bin/bash

cp UTC--* ./bsc/keystore/
docker run --rm -v $(pwd):/data private_bsc:v1.1.3 init /data/genesis.json --datadir /data/bsc
```

#### 启动bsc

win10启动提示socker问题，参照这个[issue](https://github.com/ethereum/go-ethereum/issues/16215)

这里使用vm虚拟机的centos7测试，

```shell
docker run -itd --restart=unless-stopped -v /etc/localtime:/etc/localtime -v /etc/timezone:/etc/timezone --name private-bsc -v /opt/docker/bsc-private/:/data -p 30311:30311 -p 8545:8545 -p 8546:8546 private_bsc:v1.1.2 --config config.toml --datadir /data/bsc  --nodiscover --allow-insecure-unlock --rpccorsdomain "*"
```

出现错误

```shell
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: process_linux.go:545: container init caused: rootfs_linux.go:76: mounting "/etc/timezone" to rootfs at "/etc/timezone" caused: mount through procfd: not a directory: unknown: Are you trying to mount a directory onto a file (or vice-versa)? Check if the specified host path exists and is the expected type.
```

原因是centos7.6中/etc/timezone是一个文件夹,而不是一个文件,执行如下命令

```shell
echo 'Asia/Shanghai' > /etc/timezone/timezone
```

```shell
docker run -itd --restart=unless-stopped -v /etc/localtime:/etc/localtime -v /etc/timezone/timezone:/etc/timezone --name private-bsc -v /opt/docker/bsc-private/:/data -p 30311:30311 -p 8545:8545 -p 8546:8546 private_bsc:v1.1.2 --config config.toml --datadir /data/bsc  --nodiscover --allow-insecure-unlock
```


- 查看bsc启动日志

```shell
# tail -100 /opt/docker/bsc-private/bsc/bsc.log.2021-10-21_10
```

> 注意：此时私链并没有开启挖矿

#### 启动挖矿

启动后，进入ipc，解锁我们预先生成的地址，作为此节点的coinbase

- 从另一个终端进入geth控制台

```shell
# docker exec -it private-bsc bash
root@7bccb40bc0f5:/data# geth attach /data/bsc/geth.ipc
```

- 查看当前的的coinbase地址

```shell
> eth
{
  accounts: ["0x57a8f14369c39379b8c860aca78ca33c641b81c9"],
  blockNumber: 0,
  coinbase: "0x57a8f14369c39379b8c860aca78ca33c641b81c9",
```

accounts和coinbase都为以上步骤预先生成的地址

- 设置该地址为无限期解锁，因为后面需要使用该私钥进行出块签名。0为无限期

```shell
> personal.unlockAccount("0x57a8f14369c39379b8c860aca78ca33c641b81c9","owner",0)
true
```

- 启动挖矿

```shell
>  miner.start(1)
null
```

> 注：开启一个线程进行挖矿

- 此时查看容器日志

```shell
# tail -100 /opt/docker/bsc-private/bsc/bsc.log.2021-10-21_10
// 生成DAG数据
t=2021-10-21T10:32:28+0800 lvl=info msg="Generating DAG in progress"             epoch=0 percentage=9 elapsed=7.342s
t=2021-10-21T10:34:52+0800 lvl=info msg="Generating DAG in progress"             epoch=0 percentage=10 elapsed=8.072s
t=2021-10-21T10:34:52+0800 lvl=info msg="Generating DAG in progress"             epoch=0 percentage=11 elapsed=8.816s

// 开始出块
t=2021-10-21T10:35:58+0800 lvl=info msg="Successfully sealed new block"          number=1 sealhash=0x0f990fcb956860fc1f050205d1aee1aff052f75327a0f1fe82a29b886d259f3b hash=0x8b79131b55f3447c988ac7c361dc0b903666c4dfcbae86bd08aed3f08d453c44 elapsed=1m15.471s
t=2021-10-21T10:35:58+0800 lvl=info msg=" mined potential block"                number=1 hash=0x8b79131b55f3447c988ac7c361dc0b903666c4dfcbae86bd08aed3f08d453c44
t=2021-10-21T10:35:58+0800 lvl=info msg="Commit new mining work"                 number=2 sealhash=0x42546fe2ddf49aacb6a5b87341035e41c55b74145a51fc0a60a364ae91228f15 uncles=0 txs=0 gas=0 elapsed="367.28µs"
```

节点出块主要是以下日志

```shell
// 成功封存新的区块
t=2021-10-21T10:14:58:03+0800 lvl=info msg="Successfully sealed new block"          number=1421 sealhash=0x96c582534f19f90bf0c0a7d035723cbe131968d68c250b3b561c39d4e163543f hash=0x7a983d8db1630bb5d30b906d020e578f82608408150d23e08d41182d6f62f5d5 elapsed=13.696s

// 区块进行上链
t=2021-10-21T10:58:03+0800 lvl=info msg=" block reached canonical chain"        number=1410 hash=0x6cbcd8fa3606cb2d0a5395af3cd0c515eebb8b84ad864a1cf060eea5e852f107

// 挖掘可能的区块
t=2021-10-21T10:58:03+0800 lvl=info msg=" mined potential block"                number=1421 hash=0x7a983d8db1630bb5d30b906d020e578f82608408150d23e08d41182d6f62f5d5


// 进行新的挖矿工作
t=2021-10-21T10:58:03+0800 lvl=info msg="Commit new mining work"                 number=1422 sealhash=0x51bcd69f824944147b113afc8da20a50f7af89e31793969be64
```

#### geth基础命令

```shell
# 基础信息
eth
# 基础矿信息
eth.coinbase
# 区块高度
eth.blockNumber
# 同步状态
eth.syncing
# 查看账户
eth.accounts
# 创建账户
personal.newAccount("your password")
# 查看用户余额
eth.getBalance(eth.accounts[0])
web3.fromWei(eth.getBalance(eth.accounts[0]))
# 开始挖矿
miner.start()
# 停止挖矿
miner.stop()
# 导入导出节点
geth export filename
geth import filename
# 解锁账户
# 默认假设这个账户里有余额，想要给其他账户转账，先要进行账户解锁，最后一位的60是解锁时间，单位是秒，不填的话默认300，可以根据实际需要填写时间值
personal.unlockAccount(eth.accounts[0],"123456",60);
# 转账
# 转账操作，转账1个eth，会消耗一定量的旷工费，测试多次结果为，旷工费是随机的，目前测试发现这种方式消耗旷工费很少，如果执行成功的话，会返回一串hash值，交易id，根据此id可以去区块浏览器中查询，查询时可能会发现，有的时候回出现pending状态，可能需要等待一段时间才会成功，也可能失败，失败的话，消耗的旷工费就不返还了
eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:web3.toWei(1,"ether")});
# 查询交易
eth.getTransaction("交易id");
```

#### rpc命令

当前燃气价格

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_gasPrice","params":[],"id":73}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":73,"result":"0x0"}
```

返回客户端拥有的地址列表

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":["0x2770212031615c2fe2e0a9d3ae40461f85c5ee61","0x6aa0d82340b93e2e24d8e075c843e752f5bb4422"]}
```

返回给定地址的帐户的余额 参数地址

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x2770212031615c2fe2e0a9d3ae40461f85c5ee61", "latest"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0x12aa4bba8112af40000"}
```

 转账

```
# curl -H Content-Type:application/json -X POST --data '{"jsonrpc":"2.0","method":"eth_sendTransaction", "params":[{"from":"0x2770212031615c2fe2e0a9d3ae40461f85c5ee61","to":"0x6aa0d82340b93e2e24d8e075c843e752f5bb4422","gas":"0x5208","gasPrice":"0x1264c45600","value":"0xde0b6b3a7640000"}],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0xa9fe4dd246c7cd7bb4deb6ac2a2274d03f0a71016ba313a72df7af3aba58ab57"}
```

根据交易哈希查询交易信息

```
# curl -H Content-Type:application/json -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xa9fe4dd246c7cd7bb4deb6ac2a2274d03f0a71016ba313a72df7af3aba58ab57"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":{"blockHash":"0xbda7dcb2a96bdb184b00e063b0b4cccbe0237dac50863760da18ea2e97878279","blockNumber":"0x517","from":"0x2770212031615c2fe2e0a9d3ae40461f85c5ee61","gas":"0x5208","gasPrice":"0x1264c45600","hash":"0xa9fe4dd246c7cd7bb4deb6ac2a2274d03f0a71016ba313a72df7af3aba58ab57","input":"0x","nonce":"0x1","to":"0x6aa0d82340b93e2e24d8e075c843e752f5bb4422","transactionIndex":"0x0","value":"0xde0b6b3a7640000","v":"0x823","r":"0x1e0f7ff1eaf10244475c7f136a4201532a1aa09ca3aa185beff4d8f8214239d","s":"0x6cdd9e6a7ff19ce51f71426a4efa182a2c8c01d7ca16f644aaa63b673f13b96f"}}
```

从这个地址发送的交易数量的整数

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionCount","params":["0x2770212031615c2fe2e0a9d3ae40461f85c5ee61", "latest"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0x1"}
```

查询最新区块高度

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0x46e"}
```

根据区块高度获取区块信息

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x46e", true],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":{"difficulty":"0x349dc","extraData":"0xd883010007846765746888676f312e31352e35856c696e7578","gasLimit":"0x7a1200","gasUsed":"0x0","hash":"0xd8b464d9cf8e28eeed6042480b54d07ad3425e6c1006873b2aedb022bd777392","logsBloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000","miner":"0x2770212031615c2fe2e0a9d3ae40461f85c5ee61","mixHash":"0x0306855db6701a71b56e6f4f6c67348748a1520c8e2bbaf80bbb95c3cb1e61c8","nonce":"0x6e859dfc02c48678","number":"0x46e","parentHash":"0x3dc65f09bf60a16fa54cc2e44b03ad2ff1a528760aa8be4f022f19a9e6bd4785","receiptsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","sha3Uncles":"0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347","size":"0x21a","stateRoot":"0xcf51398874cb0184bd9a4c7e4cc07e77ff75f562f1abf703c98af5d46ed02159","timestamp":"0x608507b6","totalDifficulty":"0xb820f5f","transactions":[],"transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","uncles":[]}}
```

根据区块哈希获取区块信息

```
# curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByHash","params":["0xd8b464d9cf8e28eeed6042480b54d07ad3425e6c1006873b2aedb022bd777392", true],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":{"difficulty":"0x349dc","extraData":"0xd883010007846765746888676f312e31352e35856c696e7578","gasLimit":"0x7a1200","gasUsed":"0x0","hash":"0xd8b464d9cf8e28eeed6042480b54d07ad3425e6c1006873b2aedb022bd777392","logsBloom":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000","miner":"0x2770212031615c2fe2e0a9d3ae40461f85c5ee61","mixHash":"0x0306855db6701a71b56e6f4f6c67348748a1520c8e2bbaf80bbb95c3cb1e61c8","nonce":"0x6e859dfc02c48678","number":"0x46e","parentHash":"0x3dc65f09bf60a16fa54cc2e44b03ad2ff1a528760aa8be4f022f19a9e6bd4785","receiptsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","sha3Uncles":"0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347","size":"0x21a","stateRoot":"0xcf51398874cb0184bd9a4c7e4cc07e77ff75f562f1abf703c98af5d46ed02159","timestamp":"0x608507b6","totalDifficulty":"0xb820f5f","transactions":[],"transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421","uncles":[]}}
```

根据区块哈希查询当前区块里的交易数

```
# curl -H Content-Type:application/json -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByHash","params":["0xbda7dcb2a96bdb184b00e063b0b4cccbe0237dac50863760da18ea2e97878279"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0x1"}
```

根据区块高度查询当前区块里的交易数

```
# curl -H Content-Type:application/json -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByNumber","params":["0x517"],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0x1"}
```

获取hashrate

```
# curl -H Content-Type:application/json -X POST --data '{"jsonrpc":"2.0","method":"eth_hashrate","params":[],"id":1}' http://127.0.0.1:8545
{"jsonrpc":"2.0","id":1,"result":"0xa775"}
```



### WS协议验证

**查看websocket协议端口是否启动**

- 安装netstat工具

```
docker exec -it private-bsc bash  //进入容器
apt-get update                    //更新系统
apt-get install net-tools         //安装网络工具
```

- 查看容器内websocket协议端口8546是否启动

```
# netstat -lnptu
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp6       0      0 :::8545                 :::*                    LISTEN      1/geth          
tcp6       0      0 :::8546                 :::*                    LISTEN      1/geth          
tcp6       0      0 :::30311                :::*                    LISTEN      1/geth
```

**测试websocket协议是否可用**

使用postman模拟发送一个websocket请求，查询区块高度，查看是否可正常返回

## 相关链接

[MetaMask](https://metamask.io/)

[docs.binance.org](https://docs.binance.org/smart-chain/developer)

[hardhat](https://hardhat.org/)

[openzeppelin](https://docs.openzeppelin.com/openzeppelin/)

[oneclickdapp](https://oneclickdapp.com/)

[web3jdoc](https://docs.web3j.io/4.8.7/)

