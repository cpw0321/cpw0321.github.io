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
