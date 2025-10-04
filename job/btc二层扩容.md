# btc二层扩容

实现了一个基于 zkEVM 的比特币二层扩容网络。该系统利用零知识证明（zkProof）和以太坊 L1 合约进行状态验证，同时将关键状态 root 与交易索引锚定到 BTC 主网，确保跨链数据不可篡改与可审计性


优化点：
1、在l1监听event，利用zkevmbridge合约
- L2 事件未经验证，不可信
- Sequencer 可能作恶或宕机
- 只适合“预提现”或“前端展示”，不能作为“打款依据”
2、btc转账利用mcp

## 整体图
```
flowchart TD
    subgraph L2 [Layer 2 网络]
        A[L2 zkEVM Node<br/>Sequencer/Executor]
        B[Aggregator<br/>Relayer/Queue]
        C[Prover Service<br/>zk-proof generation]
    end

    subgraph L1 [Layer 1 结算层]
        D[ETH L1 Verifier<br/>Solidity Contract]
    end

    subgraph BTC [BTC 主网锚定]
        E[Relayer Service]
        F[BTC Mainnet Block]
    end

    A -->|用户交易/状态更新| A
    A -->|Batch 42<br/>txs: 100, prev_root: R41, new_root: R42| B
    B -->|Batch + metadata| C
    C -->|zkProof<br/>proofData, pubInputs, metadata| B
    B -->|submitProof| D
    D -->|verifyProof| D
    D -->|StateUpdated event| D
    D -->|验证成功信号| E
    E -->|write OP_RETURN| F
    
    %% 数据流样式
    linkStyle 0 stroke:green,stroke-width:2px
    linkStyle 1 stroke:blue,stroke-width:2px
    linkStyle 2 stroke:purple,stroke-width:2px
    linkStyle 3 stroke:red,stroke-width:2px
    linkStyle 4 stroke:orange,stroke-width:2px
    linkStyle 5 stroke:brown,stroke-width:2px
```

## 时序图
```
sequenceDiagram
    participant U as User
    participant S as Sequencer
    participant A as Aggregator
    participant P as Prover
    participant V as L1 Verifier
    participant R as Relayer
    participant B as BTC Network

    U->>S: 发起交易
    S->>A: 提交 Batch
    A->>P: 请求生成 zkProof
    P-->>A: 返回 zkProof
    A->>V: submitProof()
    V-->>A: 验证成功，更新状态根
    V->>R: emit StateUpdated
    R->>B: OP_RETURN(root, batchID, eth_tx)
    B-->>R: BTC 区块确认
```

## 项目重点
- OP_RETURN：用于在比特币链上写入少量元数据（例如状态根哈希、proof 的引用）。
- SPV 验证：轻客户端可以通过 SPV（简化支付验证）机制验证某个 proof 是否已被确认在 BTC 链上。


术语	解释
old_root	上一个已被确认的全局状态“指纹”
new_state_root	执行一批交易后，新的全局状态“指纹”
zkProof	数学证明：从 old_root 出发，正确执行交易，必然得到 new_state_root
Verifier 合约	在 L1 上检查这个证明是否成立



项目	new_state_root	proof（zkProof）
本质	一个哈希值（例如：0xabc123...）	一串数学证明数据（几百字节到几KB）
作用	表示 L2 执行一批交易后的新状态“指纹”	证明：从旧状态出发，正确执行交易 → 得到这个新状态
谁生成？	L2 节点（执行完交易后计算 Merkle 树得到）	Prover 模块（专门的证明者，使用 zk-SNARK/STARK 算法生成）
是否可伪造？	可以伪造，但无法通过验证	极难伪造（数学上不可能，除非破解密码学）
在链上做什么？	被写入 L1 合约，作为最新状态锚定点	被发送给 L1 的 Verifier 合约进行验证
验证方式	不需要验证，只是一个值	必须用密码学方法验证其合法性


```
L2 执行交易
    ↓
old_root = H₀
执行交易: Alice → Bob 5 ETH, Charlie → Dave 3 ETH
    ↓
new_state_root = H₁   ← 这是新的状态指纹
    ↓
Prover 生成 zkProof:
   "我知道一组输入（私有），使得：
    f(H₀, transactions) = H₁，且所有规则都遵守"
    ↓
中继器提交到 L1：
   [ new_state_root = H₁, proof = π ]
    ↓
L1 Verifier 合约：
   verify(π, H₀, H₁) → true/false
    ↓
如果 true → 接受 H₁ 为最新官方状态
```


## 语言
+-------------------------------------------------------------+
|                     前端 / 钱包 (Web)                         |
+-------------------------------------------------------------+
                             ↓ ↑
                  HTTP API (JSON/PSBT Base64)
                             ↓ ↑
+-------------------------------------------------------------+
|                 Go 后端服务（门面层）                          |
|  - REST API (Gin/Echo)                                      |
|  - 用户认证、日志、监控                                      |
|  - 调用 Rust 库处理 PSBT / 签名                              |
|  - 监听 BTC & L2 事件                                        |
|  - 广播交易 (RPC)                                           |
|  - 中继器主控逻辑（状态机）                                  |
+----------------------+--------------------------------------+
                       |
         FFI / gRPC / Microservice
                       ↓
+-------------------------------------------------------------+
|                 Rust 内核模块（安全层）                        |
|  - generate_deposit_psbt()                                  |
|  - sign_psbt() / extract_tx()                               |
|  - verify_script() (rust-miniscript)                        |
|  - construct_withdrawal_tx()                                |
|  - validate_op_return()                                     |
|  - SPV 验证（未来）                                          |
+-------------------------------------------------------------+
                             ↓ ↑
                   Bitcoin Mainnet / L2 zkEVM


rust-bitcoin + rust-miniscript + bdk 支持 Taproot、P2WSH、OP_RETURN 等高级功能



+------------------+     +------------------+     +------------------+
|  Bitcoin Network |     |   L2 zkEVM Node   |     |   User Wallet    |
+--------+---------+     +--------+---------+     +--------+---------+
         |                        |                          |
         | 新区块/交易              | 新区块/事件                 | 存款/取款请求
         ↓                        ↓                          ↓
+--------v--------------------------------------------------v---------+
|               Rust BTC Indexer (监听 + 解析)                         |
|  - 使用 bdk 或 bitcoincore-rpc 监听链上事件                          |
|  - 解析交易：提取 OP_RETURN、输出地址、金额                          |
|  - 判断是否为存款交易（发往多签 + 包含 L2 地址）                      |
|  - 写入数据库：deposit_txs(user_l2_address, btc_amount, txid, ...)   |
+--------+------------------------------------------------------------+
         |
         | 结构化数据入库
         ↓
+--------v---------+
|   PostgreSQL     |
|   或 SQLite      |
+--------+---------+
         |
         | Go 服务查询
         ↓
+--------v------------------------------------------------------------+
|               Go Backend Service (门面层)                             |
|  - 提供 API：查询存款状态、构造取款流程                              |
|  - 监听 L2 Burn 事件 → 触发 BTC 打款                                 |
|  - 从数据库读取 deposit_txs → 调用 L2 mint                           |
|  - 不直接解析 BTC 交易，只读数据库！                                 |
+---------------------------------------------------------------------+


## 监听的事件

✅ RollupVerifier 合约（或叫 Aggregator、StateCommitmentChain）

// Solidity 事件定义
event StateBatchVerified(
    uint256 indexed batchId,
    bytes32 stateRoot,
    uint256 timestamp,
    bytes32 proofHash  // 可选：proof 的哈希，用于防重放
);


### zkevm合约事件PolygonZkEVMBridge.sol Claimable

// 1. 一个 batch 被验证通过
event BatchVerified(uint64 batchID);

// 2. 用户在 L2 发起取款（burn），触发桥合约事件
event Claimable(
    uint16 originNetwork,
    bytes originAddress,
    uint16 destinationNetwork,
    bytes destinationAddress,
    bytes amount,
    bytes metadata
);


// 用户在 L2 调用你的取款合约
function withdrawToBTC(string memory btcAddress) external {
    // 调用 zkEVM 官方桥合约
    zkEVMBridge.depositAsset(
        0,              // destinationNetwork: 0 表示“外部系统”
        0,              // tokenAmount (如果是 wBTC)
        "",             // to: 空（不发给任何以太坊地址）
        bytes(btcAddress) // metadata: 把 BTC 地址塞进去！
    );
}

emit Claimable(
    1,                  // originNetwork: L2
    msg.sender,         // originAddress
    0,                  // destinationNetwork: 外部系统
    "",                 // destinationAddress
    0,                  // amount
    bytes("bc1q..."),   // ✅ metadata = BTC 地址
    ""
);


## 参考
是的，这是一个**非常合理、清晰且可扩展的架构设计**！

你提出的方案完全符合现代区块链系统中“**职责分离 + 微服务化**”的最佳实践：

> ✅ **Rust 做 BTC Indexer**（安全解析比特币交易）  
> ✅ **Go 做 ETH/L2 Indexer**（监听 zkEVM 合约事件）  
> ✅ **Go + Gin 做后端服务**（协调逻辑、提供 API 给前端）

---

## 🏗️ 整体架构图

```
+------------------+     +------------------+
|  Bitcoin Network |     |   Ethereum/L2    |
+--------+---------+     +--------+---------+
         |                        |
         |                        |
         ↓                        ↓
+--------v---------+    +--------v---------+
|  Rust BTC Indexer |    |  Go ETH Indexer  |
|  - 监听 BTC 区块     |    |  - 监听 L2 事件     |
|  - 解析 OP_RETURN   |    |  - 监听 Burn/Mint |
|  - 入库 deposit     |    |  - 入库 withdrawal |
+--------+---------+    +--------+---------+
         |                        |
         +----------+-------------+
                    |
                    ↓
             +------v-------+
             |  PostgreSQL   |
             | (统一数据库)   |
             +------+--------+
                    |
                    ↓
        +-----------v-----------+
        |  Go Backend (Gin)     |
        |  - 提供 REST API       |
        |  - 协调跨链状态         |
        |  - 查询交易状态         |
        +-----------+-----------+
                    |
                    ↓
             +------v-------+
             |   前端 / 钱包   |
             | (Web / App)  |
             +--------------+
```

---

## ✅ 两个 Indexer 的职责

### 1. **Rust BTC Indexer**（`btc-indexer-rs`）
- 监听比特币网络（通过 `bitcoind` RPC/ZMQ 或 Electrum）
- 扫描所有发往**托管多签地址**的交易
- 提取 `OP_RETURN` 中的 L2 地址（如 `0xAbC...`）
- 验证交易是否符合存款规则（金额、脚本结构）
- 将有效存款写入数据库表：`btc_deposits`

```sql
-- 示例表结构
CREATE TABLE btc_deposits (
    id SERIAL PRIMARY KEY,
    txid VARCHAR(64) UNIQUE,
    amount_btc DECIMAL(18,8),
    l2_address VARCHAR(42),
    block_height INT,
    confirmed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP
);
```

---

### 2. **Go ETH/L2 Indexer**（`eth-indexer-go`）
- 监听 zkEVM 节点（通过 WebSocket）
- 订阅合约事件：
  - `Burned(user, amount, btcAddress)`
  - `Minted(user, amount)`
- 提取取款请求（目标 BTC 地址、金额）
- 写入数据库表：`l2_withdrawals`

```sql
CREATE TABLE l2_withdrawals (
    id SERIAL PRIMARY KEY,
    tx_hash VARCHAR(66) UNIQUE,
    user_address VARCHAR(42),
    btc_address VARCHAR(64),
    amount_btc DECIMAL(18,8),
    block_number BIGINT,
    status VARCHAR(20) DEFAULT 'pending', -- pending, sent, confirmed
    btc_txid VARCHAR(64),
    created_at TIMESTAMP
);
```

---

## 🌐 Go Backend (Gin) 需要提供哪些接口？

以下是前端钱包或 UI 需要的核心 API 接口：

---

### 🔹 1. 存款相关接口

#### `GET /api/v1/deposits`
**功能**：查询某 L2 地址的所有存款记录  
**参数**：`l2_address=0x...`  
**返回**：
```json
{
  "deposits": [
    {
      "txid": "a1b2c3...",
      "amount": 1.0,
      "confirmed": true,
      "block_height": 800000,
      "created_at": "2025-09-23T10:00:00Z"
    }
  ]
}
```

#### `GET /api/v1/deposits/:txid`
**功能**：查询单笔存款状态（是否确认、是否已 mint）  
**返回**：
```json
{
  "txid": "a1b2c3...",
  "amount": 1.0,
  "l2_address": "0xAbC...",
  "confirmed": true,
  "minted": true,
  "mint_tx_hash": "0x...",
  "block_height": 800000
}
```

---

### 🔹 2. 取款相关接口

#### `GET /api/v1/withdrawals`
**功能**：查询某用户的所有取款记录  
**参数**：`user_address=0x...`  
**返回**：
```json
{
  "withdrawals": [
    {
      "tx_hash": "0x...",
      "btc_address": "bc1q...",
      "amount": 1.0,
      "status": "sent",
      "btc_txid": "d4e5f6...",
      "created_at": "2025-09-23T10:30:00Z"
    }
  ]
}
```

#### `GET /api/v1/withdrawals/:tx_hash`
**功能**：查询单笔取款状态（是否已广播 BTC 交易）  
**返回**：
```json
{
  "tx_hash": "0x...",
  "btc_address": "bc1q...",
  "amount": 1.0,
  "status": "confirmed",
  "btc_txid": "d4e5f6...",
  "btc_explorer_url": "https://mempool.space/tx/d4e5f6..."
}
```

---

### 🔹 3. 状态查询接口（健康检查）

#### `GET /api/v1/status`
**功能**：返回系统整体状态  
**返回**：
```json
{
  "status": "ok",
  "latest_btc_block": 800000,
  "latest_l2_block": 1234567,
  "service": {
    "btc_indexer": "running",
    "eth_indexer": "running",
    "database": "connected"
  }
}
```

---

### 🔹 4. （可选）构造存款 PSBT（如果前端需要）

#### `POST /api/v1/deposits/construct`
**功能**：返回未签名的 PSBT Base64  
**参数**：`{ "l2_address": "0x...", "amount": 1.0 }`  
**返回**：
```json
{
  "psbt_base64": "cHNidP8..."
}
```
> ⚠️ 注意：实际签名仍由用户钱包（如 Unisat）完成

---

## 🧠 后端服务的内部协调逻辑

除了提供 API，Gin 服务还可以包含：

| 功能 | 实现方式 |
|------|----------|
| **自动 mint wBTC** | 定时扫描 `btc_deposits` 表，对已确认且未 mint 的记录调用 L2 合约 |
| **自动广播 BTC 取款** | 定时扫描 `l2_withdrawals` 表，对 `status=pending` 的记录构造并广播 BTC 交易 |
| **状态同步** | 更新 `l2_withdrawals` 中的 `btc_txid` 和 `status` |

---

## ✅ 总结：你设计的架构非常优秀

| 模块 | 技术栈 | 理由 |
|------|--------|------|
| BTC Indexer | 🦀 Rust + `bdk`/`rust-bitcoin` | 安全解析比特币交易，支持 Taproot/OP_RETURN |
| ETH/L2 Indexer | 🐹 Go + `geth` | 熟悉生态，轻松监听事件 |
| 数据库 | PostgreSQL | 统一存储两边状态 |
| 后端服务 | 🐹 Go + Gin | 快速提供 REST API，集成监控 |
| 前端 | Web + Unisat | 用户交互 |

---

## 🔚 下一步建议

你可以先实现：

1. **Rust BTC Indexer**：监听一个测试网多签地址 + OP_RETURN
2. **Go ETH Indexer**：监听本地 zkEVM 的 Burn 事件
3. **Gin 服务**：提供 `/deposits` 和 `/withdrawals` 查询接口

如果你需要，我可以为你提供：
- Rust BTC Indexer 的最小代码模板
- Go Gin API 的路由示例
- 数据库表结构 SQL

欢迎继续提问！