# 一.概述与账户

## 1.ETH概述

ETH与BTC类似，都是通过交易驱动的状态机，但是以太坊的以太币与比特币的记录方式不同，花费方式也不同，比特币需要提供币的来源，同时要维护一个UTXO，以太坊使用账户余额形式来记录账户余额，所以不会出现花费两次的问题。以太坊全节点要维护状态树、交易树、收据树，状态树主要是记录不同账户的状态，记录每个上链的账户的余额信息和其他数据，交易树与收据树是记录交易相关的详细信息与交易结果的信息，无论交易是否成功，都会进行收取gas，即手续费。因为以太坊中有智能合约的存在，账户也分为合约账户与普通账户。

## 2.普通账户生成
### a.私钥生成
- 随机数32字节的随机数据。
- 转换为16进制。
### b.公钥生成
- 256位的数据进行点乘法。
- SECP256k1，为算法。
- 最终得到x与y分别是32字节。
- 最终0x04+x+y即为公钥。
### c.地址生成

- 去掉开头的0x04，进行哈希。
- keccak256。
- 取后20字节，即160位为个人地址。
## 3.合约账户生成
说明：合约账户地址是使用普通账户与其交易的nonce值来确定的数据。

### a.合约地址生成
- 取普通用户的地址去掉前缀+nonce
- 取keccak256，取后20字节。
- 根据前面普通用户的地址生成校验地址。
- 前面地址的数据进行遍历并与合约地址对应。
- 若原账户的地址那一位的数据大小在8-f之间。
- 合约地址的对应位字符要大写，数字不变。

### b.合约账户其他信息
- 可以调用其他合约。
- 有自己的存储空间。
- 有代码。
- 余额。

# 二.以太坊的结构

## 1.区块头

```go
type Header struct {

    ParentHash  common.Hash    `json:"parentHash"       gencodec:"required"`
    UncleHash   common.Hash    `json:"sha3Uncles"       gencodec:"required"`
    Coinbase    common.Address `json:"miner"`
    Root        common.Hash    `json:"stateRoot"        gencodec:"required"`
    TxHash      common.Hash    `json:"transactionsRoot" gencodec:"required"`
    ReceiptHash common.Hash    `json:"receiptsRoot"     gencodec:"required"`
    Bloom       Bloom          `json:"logsBloom"        gencodec:"required"`
    Difficulty  *big.Int       `json:"difficulty"       gencodec:"required"`
    Number      *big.Int       `json:"number"           gencodec:"required"`
    GasLimit    uint64         `json:"gasLimit"         gencodec:"required"`
    GasUsed     uint64         `json:"gasUsed"          gencodec:"required"`
    Time        uint64         `json:"timestamp"        gencodec:"required"`
    Extra       []byte         `json:"extraData"        gencodec:"required"`
    MixDigest   common.Hash    `json:"mixHash"`
    Nonce       BlockNonce     `json:"nonce"`
}
```
- bloom：
- root：状态根哈希。
- TxHash：交易的根哈希。
- ReciptHash：收据的哈希。

## 2.区块体

```go
type Body struct {
    Transactions []*Transaction
    Uncles       []*Header
    Withdrawals  []*Withdrawal `rlp:"optional"`
}
```
- withdrawals：允许验证者进行提取质押币，POW向POS过度的一个重要标志。
- Transactions：交易集合。
- Uncles：最多指明两个叔父，之前未超过8代的叔块也可以。
- 叔块：不进行交易执行，只用发布，有人引用就获取3/4的区块奖励。

## 3.状态结构



# 三.状态树、交易树、数据树

## 1.树的结构介绍

### a.字典树(trie)

通过路径上到叶节点即可通过路径找到对应的数据。
### b.前缀树

通过对于字典树进行前缀压缩，把相同的与节点少的树进行前缀压缩。
### c.默克尔树

与比特币的交易树的根哈希的计算方法类似，为了实现对于整个树的数据的完整性进行校验的。
### d.前缀默克尔树

通过对于前缀进行压缩数据，对于分叉数据进行哈希指针查找，从而实现对于每个叶节点的遍历与查找。

## 2.状态树

说明：状态树是一种MPT数据结构，即前缀默克尔树。状态树的节点历史数据的需要存储，用来回退交易失败的状态。

![](../pic/Pasted%20image%2020240725224548.png)

- 存在状态根哈希，来判断状态是否相同。
- 状态树的大部分内容是不改变的，交易只是对应少部分的信息。
- 
#状态数的压缩节点怎么展开。
## 3.交易树

根据交易的哈希进行计算交易树，与比特币的交易树类似。

## 4.收据树

与交易树类似，对于收据进行哈希，得到根哈希值，放在块头。



# 四.共识机制与挖矿算法

## 1.共识机制

### a.POW共识机制

说明：与比特币的共识机制类似，通过对于区块哈希进行计算到目标值，然后进行部署区块，其他全节点都会根据目标值来判断区块是否合法，从而实现对于区块的交易的共识。
### b.POS共识机制

说明：可以通过提供到一定的以太币作为质押，从而成为验证者，在对于区块共识，可以通过提供的以太币在质押币中的比例作为票权，最终投票超过51%才可以同意区块入链。其中对于投票者有一定的惩罚机制与奖励机制，以保证验证者的诚实性，在确定区块的投票之后，质押者的钱不能够立马取出来，需要通过进行质疑阶段之后才可以收回质押的以太币。
### c.ghost机制

说明：ghost是处理孤块的算法，通过对于无记账权的区块进行处理，从而提高矿工对于挖矿的热情，从而可以提高出块时间为15s左右。对于叔块奖励，

## 2.叔块奖励

### a.被引用区块
- 区块奖励为奖励的7/8。
- 对于叔块的交易不执行。
- 分叉的第一个区块才能被引用。
### b.引用区块

- 最多本区块与叔区块的高度差为7.
- Uncle reward = (Height-uncle + 8 - Height-now)/8。
- 上述是一个叔块的引用奖励，最多可以进行引用两个叔块。
## 3.挖矿算法

说明：以太坊的挖矿需要比较大的内存空间才能够进行挖矿，所以挖矿的方式，主要还是GPU进行挖矿，在专属的芯片代价太大，需要至少1G的内存来生成目标target进行测试。

### a.生成cache
说明：cache每个epoch周期都会增加128B，最高上限为128MB,每个epoch都有一个种子，种子为上一个epoch计算SECP256k1得到的，最初的初始epoch为0常量。
- 每一个数据块都为64字节，即512位。
- 对于种子进行Keccak-512，得到第一个数据块的信息。
- 之后每一个数据块的信息都为前一个数据块进行Keccak-512得到。
- 直到充满整个cache块。
### b.DAG生成
- 根据cache的内容迭代获得。
- 先读取一个数据，然后进行更新获得下一个数据，然后再更新，进行256次，写入DAG.
- 循环上一步，直到把DAG填满。

### c.挖矿
- 根据区块数据进行计算一个数据。
- 在DAG中读取这个数据与下一个数据块。
- 然后计算下一个读取位置，再读取两个数据块。
- 进行128次，然后得到最终的哈希值。
- 若与目标值不匹配，重新调整区块内容，再次计算。
## 4.难度调整\

说明：以太坊的难度调整是每一个区块都会进行调整的，调整原理与比特币基本类似。


