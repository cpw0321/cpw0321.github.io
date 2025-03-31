00# go

## 一、基础知识

### 1.1 语法
```text
1. %v    只输出所有的值
2. %+v 先输出字段类型，再输出该字段的值
3. %#v 先输出结构体名字值，再输出结构体（字段类型+字段的值）

```

### 1.2 类型
#### 1.2.1 单位
+ 比特（位）：计算机存储信息的最小单位，称之为位（bit），音译比特，二进制的一个“0”或一个“1”叫一位
+ 字节：计算机存储容量基本单位是字节（Byte），音译为拜特，8个二进制位组成1个字节，一个标准英文字母占一个字节位置，一个标准汉字占二个字节位置

> 1字节 = 8位   1byte = 8bit

#### 1.2.1 int类型
![img.png](images/img01.png)


### 1.3 并发

#### 1.3.1、进程、线程、协程

> 进程是程序在操作系统中的一次执行过程，系统进行调度和分配资源的一个独立单位

> 线程是进程的一个执行实体，是cpu调度和分派的基本单位，他比进程更小能独立运行的的基本单元

一个进程可以创建和撤销多个线程，同一个进程中的多个线程之间可以并发执行

> 协程，独立的栈空间、共享堆空间，调度由用户自己控制，本质上类似于用户级线程，这些用户级线程的调度也是自己实现
线程，一个线程可以跑多个协程，协程是轻量级的线程

#### 1.3.2、并发与并行

多线程程序在一个核的cpu上运行，就是并发  
多线程程序在多个核的cpu上运行, 就是并行  


### 1.4 问题
#### 1.4.1 

#### 1.4.2. 地址空间存储
![img.png](images/img02.png)
结论：A1比A2更省空间

#### 1.4.3. 类型比较
![img.png](images/img03.png)
结论：func slice map 是地址不能进行比较
引用类型：slice map chan  
chan可以进行比较， why?

### 1.5 interface
![img.png](images/img04.png)

runtime/iface.go   func convT2E 与func convT2I
+ iface（更复杂）  
  接口相关封装
+ eface  
  泛型相关封装  

26种数据类型
![img.png](images/img05.png)

![img.png](images/img06.png)

### 1.6. GPM调度器
#### 1.6.1 csp通讯顺序进程（通讯来共享内存）
将两个并发执行的实体通过通道channel连接起来，所有的消息都通过channel传输

#### 1.6.2 GPM
+ Goroutine代表着Golang中的协程，通过Goroutine封装的代码片段将以协程方式并发执行，是GPM调度器调度的基本单位。
+ Processor代表执行Goroutine的上下文环境及资源，是GPM调度器中关联内核级线程与协程的中间调度器。
+ Machine是内核线程的封装，一个M与一个内核级线程一一对应，为Goroutine的执行提供了底层线程能力支持.

![img.png](images/img07.png)

### 1.7 defer执行顺序
+ 1.return之后执行defer。
+ 2.defer是按照栈的顺序执行，先进后出。
+ 3.panic之后执行defer。
+ 4.panic会中断流程，panic之后的逻辑不会运行。
+ 5.如果不想影响主流程的逻辑应该在函数方法中recover。  

参考： https://blog.csdn.net/qq_15115793/article/details/108074042

### 1.8 Golang中汉字处理
每个中文字，占3个byte
```text
str1 := "Hello,世界"  
fmt.Println(len(str1)) // 打印结果：12
```

### 1.9 sync.one原理
https://zhuanlan.zhihu.com/p/261988561

实现原理：
type Once struct { 
    done uint32 //标志位
    m Mutex //保证原子操作
}
通过标志位来判断是否执行过，未执行就加锁执行函数调用，并将标志位置为1

自己如何实现：
通过channel来实现
//构造一个有缓冲的通道，防止deadlock错误
 ch := make(chan int,1)
//在channel发送一个数字1作为标志位
 ch <- 1

### 1.10 

### 1.11 slice底层原理及其扩容
扩容容量的选择遵循以下规则：
如果原Slice容量小于1024，则新Slice容量将扩大为原来的2倍
如果原Slice容量大于等于1024，则新Slice容量将扩大为原来的1.25倍
使用append()向Slice添加一个元素的实现步骤如下：
假如Slice容量够用，则将新元素追加进去，Slice.len++，返回原Slice
原Slice容量不够，则将Slice先扩容，扩容后得到新Slice;将新元素追加进新Slice，Slice.len++，返回新的Slice。



---
## 问答
### 1、channel
#### 1.1. 判断chan是否关闭

```text
b, ok := <-ch
如果 channel 关闭，ok 值为 false
写已经关闭的chan会panic
```
#### 1.2. 如何优雅的关闭channel
> 关闭一个 closed channel 会导致 panic
> 向一个 closed channel 发送数据会导致 panic

+ 单生产者，单消费者
+ 单生产者，多消费者  
    前两个直接让生产者关闭channel即可
+ 多生产者，单消费者
+ 多生产者，多消费者  
    - 生产者主动退出，在另外一个goroutine中关闭，可利用sync.WaitGroup或额外channel
    - 生产者不主动退出，让消费者发送信号让生产者停止发送数据，channel未被任何goroutine使用，gc会回收
    - 生产和消费都不主动退出，让消费者监听一个退出信息，变成消费者主动退出情况

实现方法可以参考：
https://blog.csdn.net/studyhard232/article/details/88996434

#### 1.3. channel内存泄露
> 对于一个channel来说，如果没有任何goroutine引用，gc会对其进行回收，不会引起内存泄露。

> 而当goroutine处于接收或发送阻塞状态，channel处于空或满状态时，一直得不到改变，gc则无法回收这类一直处于等待队列中的goroutine，引起内存泄露。

引起内存泄露的几种情况：
+ 发送端channel满了，没有接收端
+ 接收端channel为空，没有发送端
+ channel未初始化

参考：https://blog.csdn.net/firetreesf/article/details/125188759

#### 1.4. channel底层实现原理
循环列表+互斥锁

参考：
https://blog.csdn.net/qq_39382769/article/details/122335732  

### 2. context
#### 2.1. context.Value 查找的过程是什么
递归查找，类似一个树


### 3. gc
#### 3.1. stw 的时机
参考：https://blog.csdn.net/Jacson__/article/details/124754786
```text
1、标记清除: v1.3
2、三色标记: v1.5
3、混合写屏障+三色标记: v1.8
```

### 4. map
#### 4.1 map是否线程安全
不安全
```text
func TestName1(t *testing.T) {
	m := make(map[int]int, 10)
	for i := 1; i <= 10; i++ {
		m[i] = i
	}

	for k, v := range m {
		go func() {
			fmt.Println("k ->", k, "v ->", v)
		}()
	}
	time.Sleep(2 * time.Second)
}
// 打印全部是9

func TestName2(t *testing.T){
    m := make(map[int]int, 10)
    for i := 1; i<= 10; i++ {
        m[i] = i
    }
    
    for k, v := range(m) {
        go func(k, v int) {
            fmt.Println("k ->", k, "v ->", v)
        }(k,v)
    }
    time.Sleep(5 * time.Second)
}

func TestName3(t *testing.T) {
	var m sync.Map
	for i := 1; i <= 10; i++ {
		m.Store(i, i)
	}

	f := func(k, v interface{}) bool {
		go func() {
			fmt.Println("k ->", k, "v ->", v)
		}()
		return true
	}
	m.Range(f)

	time.Sleep(2 * time.Second)
}
```

#### 4.2 map底层原理

https://blog.csdn.net/luolianxi/article/details/105371079
map是由哈希桶+哈希链实现的HashTable

![img.png](images/img08.png)

![img.png](images/img09.png)

### 5、swag
参考：https://blog.csdn.net/JunChow520/article/details/122339247  

```shell
swag init --parseInternal --parseDependency --dir .,../../internal/api
```

### 6、protoc
相关包下载
```text
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
go install github.com/envoyproxy/protoc-gen-validate@latest
go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway@v2
```
运行命令
```shell
protoc -I.:${PROTO_INCLUDE} --go_out=./ --go_opt=paths=source_relative \
--go-grpc_out=./ --go-grpc_opt=paths=source_relative \
--grpc-gateway_out ./ \
--grpc-gateway_opt logtostderr=true \
--grpc-gateway_opt paths=source_relative \
--grpc-gateway_opt generate_unbound_methods=true \
--validate_out="lang=go:." --validate_opt=paths=source_relative \
proto/*.proto
```

protoc -I ../../../  -I ./ --go_out=plugins=grpc:. $proto
参考：https://blog.csdn.net/RA681t58CJxsgCkJ31/article/details/129360092  

参数说明：
参考：https://www.cnblogs.com/cxt618/p/15647316.html
```text 
-I 或者 --proto_path：用于指定所编译的源码，就是我们所导入的proto文件，支持多次指定，按照顺序搜索，如果未指定，则使用当前工作目录。
--go_out：同样的也有其他语言的，例如--java_out、--csharp_out,用来指定语言的生成位置，用于生成*.pb.go 文件
--go_opt：paths=source_relative 指定--go_out生成文件是基于相对路径的
--go-grpc_out：用于生成 *_grpc.pb.go 文件
--go-grpc_opt：
    paths=source_relative 指定--go_grpc_out生成文件是基于相对路径的
    require_unimplemented_servers=false 默认是true，会在server类多生成一个接口
--grpc-gateway_out：是使用到了 protoc-gen-grpc-gateway.exe 插件，用于生成pb.gw.go文件
--grpc-gateway_opt：
    logtostderr=true 记录log
    paths=source_relative 指定--grpc-gateway_out生成文件是基于相对路径的
    generate_unbound_methods=true 如果proto文件没有写api接口信息，也会默认生成
--openapiv2_out：使用到了protoc-gen-openapiv2.exe 插件，用于生成swagger.json 文件
```
注意，protobuf有两个重要的版本区别，旧版本github.com/golang/protobuf/protoc-gen-go 它带有protoc-gen-go，它生成protobuf消息的序列化和 grpc代码（使用–go_out=plugins=grpc时）  
新版本 google.golang.org/protobuf/ 不再支持生成 gRPC 服务定义，如果想要生成grpc代码需要使用新插件protoc-gen-go-grpc。  
plugins参数 是旧版本，新版本已弃用。新版本方法是 --go-grpc_out=。这里我们使用新版本。  

+ 生成swagger
```go
go install github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger

```

+ grpc-gateway 
```go
go install github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway 
```


### 7、gorm
#### 7.1、gorm.OnConflict
```text
result = db.Clauses(gorm.OnConflict{
    Columns: []gorm.Column{{Name: "name"}},
    DoUpdates:  gorm.Set("id", 3),//存在则更新ID为3
      }).Create(&user2)
    if result.Error != nil {
        panic(result.Error)
    }
```


### 8、闭包
一个函数以及其引用的环境变量组合而成的实体  
当一个函数嵌套在另一个函数内部，并且内部函数引用了外部函数的变量时，就形成了闭包  
```text
func main() {
    base := 10

    // 定义一个闭包函数
    add := func(num int) int {
        return base + num
    }

    // 调用闭包函数
    result := add(5)
    fmt.Println(result) // 输出: 15
}
```


### 9、defer return panic执行顺序
```text
func test2() {
  defer func() { //如果在函数内部捕获panic  则函数上层的逻辑不会中断
    fmt.Println("ddddddd")
    if r := recover(); r != nil {
      fmt.Println(r)
    }
  }()
  defer fmt.Println("cccccc")
  fmt.Println("aaaaaaaaa")
  panic("this is a error !!!") //panic 之后 所有的逻辑都不会在执行
  fmt.Println("bbbbbbbbb")
}
```
输出结果  
aaaaaaaaa  
cccccc  
ddddddd  
this is a error !!!  

总结
1.return之后执行defer。  
2.defer是按照栈的顺序执行，先进后出。  
3.panic之后执行defer。  
4.panic会中断流程，panic之后的逻辑不会运行。  
5.如果不想影响主流程的逻辑应该在函数方法中recover。  

---

## 问题
### 1、mac下编译错误
```text
fork/exec: exec format error
```
解决：
```shell
go env -w CGO_ENABLED=1
go env -w GOOS=darwin
go env -w GOARCH=amd64
```

### 2、私有的包下载问题
参考：https://blog.csdn.net/u014659211/article/details/125256705  
```shell
GOPRIVATE="*.xxx.com"
```

### 3、updates不更新0值  
通过结构体变量更新字段值, gorm库会忽略零值字段。就是字段值等于0, nil, “”, false这些值会被忽略掉，不会更新。如果想更新零值，可以使用map类型替代结构体

---
## golang规范
### 1、命名规范
+ 包命名：package  
保持package的名字和目录保持一致，尽量采取有意义的包名，简短，有意义，尽量和标准库不要冲突。包名应该为小写单词，不要使用下划线或者混合大小写。
+ 文件命名  
尽量采取有意义的文件名，简短，有意义，应该为小写单词，使用下划线分隔各个单词。
非单元测试文件不要以_test结尾，go编译器默认x_test.go为单元测试文件，不会进行编译。
+ 变量命令  
采用驼峰法，通过首字母大小写来控制是否包外可见 
+ 接口命名  
  - 单个函数的接口名以"er"作为后缀，例如type Reader interface {…}  
  - 两个函数的接口名综合两个函数名，例如type WriteFlusher interface {…}  
  - 三个以上函数的接口名，类似于结构体名，例如type Car interface {…}  
  - 特殊名词的首字母缩写需要按照规范来，例如URLProxy或者urlProxy不要命名为UrlProxy。

+ 变量名  
给常量变量命名时，遵循以下原则
  - 使用驼峰命名
  - 力求准确表达出变量的意思，不能使用无行业经验的单个字母命名的变量
  - 对于约定俗成的常量或者变量名，可以全部大写，比如GET、PUT、DELETE等
  - 不能使用特殊符号如$、 _ 等



## 切片
当使用赋值操作或简单传递切片时，发生的是浅拷贝：

### 浅拷贝与深拷贝
#### 浅拷贝(Shallow Copy)
```go
复制
original := []int{1, 2, 3}
copy := original  // 浅拷贝
```
这种情况下：
+ 原切片original仍然可以访问
+ 两个切片共享底层数组
+ 修改一个切片的元素会影响另一个切片

### 深拷贝(Deep Copy)
使用copy()函数或重新切片并追加可以创建深拷贝：

```go
original := []int{1, 2, 3}
newSlice := make([]int, len(original))
copy(newSlice, original)  // 深拷贝
```
这种情况下：
+ 原切片original仍然可以访问且内容不变
+ 新切片有自己的底层数组
+ 修改一个切片的元素不会影响另一个切片

```go
newSlice[0] = 99
fmt.Println(original[0]) // 输出 1
```

#### 扩容后的行为
当切片append操作导致扩容时，会创建新的底层数组：

```go
original := []int{1, 2, 3}
newSlice := append(original, 4)  // 可能触发扩容
```
+ 如果未扩容，两个切片共享部分底层数组
+ 如果扩容了，newSlice会有新的底层数组，original保持不变

总结
+ 原切片在拷贝后总是可以访问
+ 浅拷贝时，原切片内容可能随新切片修改而变化
+ 深拷贝时，原切片内容保持不变
+ 扩容操作可能导致新切片与原切片分离