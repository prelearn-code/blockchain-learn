
## Block chain is secured by mining.

## 天然的bug赏金


## ETH

- ASIC resistance.
- memory hard mining puzzle.
- 16M cache，轻节点。
- 1G dataset，全节点。
- GPU是主要的算力。
- ethash.
- 工作量证明转为权益证明。
- 现在已经用权益证明了。
- 预挖矿，预留一部分货币。
- 预售机制。 

### cache生成方式

- 种子节点进行运算得到第一个节点。
- 依次取HASH，后一个元素为前一个元素的HASH。
- 填充满小数组。
- 然后生成一个超大的数组，dataset

 ![](../pic/Pasted%20image%2020240730164434.png)

![](../pic/Pasted%20image%2020240730164504.png)

### dataset生成方式

- 对于区块进行伪随机的读取，遍历256个数值的哈希，得到的哈希值写入dataset.
- 根据header与nonce生成的哈希，在dataset读取两个块。
- 然后根据读取的数据进行计算下一个的位置，也是读取两个数。
- 进行64l轮，计算最后的值是否符合难度。
- 不符合则改变nonce进行重新生成。
- ![](../pic/Pasted%20image%2020240730164619.png)

![](../pic/Pasted%20image%2020240730164628.png)


![](../pic/Pasted%20image%2020240730164517.png)

![](../pic/Pasted%20image%2020240730164526.png)

## LiteCoin

- scrpt：吃内存的哈希函数。
- 链表形式的哈希存储。
- 需要大量的内存。
- time-memory trade off。
- 128k的空间太小了。
- 理念很好。
- 出块速度2.5min。
- 其余与比特币一样。

## 矿工挖矿的函数+轻节点验证的函数
![](../pic/Pasted%20image%2020240730164752.png)

![](../pic/Pasted%20image%2020240730164820.png)


## 挖矿主循环
![](../pic/Pasted%20image%2020240730165304.png)


![](../pic/Pasted%20image%2020240730165319.png)



## 所有伪代码集合
![](../pic/Pasted%20image%2020240730165406.png)

![](../pic/Pasted%20image%2020240730165416.png)
