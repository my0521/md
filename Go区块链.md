---
title: Go区块链
date: 2020-07-01 20:00:48
categories: 
- 区块链
tags:
- 区块链
---

Go语言编程实现一个简单的区块链

<!-- more -->

## 区块节点`block.go`
1. 导入包
``` monkey
import (
	"bytes"
	"crypto/sha256"
	"strconv"
	"time"
)
```
2. 定义区块结构
```go
type Block struct{
	Timestamp 		int64   //区块创建时间戳
	Data 			[]byte  //区块包含的数据
	PrevBlockHash 	[]byte  //前一个区块的hash值
	Hash 			[]byte  //区块自身的Hash值，用于校验区块数据有效
	Nonce           int     //证明工作量
}
```
3. 生成区块
``` groovy
func  NewBlock(data string,prevBlockHash []byte) *Block{
	block := &Block{Timestamp:time.Now().Unix(),Data:[]byte(data),PrevBlockHash:prevBlockHash,Hash:[]byte{}}
	//工作量计算
	pow := NewProofofWork(block)
	nonce,hash := pow.Run()
	block.Hash =hash[:]
	block.Nonce = nonce
	//block.SetHash()
	return block
}
```
4. 计算区块Hash
``` scss
func (b *Block) SetHash() {
	timestamp := []byte(strconv.FormatInt(b.Timestamp,10))
	headers := bytes.Join([][]byte{b.PrevBlockHash,b.Data,timestamp},[]byte{})
	hash := sha256.Sum256(headers)
	b.Hash = hash[:]
}
```
5. 生成创世区块
``` scss
func NewGenesisBlock() *Block {
	return NewBlock("Genesis Block",[]byte{})
}
```

## 区块链 blockchain.go
1. 区块链结构
``` haskell
type Blockchain struct{
	Blocks []*Block
}
```
2. 新增区块
``` haskell
func (bc * Blockchain) AddBlock(data string){
	preBlock :=bc.Blocks[len(bc.Blocks)-1]
	newBlock := NewBlock(data,preBlock.Hash)
	bc.Blocks =append(bc.Blocks,newBlock)
}
```
3. 创世区块
``` scss
func NewBlockchain() *Blockchain {
	return &Blockchain{[]*Block{NewGenesisBlock()}}
}
```

## 工作量证明
1. 导入依赖包
``` monkey
import (
	"bytes"
	"crypto/sha256"
	"fmt"
	"math"
	"math/big"
)
```

2. 工作量证明
``` go
//工作量证明数值的最大值，从0开始计算工作量
var(
	maxNonce =math.MaxInt64
)
//生成的hash值的需求，前8位是0
const targetBits = 8
type ProofofWork struct{
	block  *Block
	target *big.Int
}
func NewProofofWork(b *Block) *ProofofWork  {
	target := big.NewInt(1)
	target.Lsh(target,uint(256 - targetBits))//lsh左移，前targetBits个字节编程0
	pow := &ProofofWork{b, target}
	return pow
}
func (pow *ProofofWork) prePareData(nonce int)  []byte{
	data := bytes.Join(
		[][]byte{
			pow.block.PrevBlockHash,
			pow.block.Data,
			IntToHex(pow.block.Timestamp),
			IntToHex(int64(targetBits)),
			IntToHex(int64(nonce)),//可变  从0编导最大值
		},
		[]byte{
		},
	)
	return data
}
//开始工作，即找满足节点hash值要求的nonce值
func (pow *ProofofWork) Run() (int,[]byte)  {
	var hashInt big.Int
	var hash [32]byte
	nonce := 0
	fmt.Printf("\nMining the block contraining \"%s\" \n",pow.block.Data)
	for nonce< maxNonce{
		data := pow.prePareData(nonce)
		hash =sha256.Sum256(data)
		fmt.Printf("\r%x",hash)
		hashInt.SetBytes(hash[:])//把hash转换为最大整数

		if hashInt.Cmp(pow.target) == -1{
			break
		}else {
			nonce++//从0增长到64位最大值
		}
	}
	return nonce ,hash[:]
}

//工作量验证
func (pow * ProofofWork) Validate() bool  {
	var hashInt big.Int
	data := pow.prePareData(pow.block.Nonce)
	hash := sha256.Sum256(data)
	hashInt.SetBytes(hash[:])
	isValid := hashInt.Cmp(pow.target) == -1
	return  isValid
}
```

## 工具包utils.go
``` go
import (
	"bytes"
	"crypto/sha256"
	"encoding/binary"
	"log"
)

func IntToHex(num int64)  []byte  {
	buff := new(bytes.Buffer)
	err := binary.Write(buff,binary.BigEndian,num)
	if(err != nil){
		log.Panic(err)
	}
	return buff.Bytes()
}
func DataToHash(data []byte) []byte  {
	hash := sha256.Sum256(data)
	return  hash[:]

}
```

## 使用

``` scss
func main()  {
	bc := core.NewBlockchain()
	bc.AddBlock("Send 1 BTC to MY")
	bc.AddBlock("Send 2 more BTC to MY")

	for  _, block:= range bc.Blocks{
		fmt.Println()
		fmt.Printf("Prev  Hash : %x\n",block.PrevBlockHash)
		fmt.Printf("      Data : %s\n",block.Data)
		fmt.Printf("     Nonce : %x\n",block.Nonce)
		fmt.Printf("      Hash : %x\n",block.Hash)
		pow := core.NewProofofWork(block)
		fmt.Printf("       Pow :%s\n" ,strconv.FormatBool(pow.Validate()))
		fmt.Println()
	}
}
```

	Mining the block contraining "Genesis Block" 
	004d6b8f37b16308e9f6effe9ad051ddf04891a0e681179f8a45e86f0ebf8af3
	Mining the block contraining "Send 1 BTC to MY" 
	0003407558dccf920b0191bc087d9a18bba718fb771f1cdc56cd6c963c1fc781
	Mining the block contraining "Send 2 more BTC to MY" 
	009154697c9df29703c13b1d16ba33eccbb0c9a47af5815d982e9dcb96e10733
	Prev  Hash : 
	      Data : Genesis Block
	     Nonce : 189
	      Hash : 004d6b8f37b16308e9f6effe9ad051ddf04891a0e681179f8a45e86f0ebf8af3
	       Pow :true
	Prev  Hash : 004d6b8f37b16308e9f6effe9ad051ddf04891a0e681179f8a45e86f0ebf8af3
	      Data : Send 1 BTC to MY
	     Nonce : f1
	      Hash : 0003407558dccf920b0191bc087d9a18bba718fb771f1cdc56cd6c963c1fc781
	       Pow :true
	Prev  Hash : 0003407558dccf920b0191bc087d9a18bba718fb771f1cdc56cd6c963c1fc781
	      Data : Send 2 more BTC to MY
	     Nonce : 29b
	      Hash : 009154697c9df29703c13b1d16ba33eccbb0c9a47af5815d982e9dcb96e10733
	       Pow :true









