# css

## 1.1. 语法

+ rem rem 是一种相对单位，它基于根元素的字体大小进行计算。如果根元素的字体大小为 16px，则 1rem 等于 16px。
+ border-radius 设置元素边框圆角

```css
border-radius: 1rem;
```

+ transition:用于指定元素在状态改变时过渡效果的属性

```css
transition: all 0.3s ease-in-out; // 元素被设置为当任何属性发生变化时都会以 0.3 秒的时间以 ease-in-out 缓动函数进行过渡
```

+ grid 创建网格布局的属性
+ 1fr 一个单位的剩余空间

```css
.container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr; /* 定义三列 */
    grid-gap: 10px; /* 设置列之间的间距 */
}
```

+ @media 是 CSS 中用于指定针对不同媒体类型或设备条件应用样式的规则

```css
@media (max-width: 600px) {
  body {
    background-color: lightblue;
  }
}
```

+ solid 实线

```css
.element {
    border-top: 1px solid red; /* 顶部边框为红色实线 */
}

```