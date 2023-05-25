# 笔记

## 1、git

### 1.1、gitconfig为不同的项目配置不同的用户名

.gitconfig中增加
```text
[includeIf "gitdir:D:/test/"]   // 项目路径
        path = ./gitconfig/gitconfig-test // gitconfig-test配置文件路径
```
gitconfig-test
```text
[user]
    name = gitlab
    email = gitlab@gitlab.com
```

### 1.2、git使用token访问
github头像点击
```text
settings->developer settings ->personal access tokens-generate new token
```
git add remote
```text
https://用户名username:token值@github.com/demo.git
```
不写username:也行  

git remote set-url origin xxx.com/demo.git

git remote add origin xxx.com/demo.git



### 1.3、git合并单个commit到指定的分支上
参考：https://blog.csdn.net/weixin_44867717/article/details/120885717  
```shell
git log  //查看提交的日志,复制要合并的那个分支的commit id(简略ID-前8位数)
git checkout 要合并的分支  // 切换到要合并的分支上
git cherry-pick 上面复制的那个要合并的commit id  // 提交该commit到当前分支
git push // 推送到B分支远程仓库
```
### 1.4、git拉取远程分支到本地
参考：https://blog.csdn.net/weixin_44953227/article/details/123730105

两种方式：  
+ git fetch origin 远程分支名:本地分支名
+ git checkout -b 本地分支名 origin/远程分支名



## 2、网站
代理  
```text
https://sdyun.xyz/
https://me.tofly.cyou/
```

chatgpt  
```text
https://dev.yqcloud.top/
```

## 3、计算机相关知识
### 3.1 字节与位
1 Byte = 8 位  

每个十六进制数对应4位二进制数  
0x12345678 = 四个字节   1个字节 = 2 位

### 3.2 编码

## 4、工具类
### 4.1 画图
#### 4.1.1 puml
```text
官网
https://plantuml.com/zh/

在线画图
http://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000
```

### 4.2 生成在线书
#### 4.2.1 gitbook
www.gitbook.com  

#### 4.2.2 readthedocs
readthedocs.org/  


