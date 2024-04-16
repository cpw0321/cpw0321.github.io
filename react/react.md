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


## 1.3. 错误

+ If you need interactivity, consider converting part of this to a Client Component.     at stringify (<anonymous>) digest: "4011066402" 原因

解决办法：  
文件开头添加 'use client'  
参考：https://blog.csdn.net/tianlangstudio/article/details/127989643



## 1.4. 问题

+ Next.js的Image组件在与div元素重叠时会调整大小 如何解决处理

layout属性有以下几个值可以选择：
fixed: 固定宽度和高度。
intrinsic: 根据自然宽度和高度缩放。
responsive: 根据父容器缩放。
fill: 使用图片最大尺寸

import Image from 'next/image'
 
function MyComponent() {
  return (
    <div>
      <Image
        src="/example.jpg"
        alt="Example Image"
        width={500}
        height={500}
        layout="responsive" // 或者 "fixed" 根据需求选择
      />
      <div>其他内容</div>
    </div>
  )
}
 
export default MyComponent