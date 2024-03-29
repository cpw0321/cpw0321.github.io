# nodejs  


参考笔记：
https://brucecai55520.gitee.io/bruceblog/notes/nodejs/node.html

## 1、基础知识

### 1.1、解释
nodejs基于v8的JavaScript的运行环境

### 1.2、框架
+ express
    - 快速构建web应用

+ electron 
    - 构建跨平台桌面应用

+ restify
    - 快速构建api接口项目

### 1.3、知识点
#### 1.3.1 包
+ 内置
+ 自定义
+ 第三方

1 导出包  
module.exports  === exports  
以module.exports暴露的为准  

2 安装包
```text
安装
npm install //  npm i
卸载
npm uninstall
安装dev中
npm install --save-dev  // npm i -D

查看镜像源
npm config get registry
设置镜像源
npm config set registry=https://registry.npmjs.org/

切换包工具
npm i nrm -g
nrm ls
nrm use taobao
```

#### 1.3.2 express使用
```text
const express = require("express");

const app = express();

app.get("/user", function(req, res){
    res.send({
        age: 18,
        name: "jack"
    })
})

app.listen("8080", () => {
    console.log("express starting...");
})
```

项目热启动工具  
```shell
npm i nodemon -g // g 全局安装会安装到 ~/.node中
```

#### 1.3.3 session与jwt
session适合于前端渲染


### 工具包
+ Morgan 是一个 HTTP 请求级别的中间件。它是一个伟大的工具，根据其配置和预设，记录请求以及其他一些信息。调试时非常有帮助，也可以用来创建日志文件

+ express -- json app.use(experss.json())

+ nodemon 热加载
