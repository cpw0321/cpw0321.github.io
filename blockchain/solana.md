# solana

## 项目
+ blink



项目：
+ sonic 游戏公链 layer2基于zk-rollup 已融资
+ io.net   GPU+AI(Depin龙头) 闲置的gpu赋能人工智能的计算

文章：
+ https://www.aicoin.com/zh-Hans/article/404231  全景探讨Solana生态发展：L2和应用链影响几何




## 1.1. 基础
### 1.1.1. account

1sol = 10^9 lamports

+ 钱包: Phantom


### 1.1.2. 智能合约

+ https://beta.solpg.io/ --> remix


### poh

pos\pow 节点之间需要同步时间，单个节点是基于本地生成交易时间戳的

poh 用的全局时钟  交易顺序为 前一个交易hash生成hash, 一条连续的hash来保证交易的顺序性
slot 为400ms,即出块时间

‌PoH 链结构‌为每个 Slot 独立生成一条链，全网存在多条链，但通过 Slot 机制实现全局时间一致性



#### 并行
PoH 的‘时间排序’和‘交易执行’是两个分离的阶段

[收交易] 
   ↓
[PoH 打时间戳] → 形成 H(T1), H(T2), H(T3)... （串行）
   ↓
[Sealevel 分析依赖]
   ↓
[并行执行] ──┬─► Core1: T2
             ├─► Core2: T4  
             └─► Core3: T1 → T3（串行）
   ↓
[统一提交状态]
   ↓
[广播区块]


🔹 算 hash（PoH）只是确定交易发生的物理时间顺序，它本身不执行交易逻辑。

🔹 执行交易是由另一个模块（Sealevel）完成的，它利用多核 CPU 并行处理那些不争抢同一账户的交易。

🔹 所以：“定序”是串行的，“执行”是可以并行的 —— 两者不矛盾。


#### Slot
Slot（时隙）是 Solana 网络中固定的时间窗口，每个 slot 理论上可以产生一个新区块。

每个 slot 持续 400 毫秒（ms）


让全球节点能在下一个 slot 开始前收到上一个区块



参考：
+ 教程：https://learnblockchain.cn/column/53
+ solana相关概念的理解视频：https://www.bilibili.com/video/BV1Nu411y7NE/?spm_id_from=333.788&vd_source=9a6776332c8894a5253390cd88bdf876
