# 交易树(MPT)

- 只组织当前区块的交易。
- 提供默克尔证明。
- 查询操作。
# 收据树(MPT)

- 由于
# bloom filter

- 高效查找存在性。
- 生成摘要，查看映射函数是否存在。
- 哈希之后映射。 
- 可能会误报，不会不报。
- false positive。
- 不支持删除操作。
- 添加计数器才可以进行参数操作，类似链接。
- 每个交易都有一个bloom fliter。
- 块头有一个总的Bloom filter。
- 快速过滤。
- ![](../pic/Pasted%20image%2020240729223126.png)



# 交易驱动状态机-ETH


# 问题

## 账户状态是否可以只包含这两个账号的状态。

- 不方便找到账户的信息。
- 新建区块需要找到创世区块。
##  

# 代码

## 交易树与收据树的创建
![](../pic/Pasted%20image%2020240729225256.png)

- 交易树的创建。
- 收据树的创建。

## DeriveSha

![[Pasted image 20240729225540.png]]


## trie
![](../pic/Pasted%20image%2020240729225626.png)


# 收据树的结构
![](../pic/Pasted%20image%2020240729225710.png)

- bloom：bloom filter
- logs：根据logs产生相应的bloom filter。

## 区块头的数据结构
![](../pic/Pasted%20image%2020240729225847.png)

## create bloom函数
![](../pic/Pasted%20image%2020240729225947.png)


- 映射三个位置。

## 查询bloom filter
![](../pic/Pasted%20image%2020240729230334.png)
