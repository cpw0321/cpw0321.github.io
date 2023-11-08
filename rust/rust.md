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


### 2.7 所有权
理解变量在堆栈上分配以及copy和move  

```text
#[derive(Debug, Clone, Copy)]
struct A {
    a: i32,
    b: i32
}
fn main() {
    let a = A {a: 10, b:20};
    let b: A = a;
    println!("{:?}", a);

}
```
struct A 虽然内部成员a和b实现了copy trait但是直接println a会报错，显示move了，所以要显式的指出copy+clone的trait  


+ 解引用会获取所有权


### 2.8、类型
#### 2.8.1、指针
+ 普通指针---长度确定
  - &[u32; 5]
+ 胖指针---长度不确定
  - &[u32] 

#### 2.8.2、类型自动推导
```text
let s = "1";
// println!("{}",s.parse().unwrap()); // 错误，parse是泛型，无法知道是usize还是i32类型，无法自动推导需要指明
println!("{}",s.parse::<i32>().unwrap());
```

#### 2.8.3、trait
面向接口编程，定义和实现分开，实现解耦 ====》go中的interface


#### 2.8.类型转换
+ 基本数据类型转换 as
```text
struct S(i32);  // 元组结构体

trait A {
    fn test(&self, i: i32){
        println!("from trait A {:?}", i);
    }
}

trait B {
    fn test(&self, i: i32){
        println!("from trait B {:?}", i);
    }
}

impl A for S {}
impl B for S {}

fn main(){
    let s = S(1);
    // s.test(2); // 无法知道调用A还是B中test
    <S as A>::test(&s, 10);
    <S as B>::test(&s, 20);
}
```

#### 2.9.错误处理


## 3、编辑器
### 3.1、vscode
#### 3.1.插件
+ crates 包管理
+ rust-analyzer


## 4、资料
### 4.1、书
+ https://course.rs/  
+ https://kaisery.github.io/
