# 去中心化账本

BTC采用的是基于交易的账本模式(transaction-based ledger)，只记录了每一笔交易和铸币，不直接记录账户余额。如果某个账户想要知道自己的余额，则需要通过交易进行推算。
以太坊则采用基于账户的账本模式(account-based ledger),该种模式显式记录了每个账户上有多少余额。
BTC交易模式的隐私保护性较强，但是每笔交易都需要注明币的来源，以防止双花攻击.

# UTXO

BTC中的全节点维护一个叫UTXO(Unspent Transaction Output)的数据结构，即还没有被消费的交易的输出。一笔交易中有多个输出，还没有被交易的输出就存放在UTXO中。

假设A给了B 5个比特币，同时给了C 3个比特币。B把这5个比特币转给了其他人，而C一直留着这3个比特币。此时，这5个比特币就不存放在UTXO中，而这3个比特币存在UTXO中，只要C一直不花这3个比特币，那么信息就永远存在UTXO中。

UTXO的集合可以检测double spending，检测新发布的交易是否合法，因此比特币系统中的所有全节点都要维护UTXO这个数据结构。

一般来说一笔交易的total inputs=total outputs。但有些交易的total inputs要略微大于total outpus。这涉及到比特币的第二个奖励机制。

# 交易费(transaction fee)

因为打包交易的过程麻烦而费时费力，一个矿工要穷举算出nonce就已经相当耗时。为了鼓励矿工尽可能多的将交易写到区块中，比特币系统在每笔交易中抽出一小部分费用作为交易费用于奖励矿工，以保持矿工的积极性。  

# 挖矿(Mining)

## 比特币总量
比特币的发行是通过寻找nonce获得出块奖励,最初的出块奖励是50个比特币，每隔发行21万个比特币区块奖励就会减半，至2022年，矿工每成功记账一次，获得的奖励为6.25个比特币。
[number_of_bitcoins](pic/number_of_bitcoins.jpeg)
由此推算出，比特币最终的总量为2100万，预计2140年会被全部挖完。

## 区块例子

[block_example](pic/block_example.png)
区块的块头结构如下：
```C++
class CBlockHeader{
    public:
    int32_t nVersion;// BTC版本号，无法改动
    uint256 hashPrevBlock;// 前一个区块块头哈希值(32字节)，不能改动
    uint256 hashMerkleRoot;// 通过修改Merkle Tree中铸币交易的CoinBase域来调整根哈希值
    uint32_t nTime;// 区块产生时间，有一定调整的余地
    uint32_t nBits;// 挖矿目标阈值target的压缩编码, 按照协议要求定期修改
    uint32_t nNonce;
}
```
除了调整随机数nonce，我们还可以调整MerkleRoot来调整挖矿的难度控制时间。由于nonce是32位无符号整数，所以只有2的32次方个可能的取值。由于比特币挖矿的人数增加，很有可能所有的取值遍历一遍都无法找到合适的值。所以只改变nonce是不够的，还可以通过调整merkleroot的值来改变哈希值。
所以真正的挖矿只有两层循环，外层调整merkletree中coinbase域的extra nonce，算出blockheader中的根哈希值后，再调整header里的nonce。

### nBits
nBits存放了比特币系统当前目标阈值target的压缩编码，target为比特币系统出块的难度系数，target越大，矿工挖矿时越容易产生合法区块。  
由于比特币系统代码是开源的，为了防止某些恶意节点不修改target而进行挖矿，block-header存放了nBits为target的压缩编码，以此来进行合法性验证。