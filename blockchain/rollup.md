## 二层
+ Optimistic Rollup 
    - Optimism
    - Arbitrum
  采用“乐观”假设，默认交易有效，除非有人提交欺诈证明挑战。依赖经济激励（质押惩罚）来防止作恶
+ ZK Rollup 
    - zkSync
    - StarkNet
    - Scroll
  基于密码学证明（零知识证明），交易有效性由数学保证，无需信任任何参与者


# 交易撮合系统的各项核心模块（如订单匹配、撮合算法、订单簿管理等）进行性能调优和架构优化
动性池算法‌：研究AMM（自动做市商）模型，优化滑点控制和流动性激励策略‌

## uniswap

+ V2 的恒定乘积模型 (CPMM)
  x * y = k 
+ ‌V3 的集中流动性模型
  给定一个价格区间，不会无穷大或者无穷小

## Polkadot 异构多链
+ 中继链（Relay Chain）
+ 平行链（Parachain）
+ 转接桥（Bridge）


# roullup
+ Optimistic Rollup 
    - Optimism
    - Arbitrum
  采用“乐观”假设，默认交易有效，除非有人提交欺诈证明挑战。依赖经济激励（质押惩罚）来防止作恶
+ ZK Rollup 
    - zkSync
    - StarkNet
    - Scroll
  基于密码学证明（零知识证明），交易有效性由数学保证，无需信任任何参与者 二层扩容方案如何


## zkevm
-   **Optimistic Rollup**（如 Arbitrum、Optimism）：默认交易有效，依赖“挑战期”和经济惩罚来保证安全。
-   **ZK Rollup**（如 zkSync、StarkNet）：用数学证明（零知识证明）确保交易正确，无需信任任何人。
-   **zkEVM = ZK Rollup + EVM 兼容性**

### 普通 ZK Rollup与zkEVM区别
    -   普通 ZK Rollup（如早期的 zkSync 1.0、StarkEx）只支持简单交易（如转账），**不能直接运行以太坊智能合约**。
    -   **zkEVM 的目标是让 ZK Rollup 也能完全支持以太坊的智能合约**，让开发者无缝迁移 DApp。

### **zkEVM vs 普通 ZK Rollup**

| **对比维度**   | **普通 ZK Rollup**         | **zkEVM**                       |
| ---------- | ------------------------ | ------------------------------- |
| **兼容性**    | 仅支持特定操作（如转账、Swap）        | **完全兼容 EVM**，可运行任意智能合约          |
| **开发体验**   | 需用专用语言（如 Cairo、Zinc）     | 直接用 Solidity/Vyper，无需重写代码       |
| **证明生成难度** | 较简单（定制电路优化）              | 极复杂（需模拟 EVM 所有操作）               |
| **代表项目**   | StarkEx（dYdX）、zkSync 1.0 | Scroll、zkSync Era、Polygon zkEVM |

* * *

### **为什么 zkEVM 更难实现？**

1.  **EVM 的复杂性**

    -   以太坊虚拟机（EVM）有几百种操作码（如 `CALL`、`SSTORE`），每个都要用零知识证明电路实现，**数学证明的生成成本极高**。
    -   举例：普通 ZK Rollup 只需证明“A 转 1 ETH 给 B”，而 zkEVM 要证明“Uniswap 的整个交易逻辑正确”。

1.  **性能与成本的权衡**

    -   **普通 ZK Rollup**：专为特定场景优化（如支付），证明生成快且便宜。
    -   **zkEVM**：通用计算导致证明时间更长，Gas 费可能更高（尽管仍比主链便宜）。

* * *

### **zkEVM 的不同类型（兼容性分级）**

1.  **完全等效 zkEVM**（Type 1）

    -   100% 兼容以太坊，不修改 EVM（如 Scroll）。
    -   **优点**：开发者零改动。
    -   **缺点**：证明速度最慢。

1.  **高度兼容 zkEVM**（Type 2）

    -   基本兼容 EVM，但微调部分设计（如 Polygon zkEVM）。
    -   **平衡点**：证明效率提升，兼容性仍接近 100%。

1.  **部分兼容 zkEVM**（Type 3）

    -   支持大部分 Solidity，但某些操作需适配（早期 zkSync Era）。
    -   **优点**：更快落地，**缺点**：需少量代码调整。

* * *

### **zkEVM 的潜在问题**

1.  **证明生成速度**

    -   复杂合约的 ZK 证明可能需要几十秒，影响用户体验（对比 Optimistic Rollup 的即时预确认）。

1.  **硬件成本**

    -   生成 ZK 证明需要高性能服务器，可能导致节点中心化（普通电脑跑不动）。

1.  **Gas 费不一定最低**

    -   虽然比主链便宜，但可能比 Optimistic Rollup 更贵（因为证明计算成本高）。

* * *

### **总结：如何选择？**

-   **用 zkEVM 如果：**

    -   你需要 **完全兼容以太坊**，不想改代码。
    -   重视 **即时最终性**（不想等 7 天挑战期）。
    -   应用涉及 **高价值资产**（ZK 安全性更高）。

-   **用 Optimistic Rollup 如果：**

    -   追求 **更低费用** 和 **成熟生态**（如 Arbitrum 上的 DeFi）。
    -   能接受 **7 天提款延迟**（跨链桥可缓解）。

-   **用普通 ZK Rollup 如果：**

    -   你只需要 **支付或简单交易**（如 StarkEx 的 dYdX）。

**zkEVM 是未来，但 Optimistic Rollup 仍是当前最实用的选择。**

---

# 交易撮合系统的各项核心模块（如订单匹配、撮合算法、订单簿管理等）进行性能调优和架构优化
动性池算法‌：研究AMM（自动做市商）模型，优化滑点控制和流动性激励策略‌

## uniswap

+ V2 的恒定乘积模型 (CPMM)
  x * y = k 
+ ‌V3 的集中流动性模型
  给定一个价格区间，不会无穷大或者无穷小

## Polkadot 异构多链
+ 中继链（Relay Chain）
+ 平行链（Parachain）
+ 转接桥（Bridge）


## 工厂合约

## call与delegatecall

## uups

## mpc
+ 库 (需验证)
  "github.com/lsils/gmpc"
    "github.com/hashicorp/vault/shamir"

## 跨链
+ 中心化
  btc-eth 都是利用批处理来提高速度
  mpc --私钥分片和门限来提交安全性

+ 分片技术--待学习
  - ‌哈希取模‌：对用户地址哈希值取模，确定归属分片（如hash(user_address) % 8将用户分配至8个分片之一）‌

## bybit攻击原理

delegatecall
通过 SSTORE 指令将 Safe 合约的 Slot 0（存储实现合约地址的槽位）替换为攻击者控制的地址
```solidity
// 恶意合约示例  
contract Malicious {  
    function hijack() external {  
        // 修改 Slot 0 的存储值  
        assembly {  
            sstore(0, 0xAttackContractAddress)  
        }  
    }  
}  
```
如何避免
+ 对多签交易进行校验


## 加密

+ Schnorr 签名算法 是一种基于离散对数难题的 ‌数字签名与零知识证明协议
  在不泄露私钥的前提下，证明持有者拥有私钥

+ rsa 加密算法
+ ecc 椭圆加密算法
  - Ed25519 签名算法  ---EdDSA‌
  - secp256k1 加密算法 ---ECDSA‌

+ 加密 数据传输加密
+ 签名 身份认证

## 钱包相关
+ bip32 根据一个随机数种子可以生成一个主私钥+多个子私钥
+ bip44 路径用于确定私钥 m / purpose' / coin' / account' / change / address_index
+ bip39 助记词  12/24个单词

## solana
1sol = 10^9 lamports
1eth = 10^18 wei
1gwei = 10^9 wei
1btc = 10^8 satoshi

