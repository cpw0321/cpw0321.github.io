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
ipfs add hello
# This should output a hash string that looks something like:
# QmT78zSuBmuS4z925WZfrqQ1qHaJ56DQaTfyMUF7F8ff5o
ipfs cat <that hash>
```


