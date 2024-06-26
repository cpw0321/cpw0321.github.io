# javascript

## 1、基础
### 1.1、exports
```text
js文件都可以当成一个模块，每个模块中，都隐含了一个名为module的对象，
module对象中有一个exports属性，这个属性的功能是将模块中的变量暴露给其他模块调用。  
```

### 1.2、工具

## 2、管理工具
### 2.1、管理工具

### 2.1.1、npm与npx区别
+ npx侧重于执行命令的，执行某个模块命令。虽然会自动安装模块，但是重在执行某个命令。  
  - npx是执行Node软件包的工具，它从 npm5.2版本开始，就与npm捆绑在一起。
+ npm侧重于安装或者卸载某个模块的。重在安装，并不具备执行某个模块的功能。  

### 2.1.2、yarn
JavaScript包管理工具，用于管理项目中的依赖关系 （Facebook和Google等公司共同参与开源）


macos  
```shell
curl -o- -L https://yarnpkg.com/install.sh | bash
```

windows  
```shell
npm install -g yarn
```

### 1.3 专业术语理解
+ DOM：文档对象模型（Document Object Model），用来操作网页内容的功能。
+ BOM：浏览器对象模型(Brower Object Model)，用于操作浏览器。  

+ jQuery: 是一个轻量级的 JavaScript 库，对 DOM 操作的封装和扩展  


### 1.4 浏览器中一些常用的对象
#### 1.4.1、播放速度
```text
document.querySelector('video').playbackRate=3
```




## 资料
+ 谷歌插件教程: https://xieyufei.com/2021/11/09/Chrome-Plugin.html#%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E5%B0%8F%E6%8F%92%E4%BB%B6`

+ 获取假数据json: https://dummyjson.com/

