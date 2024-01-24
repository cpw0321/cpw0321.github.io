# Arweave

## 1. 环境

+ 本地测试链：https://github.com/textury/arlocal
docker启动
```sh
docker run --name arlocal -p 1984:1984 textury/arlocal
```

+ go-sdk：https://github.com/everFinance/goar/
+ explorer: https://viewblock.io/arweave/stats
+ wallet: https://www.arconnect.io/
+ api-doc: https://docs.arweave.org/developers/arweave-node-server/http-api

## 2. 测试代码
```
package main

import (
	"fmt"
	"github.com/everFinance/goar"
	"github.com/everFinance/goar/types"
	"log"
)

func main() {
	//url := "http://arweave.net"
	url := "http://127.0.0.1:1984"
	wallet, err := goar.NewWalletFromPath("./bbb.json", url)
	if err != nil {
		fmt.Println("000:", err)
		return
	}

	info, err := wallet.Client.GetInfo()
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("Peers:", info.Peers)
	fmt.Println("Height:", info.Height)
	fmt.Println("Current:", info.Current)
	fmt.Println("Blocks:", info.Blocks)

	balance, _ := wallet.Client.GetWalletBalance("Sqixllju4GuP9oxgPvD4iLJW3lE8NlkDj9ZnUz0a_Fk")
	fmt.Println("balance:", balance)
	fmt.Println("signer", wallet.Signer.Address)
	
	// 上传数据到Arweave
	data := []byte("Hello, 20240124")
	tags := []types.Tag{
		{Name: "test", Value: "20240124"},
	}
	tx, err := wallet.SendDataSpeedUp(data, tags, 10)
	if err != nil {
		fmt.Println("err:", err)
		return
	}
	fmt.Println("tx:", tx.ID)

	dataByte, err := wallet.Client.GetTransactionData("dsB9ycJPIaAMgpXkKbtnLmSnQKTvqHqcto8SYkzwatc")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("data:", string(dataByte))
}

```