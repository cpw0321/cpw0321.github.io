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
git remote set-url origin xxx.com/demo.git

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
```text
https://sdyun.xyz/
https://me.tofly.cyou/
```