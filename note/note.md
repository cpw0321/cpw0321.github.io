# 笔记

## 一、git

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