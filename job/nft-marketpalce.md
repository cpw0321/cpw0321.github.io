# nft-marketpalce

## 基础概念
+ ask 卖方愿意出售证券的价格  
+ bid 是买方愿意购买证券的价格  
在交易市场中，ask价格通常高于bid价格

## 链上撮合交易
# 两种后端代发模式的详细拆解


一句话总结：
- 模式A把“资产托管”换“后端代发”；
- 模式B用“链下签名”换“后端代发”，资产不动。


## 1.1. start
```bash
mkdir nftmarketpalce
cd nftmarketpalce
npx create-next-app .

npm install react-icons

```


## 参考
+ 测试用例: https://github.com/0xabdou/nft-marketplace/blob/main/test/index.ts
+ https://github.com/daulathussain
+ 合约: https://github.com/0xabdou/nft-marketplace
+ js-api: https://github.com/daulathussain/api-part-0ne



前端：
+ react
+ next.js
+ axios
+ ipfs


隔离见证的优势：
1、区块大小从1M-->4M

ask: 卖价，卖家出售价格
bid: 买价，购买者愿意支付的价格


1、熟悉btc utxo模型以及p2pkh、p2sh、p2wpkh、p2wsh、p2tr(ordinals铭文)脚本

项目：bridge跨链桥
项目介绍：实现btc和eth交互，通过solidity合约实现deposit和withdraw功能，部署在evm中，监听
btc多签账户调用合约deposit实现btc跨链到eth，监听合约withdraw event进行扫块实现eth到btc的跨链

语言：golang、solidty
项目中注意点：
1、合约采用uups，可以实现合约的升级，合约出现问题可以及时升级修复
2、才用p2wsh多签账户(n-m)提高账户的安全性


项目：nftmarketplace
项目介绍：实现nft(erc721)的发售、集合展示、nft的ask和bid功能

语言：golang、solidity、reactjs
项目中注意点：


