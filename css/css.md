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

+ padding 元素内部内容与边框之间的间距-- 内边距
```css
.element {
    padding: 10px 15px 20px 25px; /* 顶、右、底、左 方向分别为 10px, 15px, 20px, 25px */
}
```

+ margin 外边距

+ cursor 用于指定当鼠标悬停在元素上时，鼠标指针的形状

```css
.element {
    cursor: pointer; // 鼠标指针形状会变为手型
}
```

+ align-items: flex-start; 是一个 CSS 样式属性，用于设置 Flex 容器中项目（Flex 子元素）在交叉轴（垂直于主轴）上的对齐方式为起点（flex-start）。


+ overflow: auto;：根据需要显示滚动条，如果内容未溢出，则不显示滚动条

+ hover 选择器用于选择鼠标指针浮动在上面的元素，它适用于所有元素
```css
// 盒子出现阴影
.sliderCard_box:hover{
    box-shadow: var(--box-shadow);
}
```

+ grid-column: 1 / -1; 表示该项目横跨从第一条垂直网格线到最后一条垂直网格线的所有列。
+ grid-row: 1 / -1; 表示该项目纵跨从第一条水平网格线到最后一条水平网格线的所有行