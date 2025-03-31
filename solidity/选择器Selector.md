# **选择器（Selector）详解**

在 Solidity 中，**函数选择器（Function Selector）**  是一个 **4 字节（32 位）的唯一标识符**，用于在合约调用时确定要执行哪个函数。它是通过 **函数签名的 Keccak-256 哈希的前 4 个字节** 计算得到的。

* * *

## **如何计算函数选择器？**

### **函数签名（Function Signature）**

函数签名是 **函数名称 + 参数类型** 的组合，例如：

-   `transfer(address,uint256)`
-   `approve(address,uint256)`

### **计算步骤**

1.  **计算函数签名的 Keccak-256 哈希**：

    ```solidity
    bytes4 selector = bytes4(keccak256("transfer(address,uint256)"));
    ```

1.  **取前 4 字节（bytes4）** ：

    -   `transfer(address,uint256)` → `keccak256` → 取前 4 字节 → `0xa9059cbb`
    -   `approve(address,uint256)` → `keccak256` → 取前 4 字节 → `0x095ea7b3`

### **示例**

```solidity
// 计算 transfer 的选择器
bytes4 transferSelector = bytes4(keccak256("transfer(address,uint256)"));
// 输出：0xa9059cbb
```

* * *

## **2. 选择器的作用**

### **(1) 在 `call` 中调用函数**

```solidity
(bool success, ) = tokenContract.call(
    abi.encodeWithSelector(0xa9059cbb, to, amount)
);
```

等价于：

```solidity
(bool success, ) = tokenContract.call(
    abi.encodeWithSignature("transfer(address,uint256)", to, amount)
);
```

### **(2) 在合约内部判断调用**

```solidity
function onERC721Received(address, uint256, bytes calldata) external returns (bytes4) {
    return this.onERC721Received.selector; // 返回 0x150b7a02
}
```

* * *

## **3. 如何获取选择器？**

### **(1) 直接计算**

```solidity
bytes4 selector = bytes4(keccak256("transfer(address,uint256)"));
```

### **(2) 使用 `abi.encodeWithSignature`**

```solidity
bytes memory data = abi.encodeWithSignature("transfer(address,uint256)", to, amount);
// data 的前 4 字节就是 selector
bytes4 selector = bytes4(data);
```

### **(3) 使用 `selector` 属性（Solidity 0.8.0+）**

```solidity
function foo() external {
    bytes4 selector = this.foo.selector; // 返回 foo() 的选择器
}
```

* * *

## **4. 常见函数的选择器**

| 函数签名                                      | 选择器（bytes4）  |
| ----------------------------------------- | ------------ |
| `transfer(address,uint256)`               | `0xa9059cbb` |
| `approve(address,uint256)`                | `0x095ea7b3` |
| `balanceOf(address)`                      | `0x70a08231` |
| `transferFrom(address,address,uint256)`   | `0x23b872dd` |
| `onERC721Received(address,uint256,bytes)` | `0x150b7a02` |

* * *

## **5. 选择器的应用场景**

### **(1) 动态调用合约函数**

```solidity
function callWithSelector(address target, bytes4 selector, uint256 value) external {
    (bool success, ) = target.call{value: value}(
        abi.encodeWithSelector(selector, ...)
    );
    require(success, "Call failed");
}
```

### **(2) ERC-721/ERC-1155 回调验证**

```solidity
function onERC721Received(address, uint256, bytes calldata) external returns (bytes4) {
    return this.onERC721Received.selector; // 必须返回 0x150b7a02
}
```

### **(3) 代理合约（Proxy）**

在代理模式（如 Transparent Proxy、UUPS）中，选择器用于决定调用哪个逻辑合约的函数。

* * *

## **6. 选择器 vs. 函数签名**

| 对比项      | 选择器（Selector）               | 函数签名（Signature）                       |
| -------- | --------------------------- | ------------------------------------- |
| **长度**   | 4 字节（bytes4）                | 可变长度（如 `"transfer(address,uint256)"`） |
| **用途**   | 用于低级调用（`call`）              | 用于 ABI 编码                             |
| **计算方式** | `keccak256(signature)[0:4]` | 直接字符串                                 |

* * *

## **7. 注意事项**

1.  **大小写敏感**：

    -   `transfer(address,uint256)` ≠ `Transfer(address,uint256)`（选择器不同）。

1.  **空格不影响**：

    -   `transfer(address,uint256)` 和 `transfer( address, uint256 )` 的选择器相同。

1.  **参数类型必须完全匹配**：

    -   `transfer(address,uint256)` ≠ `transfer(address,uint)`（`uint` 默认是 `uint256`，但最好显式声明）。

* * *

## **8. 总结**

-   **函数选择器** 是 Solidity 中用于唯一标识函数的 4 字节值。

-   **计算方式**：`bytes4(keccak256("functionName(type1,type2)"))`。

-   **主要用途**：

    -   动态调用合约（`call` + `abi.encodeWithSelector`）。
    -   代理合约、回调验证（如 ERC-721 的 `onERC721Received`）。

-   **推荐使用 `selector` 属性**（Solidity 0.8.0+）来避免手动计算错误。