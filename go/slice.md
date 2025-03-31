## 浅拷贝(Shallow Copy)

当使用赋值操作或简单传递切片时，发生的是浅拷贝：

```go
original := []int{1, 2, 3}
copy := original  // 浅拷贝
```

这种情况下：

-   原切片`original`仍然可以访问
-   两个切片共享底层数组
-   修改一个切片的元素会影响另一个切片

```go
copy[0] = 99
fmt.Println(original[0]) // 输出 99
```

## 深拷贝(Deep Copy)

使用`copy()`函数或重新切片并追加可以创建深拷贝：

```go
original := []int{1, 2, 3}
newSlice := make([]int, len(original))
copy(newSlice, original)  // 深拷贝
```

这种情况下：

-   原切片`original`仍然可以访问且内容不变
-   新切片有自己的底层数组
-   修改一个切片的元素不会影响另一个切片

```go
newSlice[0] = 99
fmt.Println(original[0]) // 输出 1
```

## 扩容后的行为

当切片append操作导致扩容时，会创建新的底层数组：


```go
original := []int{1, 2, 3}
newSlice := append(original, 4)  // 可能触发扩容
```

-   如果未扩容，两个切片共享部分底层数组
-   如果扩容了，`newSlice`会有新的底层数组，`original`保持不变

## 总结

1.  原切片在拷贝后总是可以访问
1.  浅拷贝时，原切片内容可能随新切片修改而变化
1.  深拷贝时，原切片内容保持不变
1.  扩容操作可能导致新切片与原切片分离