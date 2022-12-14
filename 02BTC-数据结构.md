# 哈希指针

哈希指针是一个结构体，不仅保存了某个结构体存放的起始位置，还保存了该结构体的哈希值。  
这样当解引用该指针时，可以检测该结构体的内容是否被篡改。

# 区块链

区块链本身是一个链表，但是他与链表又有区别————使用哈希指针代替了普通的指针.
## tamper-evident log
如此可以检测恶意篡改。普通链表的被篡改无法检测，但是由于哈希指针保存了前一个节点的哈希值(包括前一个节点的哈希指针)，因此当某个节点的值被篡改时，会导致牵一发动全身的效应————篡改者不得不修改想要修改的区块以后的所有区块，否则篡改行为将被检测。
另外，  
头节点被称为————genesis-block,  
尾节点被称为————most-recent-block.

尾节点的哈希值通常被存放在计算机本地，只要记住尾节点的哈希值，就能够检测整个区块链是否有被篡改。

# merkle tree

与链表类似，merkle tree本身是一个binary tree，但是与binary tree又有区别————使用哈希指针代替了普通指针。  
叶子节点由data blocks组成，除了叶子节点之外的所有节点都是存放指向其孩子节点的哈希指针。
比特币使用merkle tree中的叶子节点来记录交易流水，每个节点被存储在一个区块block中。区块与区块之间通过哈希指针连接。

## 轻节点与全节点
轻节点只包含block header,即只包含root hash值。
全节点包含block header和block body,即包含完整的merkletree。
轻节点常用于客户端比特币钱包，只为了显示一个钱包的金额。  
全节点则用于对交易进行验证与记录。
当全节点完成了某次交易，需要通过merkle proof向轻节点证明本次交易。
## block header
block header 只存放merkle tree的root hash值.
## block body
block body 存放整个merkle tree.
## merkle proof(存在性证明)
由全节点向轻节点发起证明，将由某次交易记录(即叶子节点)到根节点的路径发送给轻节点客户端，轻节点客户端对哈希值进行验证，如果算出来的哈希值与block header的值一样则验证成功。
