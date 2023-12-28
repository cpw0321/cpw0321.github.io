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

```text
package main

import (
	"fmt"
	"github.com/btcsuite/btcd/btcutil"
	"github.com/btcsuite/btcd/chaincfg"
	"github.com/btcsuite/btcd/chaincfg/chainhash"
	"github.com/btcsuite/btcd/rpcclient"
	"github.com/btcsuite/btcd/txscript"
	"github.com/btcsuite/btcd/wire"
	"log"
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
		fmt.Println("Failed to connect to the Bitcoin node:", err)
		return
	}
	defer client.Shutdown()

	sourceAddrStr := "tb1qr2ssscefkjeehv5kl0alhwj976v6cpxqlskn7n"
	destAddrStr := "tb1qtzqjsdwskw4r52mzek7jmd609rnmtlwf4ev79g"

	sourceAddr, err := btcutil.DecodeAddress(sourceAddrStr, &chaincfg.SigNetParams)
	if err != nil {
		log.Fatal(err)
	}

	unspentTxs, err := client.ListUnspentMinMaxAddresses(1, 9999999, []btcutil.Address{sourceAddr})
	if err != nil {
		log.Fatal(err)
	}

	// 创建比特币交易输入和输出
	totalSenderAmount := btcutil.Amount(0)
	tx := wire.NewMsgTx(wire.TxVersion)
	for _, v := range unspentTxs {
		inTxid, _ := chainhash.NewHashFromStr(v.TxID)
		outpoint := wire.NewOutPoint(inTxid, v.Vout)
		txIn := wire.NewTxIn(outpoint, nil, nil)
		tx.AddTxIn(txIn)
		totalSenderAmount += btcutil.Amount(v.Amount * 1e8)
	}

	// 获取目标地址的比特币脚本
	destAddress, err := btcutil.DecodeAddress(destAddrStr, &chaincfg.SigNetParams)
	if err != nil {
		log.Fatal(err)
	}
	destinationScript, err := txscript.PayToAddrScript(destAddress)
	if err != nil {
		log.Fatal(err)
	}

	// 计算总金额和交易费用
	var fee int64 = 1000
	changeAmount := int64(totalSenderAmount) - 2000 - fee // 减去交易费用，转账2000

	// 添加目标地址作为交易输出
	tx.AddTxOut(wire.NewTxOut(2000, destinationScript))

	if changeAmount > 0 {
		// 添加找零地址作为交易输出
		changeScript, err := txscript.PayToAddrScript(sourceAddr)
		if err != nil {
			log.Fatal(err)
		}

		tx.AddTxOut(wire.NewTxOut(changeAmount, changeScript))
	}

	signedTx, complete, err := client.SignRawTransactionWithWallet(tx)
	if err != nil {
		log.Fatal("sign tx failed", err)
	}
	if !complete {
		log.Fatal("交易未完成签名")
	}
	// 发送交易
	txHash, err := client.SendRawTransaction(signedTx, true)
	if err != nil {
		log.Fatal("send tx failed:", err)
	}
	fmt.Printf("转账成功！交易哈希：%s\n", txHash.String())
	return
}

```