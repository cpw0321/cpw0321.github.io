# 代码参考

## 1、bitcoin
### 1.1、bitcoin发送交易
```text
package main

import (
	"fmt"
	"github.com/btcsuite/btcd/btcjson"
	"github.com/btcsuite/btcd/chaincfg"
	"log"

	"github.com/btcsuite/btcd/btcutil"
	"github.com/btcsuite/btcd/rpcclient"
)

func main() {
	// 连接到比特币节点的RPC服务器
	connCfg := &rpcclient.ConnConfig{
		Host:         "127.0.0.1:8332",
		User:         "test",
		Pass:         "123456",
		HTTPPostMode: true,
		DisableTLS:   true, // 如果比特币节点未启用SSL/TLS，需要设置为true
	}
	client, err := rpcclient.New(connCfg, nil)
	if err != nil {
		log.Fatal(err)
	}
	defer client.Shutdown()

	// 源地址和目标地址
	sourceAddrStr := "tb1qr2ssscefkjeehv5kl0alhwj976v6cpxqlskn7n"
	destAddrStr := "tb1qtzqjsdwskw4r52mzek7jmd609rnmtlwf4ev79g"

	//var defaultNet chaincfg.Params
	//networkName := NetworkName
	//switch networkName {
	//case "signet":
	//	defaultNet = chaincfg.SigNetParams
	//case "testnet":
	//	defaultNet = chaincfg.TestNet3Params
	//case "main":
	//	defaultNet = chaincfg.MainNetParams
	//case "simnet":
	//	defaultNet = chaincfg.SimNetParams
	//}

	// 获取源地址的未花费交易输出（UTXO）
	sourceAddr, err := btcutil.DecodeAddress(sourceAddrStr, &chaincfg.SigNetParams)
	if err != nil {
		log.Fatal(err)
	}
	unspentTxs, err := client.ListUnspentMinMaxAddresses(1, 9999999, []btcutil.Address{sourceAddr})
	if err != nil {
		log.Fatal(err)
	}

	// 计算输入数量和找零
	var inputs []btcjson.TransactionInput
	totalInputAmount := int64(0)
	for _, unspentTx := range unspentTxs {
		totalInputAmount += int64(unspentTx.Amount * 1e8)
		inputs = append(inputs, btcjson.TransactionInput{
			Txid: unspentTx.TxID,
			Vout: unspentTx.Vout,
		})
	}
	changeAmount := totalInputAmount - 200000 - amount // 手续费设为 0.0002 BTC
	if changeAmount > 0 {
		changeAddr, err := btcutil.DecodeAddress(sourceAddrStr, &chaincfg.MainNetParams)
		if err != nil {
			log.Fatal(err)
		}
		destAddr, err := btcutil.DecodeAddress(destAddrStr, &chaincfg.MainNetParams)
		if err != nil {
			log.Fatal(err)
		}
		outputs := map[btcutil.Address]btcutil.Amount{
			changeAddr: btcutil.Amount(changeAmount),
			destAddr:   btcutil.Amount(100000),
		}
		rawTx, err := client.CreateRawTransaction(inputs, outputs, nil)
		if err != nil {
			log.Fatal(err)
		}

		// 签名交易
		signedTx, complete, err := client.SignRawTransactionWithWallet(rawTx)
		if err != nil {
			log.Fatal(err)
		}
		if !complete {
			log.Fatal("交易未完成签名")
		}
		// 发送交易
		txHash, err := client.SendRawTransaction(signedTx, true)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("转账成功！交易哈希：%s\n", txHash.String())
		return
	}


	log.Fatal("无法计算找零金额")
}

```
