# eth

## 1 Polygon
### 1.1 介绍
+ 基于pos的以太坊侧链技术框架
+ MATIC代币
+ 印度，19年开始

github: https://github.com/maticnetwork/  




## 2 以太坊
### 2.1 分片与Layer2
1 解决以太坊交易性能问题  
以太坊分片是在以太坊内部进行扩容的性能解决方案，而Layer 2则是在以太坊区块链外部进行扩容的方案  

参考：https://zhuanlan.zhihu.com/p/342753841  
### 2.2 Layer1与Layer2
以太坊公链就是Layer1
Layer2相关技术：
+ plasma 返回交易结果
+ rollup 同时返回交易结果和交易的信息（压缩）
    - 错误性证明（Fraud Proof）
+ ZK-rollup 零知识证明
    - 有效性证明（Validity Proof）

代表性的layer2:
+ Arbitrum
+ Optimism
+ Polygon（项目选型）
+ zkSync

#### 2.2.1 layer2出现的目的  
太坊公链的工作效率低下，把一些以太坊上的交易移到Layer 2上去处理，处理完成以后再把结果返回给Layer 1，这样layer 1就没这么忙了。  

## 3 Solana链
### 3.1 介绍  
+ 2017年瑞士  
+ 公链
+ sol代币
+ defi项目多

## 4 支付
### 4.1 cpay
https://www.cpay.finance/


## 5 nft
### 5.1 opensea
nft交易平台

nft: 无聊猿  
https://ipfs.io/ipfs/QmRRPWG96cmgTn2qSzjwr2qvfNEuhunv6FNeMFGa9bx6mQ  


## 6 区块链知识
### 6.1 MEV（侧重收益）
MEV（最大交易价值）是指在区块链上的交易顺序可以对参与者产生经济影响的现象

概念参考文章：
https://learnblockchain.cn/article/5810  
https://www.bitpush.news/articles/4037081  mev攻击与解决方案  

https://www.techflowpost.com/article/detail_12034.html  suave工作原理  


### 6.2 rollup（侧重吞吐与效率）

基于Layer2进行扩容，进行数据扩容和降低了gas费用  

l2项目排名网站：https://l2beat.com/  


#### 6.2.1 ZK-Rollup零知识证明

简单理解就是在链上发布一个proof，即验证椭圆曲线的一个点  
eg:  
  1000个交易利用零知识证明精简为一条交易在链上执行


+ zkevm
  - 没有zkevm时，每个个合约都需要生成一个证明去证明自己的不能跨合约，
  zkevm所有的都可以放在zkevm和evm一样可以执行所有的  
![img.png](images/zkevm比较.png)


##### 6.2.1.1 项目：  
+ https://github.com/0xPolygonHermez/zkevm-node（polygon zkevm）

#### 6.2.2 Optimistic Rollup乐观

简单理解编译成合约字节码，验证部分内容

+ Arbitrum 
介绍：https://huobiresearch.medium.com/%E4%B8%80%E6%96%87%E8%AF%BB%E6%87%82%E4%BB%A5%E5%A4%AA%E5%9D%8A%E4%BA%8C%E5%B1%82%E9%A1%B9%E7%9B%AEarbitrum-d9fdc658b1f3  

https://learnblockchain.cn/article/2665  


#### 6.2.3 零知识证明
+ Groth16 
+ Plonk
+ ZK-SNARKs
+ Halo2



## 7 跨链
### 7.1 cosmos-sdk
底层Tendermint，最新切换到cometbft

### 7.2 项目
+ https://github.com/evmos/ethermint  （cosmos+evm实现）


## 8 sdk中用到的加密知识  
### 8.1 加密算法  
非对称加密：ed25519 sm2  
对称加密：AES  
原理：  
国密签名和验签：https://learnblockchain.cn/article/1507  
库：  
github.com/ZZMarquis/gm  国密库，签名实际为r+ s 64个字节长度  
crypto/ed25519     标准库生成的私钥实际为私钥+公钥   私钥和公钥都为32字节长度   

### 8.2 hash算法
SM3 sha256  

### 8.3 助记词
bip32 bip44 bip39  
原理：
https://learnblockchain.cn/2018/09/28/hdwallet/#%E5%8A%A9%E8%AE%B0%E8%AF%8D%E6%8E%A8%E5%AF%BC%E5%87%BA%E7%A7%8D%E5%AD%90
库：  
github.com/tyler-smith/go-bip32  
github.com/tyler-smith/go-bip39  



## 9 账户抽象
### 9.1 aa
+ https://github.com/zerodevapp/kernel
+ https://github.com/zerodevapp/sdk
+ https://github.com/eth-infinitism/account-abstraction



## 10 eth相关的链
### 10.1 polygon
+ 水龙头：https://faucet.polygon.technology/
+ 浏览器：https://testnet-zkevm.polygonscan.com/tx/0x4a56eac3a326341429d3323cad9bbba9139f4d8fdf6974192cdd27ffd8a222b0
+ rpc: https://rpc.public.zkevm-test.net  Chain ID: 1442  Currency symbol: ETH


### 10.2 goerli
+ 浏览器: https://goerli.etherscan.io/



### 10.3 eth相关工具
+ 合约input解析在线 https://lab.miguelmota.com/ethereum-input-data-decoder/example/


### 10.2 zkevm
+ golang node: https://github.com/0xPolygonHermez/zkevm-node

#### 10.2.1 polygon zkevm执行流程
+ 视频教程：https://www.youtube.com/watch?v=VfoP9QiP-xY
1. sequencer将多个用户的交易打包成batch提交到l1合约上--> 在合约的calldata中
2. prover为每笔交易生成有效性证明(validity proof),并将多个交易的有效性证明聚合成一个有效性证明
3. Aggregator提交聚合了多个交易的有效性证明(validity proof)到l1合约中


## 11. eth基础
### 11.1. 基础
+ Gas Price：如果 Gas Price 设置得过低，矿工可能不愿意优先打包你的交易
+ Gas Limit：如果 Gas Limit 设置得过低，无法满足执行智能合约所需的计算资源，交易将在执行过程中耗尽 Gas，并最终失败
交易手续费 = gas * gaslimit  


## 12. nft
+ erc721
+ erc1155


## 13. 协议
+ eip-1559 早期的gas
+ eip-4844 最新的gas


## staking
+ pos(Proof of stake) 权益证明
+ lsd(liquid staking dericatives) 流动性质押
+ staking/restaking
+ avs(actively validated services) 主动验证服务

## 学习资料
+ 区块链相关视频教程 https://github.com/biquanlibai/blockchain-course

## 节点工具
+ infura.io --> 提供开发的api
+ thegraph.com --> 检索和查询以太坊的区块链
+ nft.storage --> 免费使用ipfs
+ alchemy.com --> 开发工具， 比如polygon-rpc节点
+ vercel.com --> 在线托管你的程序
<<<<<<< HEAD
+ netlify.app --> 在线静态资源托管
=======
+ chainlist.org --> 链节点
>>>>>>> 0ba8aff8529117353aab59cccadf4a048768e433

## ipfs相关的uri
https://ipfs.io/ipfs/bafyreigyhfclajiatueanhu5pexpwyakpogltngsgplfrak54zuzdhydcq/metadata.json
https://ipfs.io/ipfs/QmeSjSinHpPnmXmspMjwiXyN6zS4E9zccariGR3jxcaWtq/
https://ipfs.io/ipfs/QmTecK6aZLBteHcx7zP7jCgWELFwkPPgF4aWBJmB7RJnDg/


### js 查询事件
 ```text
     const events = await instance.queryFilter(instance.filters.DepositEventV1(null, null));
    const eventHash = events[0].topics[0];
    console.log("Deposit Event hash:", eventHash);
```

 // 根据块区间查询
 ```text
     // SimpleBridge
    const SimpleBridge = await ethers.getContractFactory("Locker");
    const instance = await SimpleBridge.attach(bridgeAddress);

    // let tx = await instance.release("0x756A6aa43547fA8cCF02ab417E6c4c4747137346", [27]);;
    // const res = await tx.wait(1);

    const filter = {
        address: bridgeAddress,
        fromBlock: 20614155,
        toBlock: 20614155,
        topics: instance.filters.releaseEvent(null, null).topics
    };
    const provider = ethers.provider;
    const logs = await provider.getLogs(filter);

    // 解析日志数据
    const parsedLogs = logs.map(log => instance.interface.parseLog(log));

    // 输出解析后的日志数据
    console.log("Release Events in block:", parsedLogs);

 ```
 


## 资料
+ 合约调用log: https://www.cnblogs.com/zhanchenjin/p/17851436.html