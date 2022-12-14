# 假设以太坊沿用比特币的共识机制
以太坊的平均出块时间为几秒钟，相比于比特币的十分钟大幅降低了。那么假如以太坊沿用比特币的共识机制，会带来什么后果呢？
## 临时分叉的增多
由于出块时间的缩短，临时分叉必然增多 ,临时分叉中被弃用的分叉称为orphan block(孤儿区块)。
## 算力的中心化
现在挖矿算力趋向于中心化，如大型矿池的出现。算力越高意味着出块的概率越高。
假设有某个大型矿池挖出了一个区块，此时其他几个个体也挖出来几个区块，现在就产生了临时分叉。
为了不让自己的block作废，大型矿池将自己挖出来的block作为prev block，然后挖下一个区块，其他个体也是如此。然而大型矿池的算力较高，出块的成功概率也较高，因此非常有可能使得大型矿池不断地产出block。这种现象称为minning centralization.
其他个体户为了不让自己的区块被deny（区块被deny后出块奖励就作废），也倾向于选择大型矿池挖出来的区块继续往下挖，这时候就产生了centralization bias。


# GHOST协议
GHOST协议针对这一问题做出来调整,如图所示.
[GHOST](pic/GHOST.png)
在临时分叉中被弃用的分叉区块不再称为orphan block，而是叫作uncle block。并且uncle block被奖励 (7/8)* 3 个以太币。为了鼓励新插入的区块记录uncle block，节点每记录一个uncle block就奖励他 (1/32)*3 个比特币，最多能够记录两个叔父区块。

## 核心思想
给挖到了矿但是矿没有被采用的矿工一些安慰费。

# GHOST协议的问题
## 叔父区块过多，但是新区块只能包含两个叔父区块
## 矿池与矿池之间处于竞争关系，故意不包含其他矿池挖出来的叔父区块
## 如何解决？
以太坊的GHOST协议将叔父区块的定义拓展了，一个新区块不仅能包含当代叔父，还能包含隔代叔父，就是该区块爷爷辈的orphan block也可以被包含。
然而代数不能允许无限的增长，比如说1000代的叔父区块，彼时挖矿难度不高，就会有人利用这个办法挖出更多的叔父区块期待其他人包含。
因此以太坊规定，只有七代以内的叔父区块被包含时能获得奖励(uncle reward),奖励随着代数的增加而减少，如图所示。
[at most seven generation](pic/seven_generation_uncle_block.png)

## 叔父区块的交易来往需要验证吗
不需要，叔父区块的交易有可能与父区块交易冲突，因此不进行验证。叔父区块作为一种Pow，唯一需要验证的是哈希值是否合法。

## 叔父区块如果是一个长链呢？
[叔父区块长链情况](pic/forck_attack.png)
叔父区块如果是一个长链，有若干种处理方法:
1. 类似于GHOST协议给长链上的所有节点一点安慰费
2. 只给第一个节点安慰费，其他的都不给安慰费
以太坊采取第二个措施。问题来了，为什么不采取第一个措施呢？
考虑一下fork attack，如果采取第一个措施，会使得fork attack的成本大幅下降。
fork attack尝试使用分叉回滚操作，如果采取第一种，当回滚失败时，所有这些区块还能有安慰费,使得分叉攻击的成本降低，此后所有人都会先尝试分叉攻击。