# erc协议

## **(1) `IERC165`（接口检测标准）**

-   **作用**：允许合约检查另一个合约是否实现了特定接口（如 `IERC721`）。

-   **核心函数**：


    ```solidity
    function supportsInterface(bytes4 interfaceId) external view returns (bool);
    ```

    -   调用者传入接口的 `interfaceId`（如 `IERC721` 的 ID），合约返回 `true` 或 `false` 表示是否支持该接口。

## **(2) `IERC721`（NFT 标准接口）**

-   **作用**：定义 NFT 的基本功能（如转账、查询所有者等）。

-   **继承关系**：

    ```solidity
    interface IERC721 is IERC165 { ... }
    ```

    -   表示 `IERC721` **必须实现** `IERC165` 的 `supportsInterface` 方法。

### **检测合约是否支持 `IERC721`**

```solidity
function isERC721(address contractAddress) external view returns (bool) {
    return IERC165(contractAddress).supportsInterface(0x80ac58cd);
}    
```