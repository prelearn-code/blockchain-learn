
# 造成原因

## state fork

- 对于当前区块有分歧。
- 同时获取到合法块。

### forking attack

- deliberate fork

## 协议分叉(protocol fork)

### 硬分叉
![](../pic/Pasted%20image%2020240723233030.png)

-  对于区块链的基本信息的修改，接受与不接受的人之间的分叉。
- 接受的人认为修改过的为合法区块，未修改为非法区块。
- 典型的为block size limit
- 永久性分叉。
- 加一个chainID作为链的标识。

### 软分叉（sofy fork）
![](../pic/Pasted%20image%2020240723233706.png)
- 分叉的结果是只有一条链是合法的。
- 更新新版本的节点挖的区块可以被识别与认证。
- 旧版本的节点挖出来的区块不会被新节点承认，即无法加入最长合法连中。
- 旧认新，新不认旧。

#### coinbase

- 挖矿难度：extra nonce + 4bytes
- extra nonce：前8bit用来作为修改target的数据。
- extra nonce 后面：作为UTXO的根哈希。

#### UTXO

- 改成默克尔树。

#### 默克尔树

- 验证交易是否在区块内。