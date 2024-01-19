# ipfs


## 1、客户端
### 1.1、安装编译go客户端
```text
git clone https://github.com/ipfs/kubo.git

cd kubo
make install

```

### 1.2、运行
```text
ipfs init // 会生成~/.ipfs


echo "hello world" > hello
$ ipfs add hello
# This should output a hash string that looks something like:
# QmT78zSuBmuS4z925WZfrqQ1qHaJ56DQaTfyMUF7F8ff5o
# ipfs.io/ipfs/QmT78zSuBmuS4z925WZfrqQ1qHaJ56DQaTfyMUF7F8ff5o
$ ipfs cat <that hash>

// 连接主网，进行文件上传与数据同步
$ ipfs daemon
```


### 1.3、ipfs-rpc-client
https://github.com/ipfs/kubo/issues/9124  


###1.4 pinata
第三方网关，由于ipfs.io很难访问
国内：https://docs.pinata.cloud/docs/getting-started
控制台：https://app.pinata.cloud/pinmanager