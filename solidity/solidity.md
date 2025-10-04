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
