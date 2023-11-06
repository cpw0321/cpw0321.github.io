# rust

## 1、安装
### 1.1、安装
windows下安装
```text
https://www.rust-lang.org/zh-CN/tools/install
```

linux/mac下安装rust和cargo
```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### 1.2、升级
升级到稳定版本  
```shell
rustup update stable
```


## 2、基础语法
### 2.1 ->
return 数据类型

### 2.2 : 
类型注解  
let x: i32 = 42;  

### 2.3 ::
路径分隔符  
MyModule::MyType  

### 2.4 Option<ID>
Option<ID>代表一个可选的ID类型的值，其取值可以是Some(id)或None  

### 2.5 b""
b"" 表示一个空的字节数组，常常用于表示二进制数据或 ASCII 码  


### 2.6 宏macor
+ 声明宏
  - vec! println!
+ 过程宏
  - 自定义派生（derive）
  - 类属性
  - 类函数

要理解宏，需要知道rust的编译过程https://www.cnblogs.com/gaozejie/p/16950786.html  



## 3、编辑器
### 3.1、vscode
#### 3.1.插件
+ crates 包管理
+ rust-analyzer


## 4、资料
### 4.1、书
+ https://course.rs/  
+ https://kaisery.github.io/
