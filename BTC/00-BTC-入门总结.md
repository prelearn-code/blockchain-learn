# 一：比特币密码学与数据结构

## 1.哈希函数

### a. 常用哈希
- sha256
- RIPEMD-160
### b.使用途径

- 产生公钥。
- 交易进行哈希。
- 默克尔树生成。
- 跟哈希的生成与验证。
- 区块的哈希作为下一个区块的输入，验证区块是否合法。
## 2.非对称加解密

### a.使用的算法
- ECDSA
### b.使用的途径

- 公钥生成。
- 签名，用于脚本的验证，确保花费区块时的合法性。
- 验证交易的来源是否合法，对于交易需要进行签名,其中公钥与签名都会放在区块中。
## 3.哈希链表

通过前一个区块的哈希进行作为本次区块计算哈希值的一个输入进行计算本次区块的哈希值。
## 4.默克尔树

通过对于交易进行两两哈希，生成默克尔树，最终生成一个跟哈希值，可以通过全节点提供的某个交易的根哈希路径上的交易哈希可以判断交易是否合法。

# 二.区块与交易信息

## 1.区块信息
![[hexo/public/pic/Pasted image 20240807150054.png]]

- 855743：区块高度即为当前区块在公链中的第几个。
- 时间：区块产生的时间。
- 大小：区块的大小。
- 交易费：所有交易的交易费用。
- 确认数：本条链后有几个块。
- 转账总金额：交易所有的转账金额的总和。
- 挖矿难度。
- 交易数：交易数量。
- 上一个区块：上一个区块的哈希值，形成一个链式结构。
- 默克尔树根哈希：交易的默克尔树的跟哈希。
- coinbase：铸币用户，可以随意写，币是凭空产生的，在输出中写对就行。
## 2.交易信息

### a.铸币交易
![[hexo/public/pic/Pasted image 20240807151207.png]]

- coinbase：可以随意写。
- 输入地址为随意可修改的信息。
- 输出地址为获得记账权的账户。
- 输出的总金额为本区块的交易费与铸币奖励的总和。
- 本次交易没有交易费用。
### b.普通交易

![[hexo/public/pic/Pasted image 20240807151713.png]]

- wintness：签名+公钥地址。
- nSequence：交易的唯一标志，防止重放攻击。
- 类型：交易脚本的类型。
- ScriptPubKey：交易的公钥地址。
# 三.区块共识实现与挖矿

## 1.区块共识

### a.实现方式

通过算力实现对于区块的写入，算力越强，尝试的nonce次数越多，就越有可能获取到写入区块的能力，算是基于工作量证明的方式。

## 2.挖矿

介绍：即通过对于交易的组合与区块中某些可修改进行进行调整，从而获取满足目标值的哈希值，才能够获取记账权，从而进行区块上链。

### a.基本原理

- 通过对于交易的组合哈希与可修改的数据进行修改，生成一个去区块的哈希，以满足最终的目标值的满足条件。
- 每次尝试的结果都是一样的，因为哈希计算没有什么相关性。
- 算力强的节点，也只是进行的次数比较多才能够有更高的获得记账权的可能。

### b.可调整的数值

说明：根据上述2.1与2.2.a中的信息可以获取相关的详细信息。

- 交易的重组生成的默克尔树的跟哈希不同。
- 时间可以在秒级的范围进行微调，以为比特币的出块时间为15分钟大约。
- nonce：随机值，是主要的调整的方式。
- 铸币交易的输入信息：不会进行信息验证，可以在需要的情况下进行随机写入信息。
- 铸币交易的输出信息：只要不转币，可以对于输出账户进行随意写入，或者写入需要的信息。

### c.挖矿

说明：通过调整可变的数值，获取区块的整体哈希值，不断对于可变数值进行随机调整，从而实现目标值的范围，从而获取合法的记账权，区块的哈希值小于等于目标值才是算符合条件的合法区块。

### d.区块发布

说明：对于所有的交易先进行脚本执行，确认交易的合法性
## 3.难度调整

- 每2016个区块进行一次难度调整，即2周左右调整一次。
- 一般的出块时间都在15分钟左右。
- 区块哈希 <= 目标值为合法
- difficult = difficult * target_time/real_time
- target = target * real_time/target_time 


# 四.账户与交易

## 1.账户生成
### a.私钥生成

- 获取256bit的随机值，即为32字节的随机值作为哈希值。
### b.公钥生成

- 根据私钥进行点乘法，获取目标点。
- 非压缩格式开头为为0x04。
- 加上x的32bit与y的32bit，为65位。
- 压缩格式只存储x32bit的数值。
- 前缀表示y的奇偶性质。
- 0x02为偶，0x03为奇。

### c.地址生成

- 对于公钥进行第一次SHA-256。
- 在进行一次RIPEMD-160哈希，获取20字节的数值。
- 在加上前缀0x00表示在主网的地址。
- 把前缀加20字节数值一起进行2次SHA-256,去前4个字节最为校验值。
- 原地址 = 前缀 + 20字节的数值 + 校验值
- 1+20+4 = 25字节
- 目标地址为原地址进行base58编码。
## 2.交易进行原理

说明：比特币也是基于交易的状态机，在每个全节点都会有一个UTXO集合，即为未花费交易的输出。交易主要是通过对于指定账户进行转账，其中有锁定脚本，每次使用比特币消费，都必须进行脚本的验证，并且指定比特币的来源，脚本验证通过才可以进行花费，一般脚本都是要进行身份验证，以获取合法的权限进行交易。

![[hexo/public/pic/Pasted image 20240807151713.png]]
- 其中的类型即为交易脚本类型。
- 脚本公钥为，下次交易的公钥，即为输出值地址的公钥,可通过公钥的哈希值，计算出账户。
- 类型：即为脚本的类型。
## 3.交易脚本验证的不同类型

说明：交易验证是通过进行本次交易脚本的输入脚本作为前者，要花费的交易的输出脚本作为后者，然后对与脚本进行执行，执行通过，则说明交易合法，执行不通过则说明交易不合法，不予上链，其中脚本执行时用栈的形式进行执行的，其中脚本类型有几种 P2PK、P2PKH、P2SH。

### a.P2PK(Pay to Public Key)

- 输入脚本：PUSHDATA(Sig)
- 输出脚本1：PUSHDATA(Public Key)
- 输出脚本2：CHECKSIG

执行：执行脚本时，先对本次交易的签名进行输入，然后获取输出脚本的公钥，对于本次交易进行验证，因为本次交易的发起者为拥有上次交易输出公钥的地址，对本次交易进行私钥签名，通过对于本次交易进行摘要，然后进行与公钥进行相关的EDSA计算，验证输入签名是否合法，合法则脚本执行通过。
### b.P2PKH(Pay to Public Key Hash)

- 输入脚本1：PUSHDATA(Sig)
- 输入脚本2：PUSHDATA(Public Key)
- 输出脚本1：DUP(复制操作)
- 输出脚本2：HASH-160
- 输出脚本3：PUSHDATA(Public Key Hash)
- 输出脚本4：EQUALVERIFY
- 输出脚本5：CHECKSIG
执行：首先将Sig与Public Key先后入栈，复制Public key的值入栈，然后对于栈顶的Public Key进行HASH-160的计算，然后把输出脚本的Public Key Hash值入栈，然后出栈两个元素进行相等比较，一个是刚入栈的公钥哈希，一个是计算的公钥哈希，验证成功进行公钥与签名的验证。
### c.P2SH(Pay to Script Hash)

- 输入脚本：............
- 输入脚本：PUSHDATA(Sig)
- 输入脚本：PUSHDATA(Serialized redeemScript)
- 输出脚本：HASH-160
-  输出脚本：PUSHDATA(redeemScript HASH)
- 输出脚本：EQUAL
执行：先执行外步骤，判断脚本是否相同，再进行执行脚本内的操作执行，其中两步的验证操作是在同一个栈上进行的。

### d.P2SH实现P2PK

- 脚本的框架还是上述的P2PSH的，只是序列化赎回脚本内容写入了。
- PUSHDATA(Public Key)
- CHECKSIG

执行：先进行基本的外循环执行，然后成功之后，栈中只留下了Sig，执行赎回脚本，放入Public Key,然后判断签名是否合法。

### e.P2SH实现多重签名

#### 普通的多重签名

![](../pic/Pasted%20image%2020240722231809.png)

- 其中的红色叉子是脚本的逻辑问题，最终会多弹出一个数值。

#### P2SH实现多重签名

- 输入脚本：Sig1
- 输入脚本：Sig2
- 输入脚本：Sig3
- 输入脚本：序列化的赎回脚本。
- 输出脚本：HASH-160
- 输出脚本：脚本哈希。
- 输出脚本：判断哈希是否相等。
- 赎回脚本：M
- 赎回脚本：Pubkey1
- 赎回脚本：Pubkey2
- 赎回脚本： PubKey3
- 赎回脚本：N
执行：按顺序执行，其中M为拥有M个合法的的公钥就行，N为所有签名的公钥数量总和。

### f.Proof of Burn
![](../pic/Pasted%20image%2020240722233216.png)

- return之后的代码都不会进行存储。
-  用来销毁比特币，这个币永远不能正确验证。
- 销毁以获取小币种的加密货币，AltCoin。
- 进行永久数据的存储。
- coinbase也可以进行随意的写入，但是需要有区块的记账权。


# 五.分叉

## 1.原因

- 同时获取合法区块。
- 对于交易有分歧。
- 恶意的节点进行分叉攻击，区块是合法的，用来不正当竞争。
- 回滚交易，使得已经发布的交易无效。

## 2.分叉类型

### a.软分叉

- 旧节点认新区块，新节点不认旧区块。
- 旧认新，新不认旧。
- 改变区块限制1M-->0.5M.
### b.硬分叉

- 旧节点不认的区块，新节点认。
- 新认旧，旧不认新。
- 改变区块大小限制由1M-->4M。