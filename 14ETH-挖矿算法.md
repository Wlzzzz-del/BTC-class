# one cpu, one vote
中本聪在最早的比特币论文中提出one cpu, one vote.认为应该让所有人的桌面机更好地参与到挖矿中，使得算力更加分散，以防止51%攻击。然而asic芯片专业化挖矿的出现正在逐渐打破这一想法。
在后来的加密货币中，像ETH的挖矿算法，都在尽力地实现asic resistance.
为了实现asic resistance，就需要使用memory hard mining puzzle。
# difficult to solve, but easy to verify
设计挖矿算法的一个原则就是难以解决，但是容易验证。
莱特币基于数组的挖矿算法问题在于，轻节点的验证也同样需要耗费很多memory。
这违背了原则。

# 难度炸弹
ETH的挖矿算法内置了一个难度炸弹，该难度炸弹为指数级函数，一开始难度趋于平稳，随着区块数量的增多，挖矿难度会呈指数级别增长。
ETH的创始人想要在未来某个时刻将PoW模式转为PoS模式，因此设置了该难度炸弹。
但是PoS模式还未开发好，ETH的难度就处于十分高的水准，因此在拜占庭时期，不得不调整其难度与出块奖励。