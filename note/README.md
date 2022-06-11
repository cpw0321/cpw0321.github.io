#笔记

## git

### 1、gitconfig为不同的项目配置不同的用户名

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
