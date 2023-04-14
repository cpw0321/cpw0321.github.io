# Linux

## 一、命令
### 1、解压缩zip

```text
# 解压
unzip a.zip -d b

### 压缩
zip -r a.zip a.txt
```
### 2、update与upgrade区别

```text
apt-get update：是同步/etc/apt/sources.list和/etc/apt/sources.list.d中列出的软件源的软件包版本，这样才能获取到最新的软件包。
apt-get upgrade：是更新已安装的所有或者指定软件包，升级之后的版本就是本地索引里的，因此，在执行 upgrade 之前一般要执行update，这样安装的才是最新的版本。
```

### 2、cloc统计代码量
```shell
sudo apt-get install cloc
```