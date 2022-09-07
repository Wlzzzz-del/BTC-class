# ETH账户存储
ETH使用trie(字典树)存储账户信息,ETH的账户地址由账户的公钥取哈希并截断得到
[trie](pic/tries.png)

# 为什么使用字典树
## 字典树分支
trie中每个节点的分支数目，取决于每个节点的取值范围，账户地址由40个16进制数表示，因此branch factor为'0'~'f'再加上一个'\0'作为结束标志。
## 使用trie存储账户不会产生哈希碰撞
如果使用哈希表,可以实现o(1)查找和修改，但是哈希表可能会产生哈希碰撞问题，两个账户映射到同一个地址上。当使用trie存储账户信息时，就不存在这样的问题,由于两个账户的地址绝对不同，因此最后会映射到trie上的不同分支。
## 为什么不用merkle tree
如果使用merkle tree存储账户信息，一个很大的问题就是排序。每个全节点对用户的顺序不一样，不一样的顺序就导致了merkle tree的root值不同，因此多个节点之间难以完成认证。使用trie存储账户信息，账户按照不同顺序给定相同的输入，产生的输出都是一样的trie结构。

## 更新操作的局部性
如果使用merkle tree存储，欲更新某个账户信息，将会使整个树的root值发生变化,导致整棵树重构。然而trie有良好的更新局部性，更新某个账户并不影响整个数据结构的稳定性。

# trie缺点
## 存储空间开销巨大
每个地址有40个16进制数，因此占了40个节点数，整棵树占用的空间可想而知。如果能对中间的节点进行压缩，压缩后不仅能提高查找效率，而且存储空间也大幅减少,树的高度变得更浅了，读取一个账号所需要的访问内存次数大幅降低。
因此引入了压缩字典树的数据结构。[压缩trie](pic/压缩字典树.png)
**当键值分布越稀疏，trie的查找效率越高.** 

## modifyed_Merkle_paricia_trie
[modifyed_Merkle_paricia_trie](pic/modify_mpt.png)
以太坊采用modifyed_mpt结构.merkle代表了其所有指针都用哈希指针来代替，最终在root上产生一个哈希值,被记录在block header内。如图所示，右上角keys_values指明了四个账户以及其余额，为了方便起见，此处地址只用了七位，values代表该账户里的钱。
wei是以太坊中最小的货币单位，1wei几乎可以忽略不计。
账户部分左边是一个根节点root,该节点是一个extension node，其中prefix指示了当前节点的状态，shared nibbles(nibbles是十六进制数的意思)指明了公共的十六进制数部分，nextnode指向了下一个节点。
### prefixs
prefixs的值指明当前节点的状态：
+ 0 - 当前节点是extension node，且shared nibbles的长度为偶数。
+ 1 - 当前节点是extension node，且shared nibbles的长度为奇数。
+ 2 - 当前节点是leaf node，且nibbles的长度为奇数
+ 3 - 当前节点是leaf node，且nibbles的长度为偶数
### Branch_Node
Branch_Node是一个线性表，长度为16，代表16进制的每一种取值，表中存放了指向下一个节点的指针。

## modify_mpt的更新
[update](pic/upadte_of_mpt.png)图中的modifyed_mpt中，当某个信息需要修改时，并不需要对整个mpt进行重构。在新的区块中，没有改动的部分指向之前的mpt，有改动的部分进行改动，并且相应牵连涉及的哈希值需要更新。如图29被改为了45，因此仅一串的哈希值需要更新,其他节点通过指向之前的节点来获取。

## 交易的回滚
为什么要保存先前的节点？主要目的是用来实现交易的回滚。
当区块链出现临时性分叉时，分叉被取消的那块交易需要被回滚到原来的状态。但是由于智能合约的存在，以太坊的编程语言是图灵完备的，不像比特币的脚本语言，难以从输出结果推算出输入，换句话说交易难以回滚。
因此需要通过记录先前的交易记录来达到交易回滚的目的。

# block_header的数据结构
[block_header数据结构](pic/以太坊blockheader.png)
+ ParentHash 代表父亲节点的哈希值
+ UncleHash 代表叔父节点的哈希值，该叔父节点可能比父亲节点还要大
+ CoinBase 代表矿工地址
+ Root 代表状态树的根哈希值
+ TxHash 代表交易树的根哈希值
+ ReceiptHash 代表收据树的根哈希值
+ Bloom 
+ Difficult 挖矿的难度值
+ Number
+ GasLimit 智能合约中gas的消耗上限
+ GasUsed 智能合约中gas使用的消耗
+ Time 区块产生的大约时间
+ Extra 额外的数据
+ MixDigest
+ Nonce 矿工需要寻找的随机值

# block的数据结构
[eth_block数据结构](pic/eth_block.png)
header存放了上面的数据，uncles是一个header列表，transactions存放了多个交易。

# extblock
[ext_block](pic/ext_block.png)区块在网上发布时，就是发布的ext_block.