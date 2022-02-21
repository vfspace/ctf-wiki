# Blockchain Weaknesses

## Double-spending attack

双花问题是电子现金系统中的一个潜在问题，即相同的资金同时支付给两个接收者。区块链系统通过公开账本的方式来避免双花问题，用户广播交易时不会立即将其添加到区块链中，而是等待矿工通过挖矿将其打包在区块中，接收者在确定合法的交易已经添加到区块链之后，才可以认为该交易有效。用户通过在区块链上接收付款时等待确认来保护自己免受双花欺诈，随着确认数量的增加，交易变得更加不可逆转。

但区块链系统并不能完全防止双花，攻击者仍可以通过庞大的哈希计算资源来重新完成已确认区块的工作量证明并使自己所在链成为最长链来逆转交易实现双花，以及可以针对某些未确认交易实现双花攻击。目前有一下常见的几种方式能够引发双花攻击：

### 51% attack

51％攻击通是指矿工试图控制超过50％的哈希率（挖矿算力）来实现双花，在此攻击中，攻击者可以阻止新交易被确认或撤销他们控制网络时已经确认的交易。

由于比特币等Pow共识的区块链遵循最长链原则，当矿工发现全网有一条更长的链时，他就会抛弃他当前的链，把新的更长的链全部复制回来，在这条链的基础继续挖矿，而分叉出的短链则被丢弃掉。因此攻击者在与商家完成交易后从交易前分叉区块并通过掌握的足够多的算力持续挖出更长的链使发生交易所在的链丢弃来实现双花。

理论上无法通过技术来防范51%攻击，而是通过经济学原理来避免，使达到全网51%的算力成本非常高，且拥有如此强大算力的攻击者进行正常挖矿比作恶作的收益更高。但对于小规模的山寨币来说，攻击成本较低，如Ethereum Classic网络曾遭受多次51%攻击。

参阅: https://en.bitcoin.it/wiki/Weaknesses#Attacker_has_a_lot_of_computing_power

- 题目名称 miniblockchain

### Finney attack

Finney attack（芬尼攻击）是由Hal Finney命名的，他是第一个描述扣块攻击的人。这种攻击是一种双花攻击的变化，攻击对象主要针对的是接受0确认的商家。

攻击者将一个给自己转账的交易预挖为一个区块，但不立即将其广播到网络中，相反用同一笔代币花费在接受 0 确认的商家的交易中，攻击者在获得商家的交换物后、这笔交易A真正确认前，才广播他之前所预挖的区块使转给自己的交易合法，这时比特币网络会接受该有效块并使对商家的交易无效，最终达到双花的目的。

0确认是指一笔交易被广播到全网，即将被打包进入区块前的状态。因为区块链目前的出块时间过慢，交易确认需要等待的时间较长，部分商家为了节省时间接受0确认的交易，也就是你把交易信息广播到全网就可以了，不需要等待打包入区块。

### Race attack

攻击者使用相同的资金连续广播两个冲突的交易，但最终只有一个交易得到确认。该攻击主要是控制矿工费来实现双花，同样针对接收0确认的商家，最终使发给自己的交易被打包确认而使另一个支付交易无效。  
与芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是0确认交易 vs 冲突交易。

### Vector76 attack

也被称为一次确认攻击，是 Race attack 和 Fenny attack 的结合，使得具有一个确认的交易仍然可被撤销。

在这种攻击中，一名矿工创建了两个节点，其中一个连接到商家节点，另一个节点连接到区块链网络中连接良好的节点。然后，矿工使用相同的资金创建两笔交易创建了两项交易，一笔交易发送给商家地址，我们称为交易1；一笔交易发送给自己的钱包地址且设置较高的矿工费，我们称为交易2。

攻击者不立即广播这两笔交易而是在交易1的分支上进行挖矿，攻击者挖到区块后，向商家节点广播交易1，向另一个节点广播交易 2。交易2被认为有效后，攻击者立即把自己之前在交易1分支上挖到的区块广播给商家，这时，这个接受一次确认就支付的商家会确认交易成功。

由于交易2发送给了连接到更多节点的节点，所以矿工在这个分支上挖出了更长链的机率更大，在这种情况下交易1会被回滚，从而实现了双花。

参阅:  
http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

## Block withholding attack

扣块攻击最简单的形式是上述的芬妮攻击，但同样存在针对[矿池](https://academy.binance.com/zh/articles/mining-pools-explained)的扣块攻击方式。

矿池最常见的支付机制是PPS（Pay-Per-Share)，该机制中矿工所贡献的每一个“share（股份）”都将收到固定的报酬，Share是用来记录矿工贡献的哈希值，这里的share并不是区块链网络中有效的哈希，而只是由矿池所设置的匹配条件。由于找到符合Pow区块链系统要求的解对于单独的矿工来说是极小概率事件，矿池为了更好衡量矿工们的工作量，会给矿工们设置一个合理的提交工作成果（Share）的门槛。

扣块攻击是指恶意矿工在找到满足矿池要求结果，但不满足比特币系统要求的结果时，正常向矿池提交工作证明；一旦得到满足比特币系统要求的结果时，也就是真的挖到区块时，则把这个结果私自扣下，不向矿池提交，矿池便因此损失了对应的奖励。扣块攻击会使矿工和矿池都受损失，矿工只是得不到矿池分摊的Share奖励，但使矿池损失了出块奖励。

## Selfish-Mining attack

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二个区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉链短便被作废了，此时攻击者因为挖到了两枚新区块而获得相应收益，而诚实矿工的奖励则被回滚。

!!! note
    注：题目附件相关内容可至 [ctf-challenges/blockchain](https://github.com/ctf-wiki/ctf-challenges/tree/master/blockchain) 仓库寻找。