# 区块链

## 1、Polygon
### 1.1、介绍
+ 基于pos的以太坊侧链技术框架
+ MATIC代币
+ 印度，19年开始

github: https://github.com/maticnetwork/  




## 2、以太坊
### 2.1、分片与Layer2
1、解决以太坊交易性能问题  
以太坊分片是在以太坊内部进行扩容的性能解决方案，而Layer 2则是在以太坊区块链外部进行扩容的方案  

参考：https://zhuanlan.zhihu.com/p/342753841  
### 2.2、Layer1与Layer2
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
+ Polygon
+ zkSync

#### 2.2.1、layer2出现的目的  
太坊公链的工作效率低下，把一些以太坊上的交易移到Layer 2上去处理，处理完成以后再把结果返回给Layer 1，这样layer 1就没这么忙了。  

## 3、Solana链
### 3.1、介绍  
+ 2017年瑞士  
+ 公链
+ sol代币
+ defi项目多

## 4、支付
### 4.1 cpay
https://www.cpay.finance/


## 5、nft
### 5.1 opensea
nft交易平台

nft: 无聊猿  
https://ipfs.io/ipfs/QmRRPWG96cmgTn2qSzjwr2qvfNEuhunv6FNeMFGa9bx6mQ  


## 6、区块链知识
### 6.1、MEV（侧重收益）
MEV（最大交易价值）是指在区块链上的交易顺序可以对参与者产生经济影响的现象

概念参考文章：
https://learnblockchain.cn/article/5810  
https://www.bitpush.news/articles/4037081  mev攻击与解决方案  

https://www.techflowpost.com/article/detail_12034.html  suave工作原理  


### 6.2、rollup（侧重吞吐与效率）

基于Layer2进行扩容，进行数据扩容和降低了gas费用

#### 6.2.1、ZK-Rollup零知识证明


#### 6.2.2、Optimistic Rollup乐观