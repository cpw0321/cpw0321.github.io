# job

参考：https://www.bilibili.com/read/cv33455530/

+ 字节：https://www.kancloud.cn/leonshi/programming/2577007
+ 面试知识点：https://zhuanlan.zhihu.com/p/690378516


+ 面试有答案 https://blog.csdn.net/IT_ziliang/article/details/123493273

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
	- 先计算hash值，根据值找到对应的桶，放到当前桶中

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

+ 无缓冲  同步
+ 有缓冲  异步


+ 通道关闭接受的为0值

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


### 12. csp并发模型
+ 传统为共享内存来通信
+ csp 以通信方式来共享内存  channel


并发模型有三种
+ 通过channel
	- 核心思想：不要通过共享内存来通信，而是通过通信来共享内存
+ sync中waitgroup
+ context


### 13. 锁
+ 互斥锁
+ 读写锁
+ 死锁
	- 自己已经上锁了，然后又调用了锁

### 14. epoll
一种io多路复用技术

调用
+ epoll_create
+ epoll_ctl
+ epoll_wait


epoll比select/poll优点：
+ select需要将用户态的socket拷贝到内核态，很低效
+ epoll监听了一个socket list，有数据也是少量的准备就绪句柄，是少量的copy


### 15. 深copy与浅copy
+ 浅，只复制数据结构最外层，不会复制内部所指向的引用类型指向的数据
	- 只复制引用，不是实际数据
	- slice是浅copy，要深copy遍历复制元素


### 16. struct
+ tag 反射
	- StructField.Tag.Get() 方法获取tag键名


+ 反色原理
	- 所有的类型都实现了interface{}
	- 被转化为反射类型Type和value， 调用reflect包


### 17. defer
+ 底层 
	- 每个defer语句对应一个_defer实例，多个实例指针连接成一个单链表，每次插入都插入头部，形成先进后出的效果


----------------------------------

## 分布式
### 1. 分布式锁
+ 基于数据库
	- 创建表，某个字段创建唯一索引，成功插入获取锁，执行完成删除数据释放锁
+ 基于redis缓存
	- set lock expireTime
	- del lock
+ 基于zk
	- 文件目录树，同一个目录下只能有唯一个文件
	- 创建目录mylock，获取目录下的所有节点，判断是不是最小节点
+ 基于etcd
	- raft算法保持强一致性，key-value

	- 保持独占， 提供一个api，只有一个节点穿件可以成功
	- 控制时序，所有用户都会安排，有一个执行顺序，通过api创建，同时会列出当前目录下的键值


----------------------------

## 网络
### 1. http
+ http1
	- 持久连接
	- 请求管道化
	- 增加缓存处理 cache-control
	- 增加host,支持断点传输
+ http2
	- 二进制分帧
	- 多路复用
	- 头部压缩
	- 服务器推送

### 2. tcp/udp
+ tcp
	- 面向连接
	- 流式协议，无大小限制
	- 可靠

+ udp
	- 面向无连接
	- 数据包协议，有大小限制
	- 不可靠


------------------------------

## 数据库
### 1. redis
#### 1.1 基本数据类型
+ string

+ hash


+ list


+ set

+ sorted set

#### 1.2 缓存
+ 穿透--redis为空，db为空
	- 缓存空的对象
	- 布隆过滤器

+ 击穿--redis 某个热点失效
	- 互斥锁
	- 热点数据永不失效

+ 雪崩--redis大量数据同时过期
	- 缓存数据设置随机时间
	- 缓存预加载


-----------------------

## 算法


## 资料
+ 面试题 https://blog.csdn.net/Bel_Ami_n/article/details/123352478