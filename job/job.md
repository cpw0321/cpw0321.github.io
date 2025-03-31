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

### 3. 七层网络模型
+ 物理层
+ 数据链路层
+ 网络层
+ 传输层
+ 会话层
+ 表示层
+ 应用层

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



-----------------------------

## 问题
+ innodb和my 区别

+ b+和b树区别


+ rabbtmq和kafak选型

+ mysql回表



mysql 两种引擎的区别

1. 事务支持
InnoDB：支持 ACID 事务（原子性、一致性、隔离性、持久性），即可以进行事务管理，支持 COMMIT 和 ROLLBACK 操作。
MyISAM：不支持事务，所有的操作都是立即生效的，不能回滚或提交事务。
2. 数据完整性
InnoDB：支持 外键约束（Foreign Key），可以强制执行数据完整性规则，比如确保关联表之间的数据一致性。
MyISAM：不支持外键约束，不能强制表之间的数据一致性。
3. 表锁与行锁
InnoDB：支持 行级锁（Row-level Locking），即锁定数据行，而不是整个表。这有助于提高并发性，尤其在多用户高并发的环境中更为高效。
MyISAM：仅支持 表级锁（Table-level Locking），即操作整个表时都会加锁，这在写操作较多时可能会影响性能。
4. 性能
InnoDB：在读写并发较高的场景下表现较好，但由于事务和行锁的支持，性能可能稍逊色于 MyISAM（对于简单的、只读或写少的操作）。
MyISAM：在单一写操作或读操作频繁的场景中，性能较好，因为它的锁机制较简单，且没有事务的开销。但在高并发场景下可能会表现较差。
5. 数据恢复
InnoDB：支持崩溃恢复。由于采用了事务日志（redo log），在系统崩溃后，InnoDB 能够从日志中恢复未提交的数据。
MyISAM：不支持崩溃恢复。如果系统崩溃，MyISAM 表可能会丢失未写入磁盘的数据，需要使用工具如 myisamchk 来进行修复。
6. 表的存储格式
InnoDB：每个表都有自己的数据文件和索引文件，且存储在一个共享的表空间中（可以选择表空间独立）。每个表的数据存储格式是 Barracuda，支持压缩、加密等特性。
MyISAM：每个表使用三个文件：.frm 文件（表定义）、.MYD 文件（数据）、.MYI 文件（索引）。这些文件较为简单，通常不支持事务和外键约束。
7. 全文索引
InnoDB：从 MySQL 5.6 版本开始，InnoDB 支持 全文索引（Full-text indexing），但其性能和 MyISAM 比较仍然有差距。
MyISAM：原生支持 全文索引，对于需要进行全文搜索的场景，MyISAM 的性能通常优于 InnoDB。
8. 表大小
InnoDB：支持非常大的表，且有自适应的存储管理方式，适合处理大型数据库。
MyISAM：表大小上没有 InnoDB 那么灵活，表大小受限于操作系统的文件大小限制。


golang的gmp 饥饿模式
Golang 的 GMP（Goroutine、Machine、Processor）模型中，饥饿模式是指在某些情况下，调度器可能无法及时调度某些 goroutine，导致它们长时间得不到执行，尤其是在高并发或资源竞争较为严重的情况下。

饥饿模式通常发生在以下几种情况：

优先级调度：Golang 的调度器没有明确的优先级机制，可能导致某些低优先级的 goroutine（如某些需要等待的 goroutine）被长时间“饿死”。
Goroutine 调度问题：由于调度器的设计，某些 goroutine 在等待某些锁或者被调度的过程中可能长时间被跳过，特别是在 M（机器）和 P（处理器）资源有限的情况下。
工作窃取（Work Stealing）：调度器会尝试从其他 P 上窃取工作，可能导致部分 goroutine 长时间未被执行，尤其是在资源不平衡的情况下。
不过，Golang 调度器通常会尽量避免这种饥饿问题，调度器会周期性地检查并确保 goroutine 不会因调度偏差而被长时间忽略。如果你遇到饥饿问题，可以考虑调整 goroutine 的数量或优化资源竞争。