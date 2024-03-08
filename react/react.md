# react

## 1. 语法

### 1.1 
+ 自定义hooks  useState  
+ context --> 中属性任何一个改变都会引起子组件重新渲染
+ useReducer --> 解决上面问题
+ useImmerReducer --> 比上一个简化代码，第三个参数可以处理 aync await



+ 在 JSX 中，可以通过对象属性的方式将 CSS 类名赋给 className 属性
```js
import React from 'react';
import Style from './Style.module.css'; // 假设 Style 是你的 CSS 模块文件名

function MyComponent() {
  return (
    <div className={Style.search_con}>
      {/* 其他内容 */}
    </div>
  );
}

export default MyComponent;
```

## 1.2. 快捷指令
+ racfe 生成component代码块