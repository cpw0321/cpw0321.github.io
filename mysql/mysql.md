# mysql

## 一、安装

## 1.1. docker运行

```text
docker run -d  --name mysql \
-v ~/code/docker/mysql/data:/var/lib/mysql \
-v ~/code/docker/mysql/conf:/etc/mysql \
-v ~/code/docker/mysql/log:/var/log/mysql \
-p 3306:3306 \
-e TZ=Asia/Shanghai \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7
```

```text
docker run -d  --name mysql \
-v ~/code/docker/mysql8:/var/lib/mysql \
-p 3306:3306 \
-e TZ=Asia/Shanghai \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:8.0
```

## 二、原理
### 2.1 ACID
原子性 （undo log）、一致性、隔离性、持久性 (redo log)  
![img.png](images/img01.png)

### 2.2 事务
#### 2.2.1 事务自动开启和关闭
```shell
mysql> SHOW VARIABLES LIKE 'autocommit';

+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+

1 row in set, 1 warning (0.04 sec)
SET autocommit = 0|1|ON|OFF;
```

#### 2.2.2 事务级别

| 事务隔离级别                             | 脏读 | 不可重复读 | 幻读 |
|------------------------------------------|------|------------|------|
| 读未提交<br />（read-uncommitted）       | 是   | 是         | 是   |
| 不可重复读<br />（read-committed）oracle | 否   | 是         | 是   |
| 可重复读<br />（repeatable-read）mysql   | 否   | 否         | 是   |
| 串行化<br />（serializable）             | 否   | 否         | 否   |


数据库默认级别：可重复读，会产生幻读，什么是幻读：第一次读出来的是1-5行，再次读读出来的是1-6行


+ 脏读:  
在事务A执行过程中，事务A对数据资源进行了修改，事务B读取了事务A修改后的数据
+ 幻读:  
事务A 按照一定条件进行数据读取， 期间事务B 插入了相同搜索条件的新数据，事务A再次按照原先条件进行读取时，发现了事务B 新插入的数据 称为幻读
+ 不可重复读:  
如果事务A 按一定条件搜索， 期间事务B 删除了符合条件的某一条数据，导致事务A 再次读取时数据少了一条。这种情况归为 不可重复读
+ 快照读:  
快照读就是读取数据的时候会根据一定规则读取事务可见版本的数据。
+ 当前读:  
当前读就是读取最新版本的数据。
+ 串行化:  
  事务A和事务B同时操作数据时，如果事务A修改了数据，没有提交数据时，事务B想增加、修改、删除数据，都必须等待事务A提交，事务B才能执行

> 不可重复读针对的是修改，幻读针对的是新增或删除
> 不可重复读指的是两次读取过来的数据内容不一样，幻读指的是两次读取过来的数据条数不一样

### 2.3 索引
#### 2.3.1 类型
索引类型：主键索引、唯一索引、普通索引、组合索引、全文索引 
+ 索引失效
联合索引  
最佳左前缀法则  
失效的案例：  
A > 1 and B = 1   失效，改成a=1就满足最左前缀法则  
%like% 不走索引  
![img.png](images/img02.png)

![img.png](images/img03.png)

![img.png](images/img04.png)

+ 什么是覆盖索引？  
Select 中使用的索引和where中使用的索引是一致的  
两个缺点：  
  - a)	select索引被where覆盖掉了  
  - b)	传输数据时浪费不必要的性能  

文件内排序
开辟了一个新的内存空间，将数据拷贝过去重新排序

#### 2.3.2 索引查询  
Innodb(聚簇索引)  
MyIsam(非聚簇索引)  

聚簇索引：将索引和数据放在了一起（理解主键索引）  
没有主键索引会生成一个默认的非空索引，或gen_clust_index隐式聚簇索引  

![img.png](images/img05.png)

![img.png](images/img06.png)

#### 2.3.3 B树和B+树的区别
+ (1) B+树改进了B树, 让内结点只作索引使用, 去掉了其中指向data record的指针, 使得每个结点中能够存放更多的key, 因此能有更大的出度. 这有什么用? 这样就意味着存放同样多的key, 树的层高能进一步被压缩, 使得检索的时间更短. 
+ (2)当然了,由于底部的叶子结点是链表形式, 因此也可以实现更方便的顺序遍历, 但是这是比较次要的, 最主要的的还是第(1)点

![img.png](images/img07.png)

#### 2.2.4 MVCC
MVCC： 版本控制  
MVCC 就InnoDB 秒级建立数据快照的能力  
提高并发读写性能，不用加锁就能让多个事务并发读写  

Undolog  
Readview 判断版本链中哪个版本可用  

为什么解决了幻读？  
mvcc为快照读，多个select读的readview都是同一个的，所以不存在幻读  
当前读使用的是间隙锁，锁住一段时间范围  
Innodb默认使用的是可重复读，并且是默认开启间隙锁的  

### 2.3 锁
+ 行锁：  
A事务操作时未提交，此时一个sql来操作这个数据会阻塞，A commit后阻塞的sql会执行成功
+ 表锁：  
索引失效，行锁会升级为表锁
间隙锁：
Id 1 3 5 缺少2 4 6间隙，间隙锁一般发生在范围查找中

查看锁的状态
![img.png](images/img08.png)


## 问题

+ mysql上安装报初始密码不可用

解决：https://blog.csdn.net/themagickeyjianan/article/details/85255396


## 回表
主要发生在使用非聚集索引（二级索引）进行查询时。当查询所需的数据列不完全包含在索引中时，数据库引擎需要先通过索引找到主键值，然后再通过主键值去聚集索引（主键索引）中查找完整的数据行，这个过程称为"回表"。

回表带来的问题
1. 额外的I/O操作：需要两次索引查找（先二级索引，再聚集索引）
2. 性能下降：特别是当需要回表的数据量很大时
3. CPU资源消耗增加：需要处理更多的索引和数据

解决回表的方法
1. 使用覆盖索引（Covering Index）
确保查询的所有字段都包含在索引中，这样就不需要回表：
```sql
-- 原表结构: user(id PK, name, age, address)
-- 原查询: SELECT name, age FROM user WHERE age > 20;

-- 创建覆盖索引
CREATE INDEX idx_age_name ON user(age, name);
```

2. 优化查询字段
只查询必要的字段，避免SELECT *：
```sql
-- 不好的写法
SELECT * FROM user WHERE age > 20;

-- 好的写法
SELECT id, name FROM user WHERE age > 20;
```
3. 使用复合索引
合理设计复合索引的顺序和包含的列：
```sql
-- 对于查询 WHERE a=? AND b=? ORDER BY c
CREATE INDEX idx_a_b_c ON table(a, b, c);
```
4. 使用索引下推（Index Condition Pushdown, ICP）
MySQL 5.6+支持的特性，可以在存储引擎层过滤数据，减少回表次数。

5. 使用聚簇索引优化
对于频繁查询的列，考虑将其设置为聚簇索引（但要注意聚簇索引的选择会影响表的物理存储顺序）。

6. 使用物化视图或汇总表
对于复杂查询，可以预先计算并存储结果。

实际案例分析
假设有一个订单表：

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    user_id BIGINT,
    order_date DATETIME,
    amount DECIMAL(10,2),
    status VARCHAR(20),
    -- 其他字段...
    INDEX idx_user_id (user_id)
);
```
查询用户最近的订单：

```sql
SELECT * FROM orders WHERE user_id = 123 ORDER BY order_date DESC LIMIT 10;
```
这个查询会先通过idx_user_id索引找到所有user_id=123的记录的主键，然后回表获取完整记录，再排序。

优化方案：

```sql
-- 创建覆盖索引
CREATE INDEX idx_user_id_order_date ON orders(user_id, order_date, id);

-- 修改查询（利用覆盖索引）
SELECT id, user_id, order_date, amount, status 
FROM orders 
WHERE user_id = 123 
ORDER BY order_date DESC 
LIMIT 10;
```

## 聚簇索引与非聚簇索引区别
+ 聚簇索引：数据行实际存储在索引的叶子节点中（数据即索引）
    类似于字典本身是按字母顺序排列的

+ 非聚簇索引：叶子节点只存储指向数据的"指针"（主键值或行地址）
    类似于书后的索引只告诉你术语在哪些页码

# 资料
+ 客户端工具：dbeaver

