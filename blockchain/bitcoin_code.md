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
	// 只能list钱包中地址
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


用账户私钥去签名发送交易
```text
func main66() {
	netParams := &chaincfg.SigNetParams
	btcApiClient := mempool.NewClient(netParams)

	privateKeyHex02 := "7e3503d37b624a541815ce378f00f5ba01914708d9c8572ddc7057cc649659de"
	privateKeyByte02, _ := hex.DecodeString(privateKeyHex02)
	privateKeyBytes02 := privateKeyByte02 // 用你自己的私钥替换这里的字节
	privateKey02, publicKey02 := btcec.PrivKeyFromBytes(privateKeyBytes02)
	// 根据公钥生成比特币地址
	addressPubKeyHash01 := btcutil.Hash160(publicKey02.SerializeCompressed())
	address02, err := btcutil.NewAddressPubKeyHash(addressPubKeyHash01, &chaincfg.SigNetParams)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("address: ", address02)

	fromAddr, err := btcutil.DecodeAddress(address02.EncodeAddress(), &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("DecodeAddress err:", err)
		return
	}
	unspentList, err := btcApiClient.ListUnspent(fromAddr)
	if err != nil {
		fmt.Println("ListUnspent err:", err)
		return
	}
	if len(unspentList) == 0 {
		return
	}
	fmt.Println("000000:", len(unspentList))

	// 创建比特币交易输入和输出
	totalSenderAmount := btcutil.Amount(0)
	tx := wire.NewMsgTx(wire.TxVersion)
	for i := range unspentList {
		in := wire.NewTxIn(unspentList[i].Outpoint, nil, nil)
		tx.AddTxIn(in)
		totalSenderAmount += btcutil.Amount(unspentList[i].Output.Value)
	}

	// 获取目标地址的比特币脚本
	destAddress, err := btcutil.DecodeAddress("2MuZUViaTCUr4ia2nsPp7tDDWjMbcjWGqn5", &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("DecodeAddress err:", err)
		return
	}
	destinationScript, err := txscript.PayToAddrScript(destAddress)
	if err != nil {
		fmt.Println("PayToAddrScript err:", err)
		return
	}

	// 计算总金额和交易费用
	var fee int64 = 1000
	changeAmount := int64(totalSenderAmount) - 1000 - fee // 减去交易费用，转账2000

	fmt.Println("0000: ", int64(totalSenderAmount))

	// 添加目标地址作为交易输出
	tx.AddTxOut(wire.NewTxOut(1000, destinationScript))

	var changeScript []byte
	if changeAmount > 0 {
		// 添加找零地址作为交易输出
		changeScript, err = txscript.PayToAddrScript(fromAddr)
		if err != nil {
			fmt.Println("PayToAddrScript err:", err)
			return
		}

		tx.AddTxOut(wire.NewTxOut(changeAmount, changeScript))
	}

	// 构建多签赎回脚本
	for i, v := range tx.TxIn {
		sigScriptB, err := txscript.SignatureScript(tx, i, changeScript, txscript.SigHashAll, privateKey02, true)
		if err != nil {
			fmt.Println("SignatureScript:", err)
			return
		}

		v.SignatureScript = sigScriptB
	}

	a, _ := json.Marshal(tx)
	fmt.Println("1111111:", string(a))
	// 发送交易
	txHash, err := btcApiClient.BroadcastTx(tx)
	if err != nil {
		fmt.Println("BroadcastTx err:", err)
		return
	}
	fmt.Printf("转账成功！交易哈希：%s\n", txHash.String())

}
```


```text
// 多签
package main

import (
	"encoding/hex"
	"encoding/json"
	"fmt"
	"github.com/btcsuite/btcd/btcec/v2"
	"github.com/btcsuite/btcd/btcec/v2/schnorr"
	"github.com/btcsuite/btcd/btcutil"
	"github.com/btcsuite/btcd/chaincfg"
	"github.com/btcsuite/btcd/rpcclient"
	"github.com/btcsuite/btcd/txscript"
	"github.com/btcsuite/btcd/wire"
	"log"
	"test/pkg/btcapi/mempool"
)

func main() {
	netParams := &chaincfg.SigNetParams
	btcApiClient := mempool.NewClient(netParams)

	addres02 := "n4HJET932iJACmnoGRAAKAePRGsjmZkPu7"

	privateKeyHex01 := "7e3503d37b624a541815ce378f00f5ba01914708d9c8572ddc7057cc649659de"
	privateKeyByte, _ := hex.DecodeString(privateKeyHex01)
	privateKeyBytes01 := privateKeyByte // 用你自己的私钥替换这里的字节
	privateKey01, publicKey01 := btcec.PrivKeyFromBytes(privateKeyBytes01)

	addressPubKey01, err := btcutil.NewAddressPubKey(publicKey01.SerializeCompressed(), &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("0000000:", err)
		return
	}

	privateKeyHex02 := "ddcc69506d5c7088078315386fac523d36d2d88c37ab0d56fa6e24501f1a4463"
	privateKeyByte02, _ := hex.DecodeString(privateKeyHex02)
	privateKeyBytes02 := privateKeyByte02 // 用你自己的私钥替换这里的字节
	privateKey02, publicKey02 := btcec.PrivKeyFromBytes(privateKeyBytes02)

	addressPubKey02, err := btcutil.NewAddressPubKey(publicKey02.SerializeCompressed(), &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("1111111111:", err)
		return
	}


	multiSigScript, err := txscript.MultiSigScript([]*btcutil.AddressPubKey{addressPubKey01, addressPubKey02}, 2)
	if err != nil {
		fmt.Println("22222222:", err)
		return
	}

	address, err := btcutil.NewAddressScriptHash(multiSigScript, &chaincfg.SigNetParams)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("mulsig address:", address.EncodeAddress())

	mulsigAddr, err := btcutil.DecodeAddress(address.EncodeAddress(), &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("3333333:", err)
		return
	}
	unspentList, err := btcApiClient.ListUnspent(mulsigAddr)
	if err != nil {
		fmt.Println("44444444:", err)
		return
	}
	if len(unspentList) == 0 {
		fmt.Println("55555555")
		return
	}
	fmt.Println("000000:", len(unspentList))

	// 创建比特币交易输入和输出
	totalSenderAmount := btcutil.Amount(0)
	tx := wire.NewMsgTx(wire.TxVersion)

	in := wire.NewTxIn(unspentList[0].Outpoint, nil, nil)
	tx.AddTxIn(in)
	totalSenderAmount += btcutil.Amount(unspentList[0].Output.Value)
	fmt.Println("111:", int64(totalSenderAmount))

	// 获取目标地址的比特币脚本
	destAddress, err := btcutil.DecodeAddress(addres02, &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("666666666:", err)
		return
	}
	destinationScript, err := txscript.PayToAddrScript(destAddress)
	if err != nil {
		fmt.Println("777777777:", err)
		return
	}
	//

	fmt.Println("0000: ", int64(totalSenderAmount))


	// 构建多签赎回脚本
	sig01, err := txscript.RawTxInSignature(tx, 0, multiSigScript, txscript.SigHashAll, privateKey01)
	if err != nil {
		return
	}

	sig02, err := txscript.RawTxInSignature(tx, 0, multiSigScript, txscript.SigHashAll, privateKey02)
	if err != nil {
		return
	}

	// 重点AddOp(txscript.OP_FALSE)
	signatureScript, err := txscript.NewScriptBuilder().AddOp(txscript.OP_FALSE).AddData(sig01).AddData(sig02).AddData(multiSigScript).Script()
	if err != nil {
		panic(err)
	}
	tx.TxIn[0].SignatureScript = signatureScript

	// 发送交易
	//txHash, err := btcApiClient.BroadcastTx(tx)
	//if err != nil {
	//	fmt.Println("1234567:", err)
	//	return
	//}
	//fmt.Printf("转账成功！交易哈希：%s\n", txHash.String())

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

	// 发送交易
	txHash, err := client.SendRawTransaction(tx, true)
	if err != nil {
		log.Fatal("555555555:", err)
	}
	fmt.Printf("转账成功！交易哈希：%s\n", txHash.String())
	return

}

```


```text
// schnorr签名交易
func main() {
	netParams := &chaincfg.SigNetParams
	btcApiClient := mempool.NewClient(netParams)

	//privateKeyHex02 := "4ef5cb1fd5afd08ca9acbe5d077d89f1e095f67616a326d368851e57a92d1bab"
	privateKeyHex02 := "7e3503d37b624a541815ce378f00f5ba01914708d9c8572ddc7057cc649659de"
	privateKeyByte02, _ := hex.DecodeString(privateKeyHex02)
	privateKeyBytes02 := privateKeyByte02 // 用你自己的私钥替换这里的字节
	privateKey02, _ := btcec.PrivKeyFromBytes(privateKeyBytes02)
	//fromAddress, err := btcutil.NewAddressPubKey(publicKey02.SerializeCompressed(), &chaincfg.SigNetParams)
	//if err != nil {
	//	fmt.Println("0000000:", err)
	//	return
	//}
	utxoTaprootAddress, err := btcutil.NewAddressTaproot(schnorr.SerializePubKey(txscript.ComputeTaprootKeyNoScript(privateKey02.PubKey())), netParams)
	if err != nil {
		fmt.Println("0000000:", err)
		return
	}
	fromAddr, err := btcutil.DecodeAddress(utxoTaprootAddress.EncodeAddress(), &chaincfg.SigNetParams)
	if err != nil {
		fmt.Println("DecodeAddress err:", err)
		return
	}
	// tb1plfmp4605cr79pnrrev8x2pxt3ngwx5xpffsllz7p57mfp57zmupsmkg20n
	fmt.Println("address: ", fromAddr)

	unspentList, err := btcApiClient.ListUnspent(fromAddr)
	if err != nil {
		fmt.Println("ListUnspent err:", err)
		return
	}
	if len(unspentList) == 0 {
		fmt.Println("1111111111")
		return
	}
	fmt.Println("000000:", len(unspentList))

	// 创建比特币交易输入和输出
	totalSenderAmount := btcutil.Amount(0)
	tx := wire.NewMsgTx(wire.TxVersion)
	in := wire.NewTxIn(unspentList[0].Outpoint, nil, nil)
	tx.AddTxIn(in)
	totalSenderAmount = btcutil.Amount(unspentList[0].Output.Value)

	// 获取目标地址的比特币脚本
	//toAddress := "tb1psgsl0n50gtfmnjf9rmgw38rwc6htjjqh68w7rc289tc56wp8y6mqx8t0sz"
	//destAddress, err := btcutil.DecodeAddress(toAddress, &chaincfg.SigNetParams)
	//if err != nil {
	//	fmt.Println("DecodeAddress err:", err)
	//	return
	//}
	//destinationScript, err := txscript.PayToAddrScript(destAddress)
	//if err != nil {
	//	fmt.Println("PayToAddrScript err:", err)
	//	return
	//}

	// 计算总金额和交易费用
	var fee int64 = 1000
	changeAmount := int64(totalSenderAmount) - 600 - fee // 减去交易费用，转账2000

	fmt.Println("0000: ", int64(changeAmount))

	// 添加目标地址作为交易输出
	//tx.AddTxOut(wire.NewTxOut(600, destinationScript))

	var changeScript []byte
	if changeAmount > 0 {
		// 添加找零地址作为交易输出
		changeScript, err = txscript.PayToAddrScript(fromAddr)
		if err != nil {
			fmt.Println("PayToAddrScript err:", err)
			return
		}
		tx.AddTxOut(wire.NewTxOut(changeAmount, changeScript))
	}

	// 这里*
	a := txscript.NewMultiPrevOutFetcher(map[wire.OutPoint]*wire.TxOut{
		*unspentList[0].Outpoint: {
			PkScript: changeScript,
			Value:    unspentList[0].Output.Value,
		},
	})
	sigHashes := txscript.NewTxSigHashes(tx, a)

	witness, err := txscript.TaprootWitnessSignature(tx, sigHashes,
		0, unspentList[0].Output.Value, changeScript, txscript.SigHashDefault, privateKey02)

	fmt.Println("000: ", len(witness))
	tx.TxIn[0].Witness = witness

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

	// 发送交易
	txHash, err := client.SendRawTransaction(tx, true)
	if err != nil {
		log.Fatal("555555555:", err)
	}
	fmt.Printf("转账成功！交易哈希：%s\n", txHash.String())

}
```