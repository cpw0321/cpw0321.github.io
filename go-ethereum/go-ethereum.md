# go-ethereum

## 一、源码的目录结构

``` text
|---accounts 以太坊账户管理
|---bmt 二进制Merkle-Patricia Trie的实现
|---build 主要是编译和构建的一些脚本和配置
|---cmd 命令行工具
| |---abigen 合约接口生成工具
| |---bootnode 实现网络发现的节点
| |---evm 以太坊虚拟机
| |---faucet
| |---geth 以太坊命令行客户端
| |---p2psim 提供了一个工具来模拟http的API
| |---puppeth 创建一个新的以太坊网络的向导
| |---rlpdump 提供RLP数据的格式化输出
| |---swarm swarm网络的接入点
| |---util 公共的组件
| |---wnode 这是一个简单的Whisper节点。 它可以用作独立的引导节点。此外，可以用于不同的测试和诊断目的。
|---common 提供了一些公共的工具类
|---consensus 以太坊的共识算法，包括ethhash, clique
|---core 以太坊的核心数据结构和算法(虚拟机，状态，区块链，布隆过滤器)
|---crypto 加密,数字签名和hash算法
|---eth 实现了以太坊的协议
|---ethclient 以太坊的RPC客户端
|---ethdb eth的数据库,主要是LevelDB及相应接口
|---event 实时事件处理
|---light 实现为以太坊轻量级客户端提供按需检索的功能
|---metrics 提供磁盘计数器
|---miner 以太坊的挖矿和共识算法
|---mobile 移动端使用的一些warpper
|---node 以太坊的多种类型的节点
|---p2p 以太坊p2p网络协议
|---rlp 以太坊序列化和反序列化处理
|---rpc 远程方法调用
|---swarm swarm网络处理
|---trie 以太坊重要的数据结构MPT的实现
|---whisper whisper节点的协议
```

## 1. 基础知识
### 1.1. 单位

|单位	    |Wei 值	    |Wei    |
| --------- | --------- | -------- |  
|wei	    |1wei	    |1   |
|Kwei	    |1e3wei	    |1,000  |
|Mwei	    |1e6wei	    |1,000,000  |
|Gwei	    |1e9wei	    |1,000,000,000 | 
|microEther	|1e12wei	|1,000,000,000,000  |
|milliEther	|1e15wei	|1,000,000,000,000,000 | 
|Ether	    |1e18wei	|1,000,000,000,000,000,000 | 

交易手续费 = gas * gaslimit

### 1.2 makefile

伪目标
```text
.PHONY: clean
```
作用：  
1、为了避免在makefile中定义的只执行命令的目标和工作目录下的实际文件出现名字冲突  
2、提交执行makefile时的效率


### 1.3 编译geth
```shell
make geth
```

### 1.4 运行geth
```shell
# 查看版本
./build/bin/geth version
```

--datadir 数据目录
--netwowrk 链chainId
console 控制台交互

### 1.5搭建私链教程
搭建dev， dev直接运行就可以，不需要genesis.json和init操作
https://geth.ethereum.org/docs/developers/dapp-developer/dev-mode 

1、初始化文件
genesis.json
```text
{
  "config": {
    "chainId": 12345,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "berlinBlock": 0,
    "clique": {
      "period": 5,
      "epoch": 30000
    }
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "extradata": "0x000000000000000000000000000000000000000000000000000000000000000022220a7c9510cd94e7cae9cd569de40c8b1955670000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "alloc": {
    "22220a7c9510cd94e7cae9cd569de40c8b195567": { "balance": "300000000000000000000000" }
  }
}
```

extradata // 这里面包含signer地址


2、初始化命令
```shell
./build/bin/geth --datadir ./data init genesis.json
```

3、启动链
```shell
# dev模式启动
./build/bin/geth --dev --http --http.port 8545 --http.addr 0.0.0.0 --http.api personal,eth,net,web3,txpool,debug --http.corsdomain '*'  --ws --ws.addr 0.0.0.0 --ws.port 8546 console 2>geth.log

```

4、常用命令
```shell
# 查看chainID
net.version  // chainID不对，部署合约会报错Served eth_sendRawTransaction err="invalid sender

#新建账户
personal.newAccount()  // 废弃了 --rpc.enabledeprecatedpersonal 可以开启
./build/bin/geth --datadir ./data account new   // 创建账户密码为空就行

#查看账户
eth.accouts

#查看余额
eth.getBalance(eth.accounts[0])
eth.getBalance(eth.coinbase)/1e18

#设置旷工地址
miner.setEtherbase("0x255843d997dfffb130bdfe73d2c34322f4500623")

#查看旷工
eth.coinbase

#转账
eth.sendTransaction({from: eth.coinbase, to: "0x22220a7C9510cd94e7cAE9CD569dE40C8B195567", value: web3.toWei(50, "ether")})

```

### 1.6. 搭建浏览器
#### 1.6.1. blockscout


