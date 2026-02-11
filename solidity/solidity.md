# solidity

## 1、solidity语法
学习示例：https://github.com/AmazingAng/WTF-Solidity  
remix编译器：http://remix.ethereum.org/

### 1.1、function
```text
function <function name> (<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```
函数可见性说明符：
+ public: 内部外部均可见。
+ private: 只能从本合约内部访问，继承的合约也不能用。
+ external: 只能从合约外部访问。
+ internal: 只能从合约内部访问，继承的合约可以用。

决定函数权限/功能的关键字：
+ view函数只可读，不可以改变区块链数据的状态。
+ pure函数更严格，既不可读也不可写。

view:
```text
pragma solidity ^0.7.0;

contract viewSample {

  //view函数从区块链读取数据
  function getBlock() public view returns (uint){
    uint blocknumber = block.number;
    return blocknumber;
  }
}
```
pure:
```text
pragma solidity ^0.8.18;

contract pureSample {
  //pure函数只关心函数内的变量和计算
  function getResult() public pure returns(uint sum){
    uint a = 5;
    uint b = 7;
    sum = a + b;
  }
}
```

### 1.2、变量
#### 1.2.1、数据位置
+ storage
  - 数据存在链上
  - gas多
+ memory
  - 临时存在内存里
  - gas少
+ calldata
  - 临时存在内存里
  - gas少
  - 变量不能被修改

#### 1.2.2、mapping映射
```text
mapping(uint => address) public idToAddress; // id映射到地址
```

#### 1.2.3、常数
+ constant  必须在声明的时候初始化，之后再也不能改变
+ immutable  可以在声明时或构造函数中初始化

#### 1.2.4、构造函数constructor


#### 1.2.5、修饰器
```text
modifier onlyOwner {
    require(msg.sender == owner); // 检查调用者是否为owner地址
    _; // 如果是的话，继续运行函数主体；否则报错并revert交易
}
```

#### 1.2.5、事件event
+ 响应：应用程序（ether.js）可以通过RPC接口订阅和监听这些事件，并在前端做响应。
+ 经济：事件是EVM上比较经济的存储数据的方式，每个大概消耗2,000 gas；相比之下，链上存储一个新变量至少需要20,000 gas。

+ event 定义事件
+ emit 调用事件

index 索引
```text
event Transfer(address indexed from, address indexed to, uint256 value);
```

#### 1.2.6、继承
+ virtual: 父合约中的函数，如果希望子合约重写，需要加上virtual关键字。
+ override：子合约重写了父合约中的函数，需要加上override关键字。

#### 1.2.7、抽象合约
abstract  
一个智能合约里至少有一个未实现的函数

#### 1.2.8、异常
+ error
```text
error TransferNotOwner(); // 自定义error
revert TransferNotOwner();
```
+ require
```text
require(_owners[tokenId] == msg.sender, "Transfer Not Owner");
```
+ assert
```text
assert(_owners[tokenId] == msg.sender);
```

#### 1.2.9 库合约
+ 利用using for指令
```text
// 利用using for指令
using Strings for uint256;
function getString1(uint256 _number) public pure returns(string memory){
    // 库函数会自动添加为uint256型变量的成员
    return _number.toHexString();
}
```
+ 通过库合约名称调用库函数
```text
// 直接通过库合约名调用
function getString2(uint256 _number) public pure returns(string memory){
    return Strings.toHexString(_number);
}
```

#### 1.2.10 ABI编码解码

#### 1.2.11 签名
签名参考：https://learnblockchain.cn/article/5012


#### 1.2.12 工厂合约
工厂合约相当于自定义了一个类，将该类放入数据，其实就是数组
new FactoryClass(name)


#### 1.2.13 fallback回退函数
一般用于函数内部的捕获


#### 1.2.14 哈希
keccak256() 相当于md5
abi.encodePacked abi编码类似json


#### 1.2.15 call

address.call{value:0}() // 写
address.staticcall() // 读

call与staticcall区别就是会不会触发状态改变，前者改变后者不改变

#### 1.2.16 签名验证

1、内容进行keccak256() byte32  
2、通过当前账户对步骤1结果（含有以太坊签名的前缀字符串，bytes memory prefix = "\x19Ethereum Signed Message:\n32";）签名  
3、通过（含有签名前缀字符串）ETH哈希(步骤2的值) + 签名结果  还原账户address  
4、验证签名 原始内容 + 签名结果 + 账户address 校验签名是否为address

#### 1.2.17 muticall
一个合约里面同时调用其他两个合约方法

#### 1.2.18 create2 合约地址生成算法

bytes memory packed = abi.encodePacked(
  uint8(0xff), // 255
  address(this),
  salt, // byte32
  keccak256(creationCode) // byte32  type(合约名称)。creationCode
)

byte32 hash = keccak256(packde)
address newAddress = address(uint160(uint(hash)))

#### 1.2.19 

---------------------------------

## 2、hardhat
## 2.1、介绍
官网：https://hardhat.org/  
可实现编译、部署、测试、开源和调试以太坊应用的开发环境

## 2.2、教程
参考：https://learnblockchain.cn/article/1356  
参考：https://learnblockchain.cn/article/4336
参考：https://blog.csdn.net/qq_16137795/article/details/127495671  

```shell
# 编译 /contracts
npx hardhat compile 

# 测试 /test
npx hardhat test

# 部署 scripts
npx hardhat run scripts/TokenDeploy.js --network localhost

# 本地生成账户
npx hardhat node

```

## 3、openzeppelin
### 3.1 介绍

### 3.2 安装
```shell
npm install @openzeppelin/contracts
```

## 4、ipfs
### 4.1 ipfs上传图片
参考：https://zhuanlan.zhihu.com/p/32682117

图片示例:
https://ipfs.io/ipfs/QmRRPWG96cmgTn2qSzjwr2qvfNEuhunv6FNeMFGa9bx6mQ  


## 5、合约调用
### 5.1、升级合约部署
https://learnblockchain.cn/article/2920  


### 5.2、 golang调用合约方法
https://learnblockchain.cn/article/6220  


## 6. EIP
eip--以太坊改进建议  
erc--以太坊意见征求稿  
eip包含erc
### 6.1. erc
+ erc165: 声明支持的接口
+ erc721: nft


## 7. Foundry 
+ 教程：https://learnblockchain.cn/docs/foundry/i18n/zh/getting-started/first-steps.html
+ 教程：https://docs.moonbeam.network/cn/builders/build/eth-api/dev-env/foundry/

### 7.1. Manual deployment
```bash
# 1. Install Foundry
curl -L https://foundry.paradigm.xyz | bash
foundryup

# 2. Deploy Multicall3
$ forge create --rpc-url <your_rpc_url> \
--private-key <your_private_key> \
--gas-price 2500000000 \
src/Multicall3.sol:Multicall3

# 3. Call Multicall3 getChainId method
cast call <multicall3_address> "getChainId()" --rpc-url <your_rpc_url> 
```


## 问题
+ call与transfer区别
  transfer gas是固定的消耗完可能失败




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

## poh

pos\pow 节点之间需要同步时间，单个节点是基于本地生成交易时间戳的

poh 用的全局时钟  交易顺序为 前一个交易hash+ timestamp 生成hash, 一条连续的hash来保证交易的顺序性

## pump.fun
 Meme 币发行的工具

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
+ bip39 助记词  12/24个单词  帮我总结上面区块链的知识点，不对的帮我完善和说明其原因




好的，没问题！这是一个关于 `call`/`delegatecall` 以及 UUPS 代理模式的面试知识点总结，帮你理清核心概念和面试中可能遇到的问题。

---

### **核心概念：`call` 与 `delegatecall`**

这两个是 Solidity 中非常底层且强大的函数，用于在合约之间进行消息调用。理解它们的区别是掌握代理模式（如 UUPS）的基础。

| 特性 | `call` | `delegatecall` |
| :--- | :--- | :--- |
| **执行上下文 (Context)** | 在**被调用合约**的上下文中执行。 | 在**调用者合约**的上下文中执行。 |
| **状态变量 (Storage)** | 操作的是**被调用合约**的状态变量。 | 操作的是**调用者合约**的状态变量。 |
| **代码 (Code)** | 执行的是**被调用合约**的代码。 | 执行的是**被调用合约**的代码，但**作用于调用者合约的存储**。 |
| **`msg.sender`** | 被调用合约看到的 `msg.sender` 是**调用者合约的地址**（或原始调用者，如果调用者是合约）。 | **保持不变**，与调用 `delegatecall` 之前相同（通常是原始用户）。 |
| **`msg.value`** | 可以传递 `msg.value`。 | 可以传递 `msg.value`。 |
| **`this` (或 `address(this)`)** | 在被调用代码中，`this` 指向**被调用合约**。 | 在被调用代码中，`this` 指向**调用者合约**。 |
| **典型用途** | 通用的跨合约调用，发送 ETH。 | **代理模式 (Proxy Pattern)**，实现逻辑升级。 |

**核心区别一句话总结：**
> `call` 像是让别人（被调用合约）用自己的钱和房子（存储）帮你做事（代码）。
> `delegatecall` 像是请一个专家（被调用合约的代码）来你家（调用者的存储），用你的钱和家具（存储）帮你做事。

---

### **UUPS 代理模式 (Universal Upgradeable Proxy Standard)**

UUPS 是一种流行的**可升级智能合约**的设计模式，它利用了 `delegatecall` 的特性。

#### **1. 为什么需要可升级合约？**
智能合约一旦部署，代码就无法更改（不可变性）。但现实中，代码可能有 Bug 或需要新功能。代理模式提供了一种“升级”逻辑代码的机制。

#### **2. UUPS 的核心组件**

*   **Proxy Contract (代理合约):**
    *   这是用户直接交互的合约。
    *   它持有**状态变量**（存储数据）和一个指向**逻辑合约**的地址。
    *   它的 `fallback` 函数使用 `delegatecall` 将所有调用转发给逻辑合约。
    *   **关键：** 代理合约的存储被逻辑合约的代码所修改。

*   **Implementation Contract (逻辑合约 / 实现合约):**
    *   包含实际的业务逻辑（函数实现）。
    *   在 UUPS 中，**升级逻辑本身也包含在逻辑合约中**（这是与透明代理的主要区别）。

*   **Upgrade Logic (升级逻辑):**
    *   一个特殊的函数（通常是 `upgradeTo(address newImplementation)`），允许管理员将代理合约指向一个新的逻辑合约地址。
    *   在 UUPS 中，这个 `upgradeTo` 函数是**逻辑合约的一部分**。

#### **3. UUPS 的工作流程**

1.  **部署:**
    *   首先部署一个初始的逻辑合约（v1）。
    *   然后部署代理合约，并在构造函数中设置其逻辑合约地址为 v1 的地址。
2.  **用户调用:**
    *   用户调用代理合约上的一个函数（例如 `myFunction()`）。
    *   代理合约的 `fallback` 函数捕获这个调用。
    *   `fallback` 函数使用 `delegatecall` 调用逻辑合约 v1 的 `myFunction()`。
    *   **关键：** `myFunction()` 的代码被执行，但它读写的是**代理合约**的存储。
3.  **升级:**
    *   部署一个新的逻辑合约（v2），它包含修复的 Bug 或新功能。
    *   管理员（或治理合约）调用代理合约的 `upgradeTo(address v2)` 函数。
    *   代理合约使用 `delegatecall` 调用当前逻辑合约 v1 的 `upgradeTo` 函数。
    *   `upgradeTo` 函数执行，并将代理合约内部存储的逻辑合约地址更新为 v2 的地址。
    *   升级完成！所有后续调用都会通过 `delegatecall` 转发到 v2。

#### **4. UUPS vs 透明代理 (Transparent Proxy)**

| 特性 | UUPS | 透明代理 |
| :--- | :--- | :--- |
| **升级逻辑位置** | **在逻辑合约中** | **在代理合约中** |
| **升级函数调用者** | 代理合约 `delegatecall` 逻辑合约的 `upgradeTo` | 用户直接调用代理合约的 `upgradeTo` |
| **Gas 成本 (升级)** | **较低**（因为逻辑在实现中，代理合约代码简单） | 较高（代理合约包含升级逻辑） |
| **安全性** | 逻辑合约必须可信，因为它可以升级自己。需要精心设计 `upgradeTo` 的权限控制。 | 代理合约控制升级，逻辑合约无法自行升级，更“透明”。 |
| **标准化** | EIP-1822 | EIP-1967 |
| **复杂性** | 逻辑合约需要继承 UUPS 升级逻辑，可能更复杂。 | 代理合约负责升级，逻辑合约更“纯净”。 |

---

### **面试中可能遇到的问题 & 回答要点**

1.  **问：`call` 和 `delegatecall` 的区别是什么？**
    *   **答：** 核心在于执行上下文。`call` 在被调用合约的上下文中执行，操作其存储；`delegatecall` 在调用者合约的上下文中执行，操作调用者的存储，但执行被调用合约的代码。`delegatecall` 常用于代理模式，实现代码升级。

2.  **问：什么是代理模式？为什么用它？**
    *   **答：** 代理模式是一种设计模式，通过一个代理合约将调用转发给逻辑合约。主要目的是实现合约的可升级性，因为直接部署的合约代码不可变。代理合约持有状态，逻辑合约持有代码，通过改变代理指向的逻辑合约地址来“升级”功能。

3.  **问：UUPS 代理模式是如何工作的？**
    *   **答：** UUPS 将升级逻辑（`upgradeTo` 函数）放在逻辑合约中。当需要升级时，代理合约通过 `delegatecall` 调用当前逻辑合约的 `upgradeTo` 函数，该函数会修改代理合约存储中的逻辑合约地址。这利用了 `delegatecall` 在调用者（代理）上下文中执行代码的特性。

4.  **问：UUPS 和透明代理的主要区别？**
    *   **答：** 最大区别是升级逻辑的位置。UUPS 的升级逻辑在**逻辑合约**中，升级时代理 `delegatecall` 逻辑合约的升级函数。透明代理的升级逻辑在**代理合约**中，用户直接调用代理的升级函数。UUPS 升级时 gas 成本更低，但要求逻辑合约本身是可信的。

5.  **问：使用 `delegatecall` 有什么风险？**
    *   **答：**
        *   **存储碰撞 (Storage Collision):** 如果代理合约和逻辑合约的存储变量声明顺序不一致，`delegatecall` 可能会错误地读写存储槽，导致数据损坏。必须使用 `immutable` 变量、继承或遵循严格的存储布局规范（如 OpenZeppelin 的 `Initializable` 和 `ERC1967Proxy`）。
        *   **重入攻击:** `delegatecall` 不会改变 `msg.sender`，如果逻辑合约没有适当的重入锁（如 `ReentrancyGuard`），可能会被利用。
        *   **恶意逻辑合约:** 在 UUPS 中，如果攻击者控制了逻辑合约，他们可以调用 `upgradeTo` 将代理指向一个恶意合约，完全接管。因此，`upgradeTo` 函数必须有严格的权限控制（如仅管理员）。

6.  **问：如何防止 UUPS 代理的存储碰撞？**
    *   **答：**
        *   使用 **`_gap`** 变量：在基础合约中预留足够的 `uint256[50] private __gap;`，为未来继承的合约留出存储空间，避免新变量覆盖旧变量。
        *   使用 **OpenZeppelin 的可升级合约库**：它们提供了 `Initializable` 合约和经过审计的存储布局。
        *   **严格遵循继承顺序**：确保所有合约按正确的顺序继承，共享相同的存储基类。
        *   **避免在代理合约中声明状态变量**（除了代理所需的，如逻辑地址、管理员等），将所有业务状态放在逻辑合约中通过继承管理。

7.  **问：在 UUPS 中，`initialize` 函数的作用是什么？为什么不能用构造函数？**
    *   **答：** 因为代理合约和逻辑合约是分开部署的，构造函数只在部署时运行一次。当逻辑合约 v1 被部署时，它的构造函数运行，但代理合约的存储并未被初始化。所以需要一个 `initialize` 函数，在代理合约指向 v1 后，由管理员调用一次来初始化代理合约的存储。必须使用 `initializer` 修饰符防止多次初始化。

---

### **面试准备建议**

*   **动手实践：** 自己用 Hardhat 或 Foundry 写一个简单的 UUPS 代理合约（可以用 OpenZeppelin 的 `UUPSUpgradeable`），部署并尝试升级，加深理解。
*   **阅读源码：** 查看 OpenZeppelin 的 `Proxy`, `ERC1967Proxy`, `UUPSUpgradeable` 等合约的源码，理解其实现细节。
*   *