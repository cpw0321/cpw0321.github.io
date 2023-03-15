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
```solidity
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
```solidity
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





## 2、hardhat
## 2.1、介绍
官网：https://hardhat.org/  
可实现编译、部署、测试、开源和调试以太坊应用的开发环境

## 2.2、教程
参考：https://learnblockchain.cn/article/1356  
参考：https://learnblockchain.cn/article/4336

## 3、openzeppelin
### 3.1 介绍

### 3.2 安装
```shell
npm install @openzeppelin/contracts
```