# javascript

## 1、基础
### 1.1、exports
```text
js文件都可以当成一个模块，每个模块中，都隐含了一个名为module的对象，
module对象中有一个exports属性，这个属性的功能是将模块中的变量暴露给其他模块调用。  
```

### 1.2、

## 2、管理工具
### 2.1、管理工具

### 2.1.1、npm与npx区别
+ npx侧重于执行命令的，执行某个模块命令。虽然会自动安装模块，但是重在执行某个命令。  
  - npx是执行Node软件包的工具，它从 npm5.2版本开始，就与npm捆绑在一起。
+ npm侧重于安装或者卸载某个模块的。重在安装，并不具备执行某个模块的功能。  

### 2.1.2、yarn
macos  
```shell
curl -o- -L https://yarnpkg.com/install.sh | bash
```

windows  
```shell
npm install -g yarn
```