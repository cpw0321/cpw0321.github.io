# job

参考：https://www.bilibili.com/read/cv33455530/

## 基础

### 1. slice切片底层

+ 实现原理：
	- ptr一个数组，由指针指向底层数组
	- len 切片长度
	- cap 切片容量

	初始化make([]int, 5, 10)

+ 扩容：
	- v1.17 <1024为2倍，>1024为1.25倍
	- v.18  <256为2倍，>256为(1.25+3 * 256/4)

### 2. rune类型

+ string底层是byte数组
+ 中文 unicode占2个字节，utf-8占3个字节(golang默认utf-8)
+ 相当于int32, 用来处理unicode和utf-8字符，byte相当于int8处理ascii字符


### 3. map底层

+ 底层
	- 是个hmap
	- hmap有多个bmap桶
	- 每个bmap包含一个哈希链表

+ 解决hash冲突
	- 先计算hash值，根据值找到对应的桶，桶中存在元素插入链表头部

+ 扩容
	- 创建新桶，桶为原来的两倍/旧桶一样，将原来的拷贝过去，为了保持平均分布，提升查询效率
	- 旧桶不会直接删掉，而是引用去掉，gc来回收
	- 增量扩容/等量扩容
		装载因子>6.5，扩容一倍
		溢出桶过多重新分配一样的内存，即很多键值对被删除


+ slice可以作为map的key吗？
	- 可以比较的类型可以作为key
	- 不能用的
		slices
		maps
		functions

+ 并发不安全
	- sync.map解决并发安全

+ 如何并发访问
	- sync.map
	- sync.Mutex/sync.RWMutex  互斥/读写


+ nil和空map区别
	- nil为未初始化
	- 空长度为空


### 4. 函数传参传的是什么，slice,map,chan
go所有的都是值传递，都是一个拷贝，副本

### 5. select

+ select会依次检查每个case分支-channel
+ 多个channel会随机执行


### 6. context结构与使用场景

+ 作用
	- 并发协调与goroutine的生命周期控制
	- 一定的存储能力


+ 定义
	- Deadline
	- Done() <- chan struct{}
	- Err
	- Vaule

+ 使用
	- cancelCtx
	- timerCtx
	- valueCtx

### 7. channel底层


### 8. GPM


### 9. gc

+ 三色标记
	- 白色 未标记的对象， 可以被回收
	- 灰色 正在扫描的对象，存在指向白色的外部指针
	- 黑色 已经被标记的对象，根对象可到达的

+ 屏障技术
	- 插入写屏障
	- 删除写屏障


### 10. 内存泄露

+ 内存空间没有正常被释放

+ 哪些情况
	- goroutine阻塞无法退出
	- 互斥锁未释放/死锁
	- time.Ticker，没有调用stop()
	- 字符串截取，即str1 := str0[:5]  str0很大，str1一直使用



### 11. 内存逃逸

+ 本应该分配到栈上的，跑到了堆上

+ 哪些情况
	- 方法内返回局部变量指针
	- 向channel发送指针数据
	- 闭包中引用包外面的值
	- slices中存指针
	- interface{}类型
	- 栈内存溢出

+ 如何避免
	- 尽量避免调用interface类型的接口
	- 避免函数中使用指针类型局部变量

+ 分析
	- go build -gcflags '-m'

+ 危害
	- 增加gc压力
	- 内存碎片化

## 网络

## 数据库


## 算法