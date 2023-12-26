# 代码参考

## 1、eth
### 1.1、eth事件event监听
```text
package main

import (
	"context"
	"encoding/json"
	"github.com/ethereum/go-ethereum/core/types"
	"log"
	"math/big"
	"time"

	"github.com/ethereum/go-ethereum"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/ethclient"
)

const(
	url = "http://127.0.0.1:8545"
	address = ""  // 合约地址
	deposit = "function hash"   // 浏览器上可以获取到deposit函数的hash
	withdraw = "function hash" // 浏览器上可以获取到withdraw函数的hash
)

var (
	addresses = []common.Address{
		common.HexToAddress(address),
	}
	topics = [][]common.Hash{
		{
			common.HexToHash(deposit),
			common.HexToHash(withdraw),
		},
	}
)

func main() {
	client, err := ethclient.Dial(url)
	if err != nil {
		log.Fatal(err)
	}

	var lastBlock int64
	for {
		// 获取最新的区块头信息
		header, err := client.HeaderByNumber(context.Background(), nil)
		if err != nil {
			log.Fatal(err)
		}

		latestBlock := header.Number.Int64()
		log.Println("height", latestBlock)

		if latestBlock <= lastBlock {
			time.Sleep(time.Second * 10)
			continue
		}
		for i := lastBlock + 1; i <= latestBlock; i++ {
			query := ethereum.FilterQuery{
				FromBlock: big.NewInt(i),
				ToBlock:   big.NewInt(i),
				Topics:    topics,
				Addresses: addresses,
			}
			logs, err := client.FilterLogs(context.Background(), query)
			if err != nil {
				log.Println("failed to fetch block", "height", i, "err", err)
				break
			}
			for _, vlog := range logs {
				//log.Println("vlog:", vlog)
				eventHash := common.BytesToHash(vlog.Topics[0].Bytes())
				if eventHash == common.HexToHash(deposit) {
					data := DepositEvent{
						Sender:    TopicToAddress(vlog, 1),
						ToAddress: TopicToAddress(vlog, 2),
						Amount:    DataToBigInt(vlog, 0),
					}
					value, _ :=json.Marshal(&data)
					log.Println("deposit event: ", string(value))

				} else if eventHash == common.HexToHash(withdraw) {
					data := WithdrawEvent{
						FromAddress: TopicToAddress(vlog, 1),
						ToAddress:   DataToString(vlog, 0),
						Amount:      DataToBigInt(vlog, 1),
					}
					value, _ :=json.Marshal(&data)
					log.Println("withdraw event: ", string(value))
					//log.Println("11111111:", common.Bytes2Hex(vlog.Data))
				}
			}
			//log.Println("curr: ", i)
			lastBlock = i
		}
	}
}


func TopicToHash(vLog types.Log, index int64) common.Hash {
	return common.BytesToHash(vLog.Topics[index].Bytes())
}

func TopicToAddress(vLog types.Log, index int64) common.Address {
	return common.BytesToAddress(vLog.Topics[index].Bytes())
}

func DataToBigInt(vLog types.Log, index int64) *big.Int {
	start := 32 * index
	return big.NewInt(0).SetBytes(vLog.Data[start : start+32])
}

type WithdrawEvent struct {
	FromAddress common.Address
	ToAddress   string
	Amount      *big.Int
}

type DepositEvent struct {
	Sender    common.Address
	ToAddress common.Address
	Amount    *big.Int
}

func DataToString(vLog types.Log, index int64) string {
	start := 32 * index
	offset := big.NewInt(0).SetBytes(vLog.Data[start : start+32]).Int64()
	length := big.NewInt(0).SetBytes(vLog.Data[offset : offset+32]).Int64()
	return string(vLog.Data[offset+32 : offset+32+length])
}
```


### 1.2、eth合约调用
```text
package main

import (
	"bytes"
	"context"
	"crypto/ecdsa"
	"fmt"
	"log"
	"math/big"

	"github.com/ethereum/go-ethereum/accounts/abi"
	"github.com/ethereum/go-ethereum/accounts/abi/bind"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/core/types"
	"github.com/ethereum/go-ethereum/crypto"
	"github.com/ethereum/go-ethereum/ethclient"
)

var abiStr = `abi数据`
var (
	url              = "http://127.0.0.1:8545"
	// 合约地址
	address          = ""
	// 私钥不需要0x,有的话自己传参时处理一下
	PRIVATE_KEY_TEST = ""
)

func main() {
	// 连接到以太坊网络
	client, err := ethclient.Dial(url)
	if err != nil {
		log.Fatal(err)
	}

	// 合约地址
	contractAddress := common.HexToAddress(address)

	// 创建发送交易的账户
	privateKey, err := crypto.HexToECDSA(PRIVATE_KEY_TEST)
	if err != nil {
		log.Fatal("11111", err)
	}
	publicKey := privateKey.Public()
	publicKeyECDSA, ok := publicKey.(*ecdsa.PublicKey)
	if !ok {
		log.Fatal("error casting public key to ECDSA")
	}
	fromAddress := crypto.PubkeyToAddress(*publicKeyECDSA)

	// 构建未签名的交易
	nonce, err := client.PendingNonceAt(context.Background(), fromAddress)
	if err != nil {
		log.Fatal(err)
	}
	gasPrice, err := client.SuggestGasPrice(context.Background())
	if err != nil {
		log.Fatal(err)
	}
	gasLimit := uint64(300000) // 设置适当的gas限制

	// 构造方法调用数据
	contractAbi, err := abi.JSON(bytes.NewReader([]byte(abiStr)))
	if err != nil {
		fmt.Println("abi.JSON error ,", err)
		return
	}
	// 将data设置为Deposit函数的ABI编码结果
	// 转30000
	amount, _ := new(big.Int).SetString("30000", 10)
	toAddress := "0x6e4703bd224D03Ed1a88Aac47D0244D3599F0C07"
	data, err := contractAbi.Pack("deposit", common.HexToAddress(toAddress), amount)
	if err != nil {
		fmt.Println("contractAbi.Pack error:", err)
		return
	}

	tx := types.NewTx(&types.LegacyTx{
		Nonce:    nonce,
		To:       &contractAddress,
		Value:    big.NewInt(0),
		Gas:      gasLimit,
		GasPrice: gasPrice,
		Data:     data,
	})

	// 对交易进行签名
	chainID, err := client.ChainID(context.Background())
	if err != nil {
		log.Fatal(err)
	}
	signedTx, err := types.SignTx(tx, types.NewEIP155Signer(chainID), privateKey)
	if err != nil {
		log.Fatal(err)
	}

	// 发送交易到以太坊网络
	err = client.SendTransaction(context.Background(), signedTx)
	if err != nil {
		log.Fatal(err)
	}

	// 等待交易确认
	receipt, err := bind.WaitMined(context.Background(), client, signedTx)
	if err != nil {
		log.Fatal(err)
	}
	log.Println("status:", receipt.Status)
}

```


```text
eth_call 只能是获取只读数据，其他合约写入数据还是需要调用SendTransaction
https://blog.csdn.net/hitpter/article/details/134083805

package main

import (
	"bytes"
	"context"
	"crypto/ecdsa"
	"fmt"
	"github.com/ethereum/go-ethereum"
	"github.com/ethereum/go-ethereum/accounts/abi"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/crypto"
	"github.com/ethereum/go-ethereum/ethclient"
	"log"
	"math/big"
)

var abiStr = ``
var (
	url = "http://127.0.0.1:8123"
	// 合约地址
	address = ""
	// 私钥不需要0x,有的话自己传参时处理一下
	PRIVATE_KEY_TEST = ""
)

func main() {
	// 连接到以太坊网络
	client, err := ethclient.Dial(url)
	if err != nil {
		log.Fatal("Dial failed:", err)
	}
	// 合约地址
	contractAddress := common.HexToAddress(address)

	// 创建发送交易的账户
	privateKey, err := crypto.HexToECDSA(PRIVATE_KEY_TEST)
	if err != nil {
		log.Fatal("hex err:", err)
	}
	publicKey := privateKey.Public()
	publicKeyECDSA, ok := publicKey.(*ecdsa.PublicKey)
	if !ok {
		log.Fatal("error casting public key to ECDSA")
	}
	fromAddress := crypto.PubkeyToAddress(*publicKeyECDSA)

	amount, _ := new(big.Int).SetString("100000", 10)
	toAddress := "0x6e4703bd224D03Ed1a88Aac47D0244D3599F0C07"
	value := [32]byte{}
	copy(value[:], "1c7fd15")

	// 构造方法调用数据
	contractAbi, err := abi.JSON(bytes.NewReader([]byte(abiStr)))
	if err != nil {
		fmt.Println("abi.JSON error ,", err)
		return
	}
	data, err := contractAbi.Pack("deposit", value, common.HexToAddress(toAddress), amount)
	if err != nil {
		fmt.Println("contractAbi.Pack error:", err)
		return
	}
	callMsg := ethereum.CallMsg{
		From: fromAddress,
		To:   &contractAddress,
		Data: data,
	}

	_, err = client.CallContract(context.Background(), callMsg, nil)
	if err != nil {
		fmt.Println("CallContract err:", err)
	}

}
```