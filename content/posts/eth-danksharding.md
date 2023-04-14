---
title: " EIP-4844及Danksharding万字研报：全新公链叙事已来？解读「区块链不可能三角」的变革性解决方案"
date: 2023-04-14T11:28:55+08:00
categories : ["深度分析"]
draft: false
tags : ["ETH","扩容","L2","分片"]
featuredImagePreview: "/mitul-grover-OAcpx6U-unsplash.jpg" #这里修改的是“相关文章”小图和首页预览图
images: ["/mitul-grover-OAcpx6U-unsplash.jpg"]
---

{{< admonition type=tip title="CryptoDAO点评" open=true >}}
扩容一直是以太坊绕不开的话题之一，以太坊要想成为真正的 “世界计算机” 需要同时具备可扩展性、安全性、去中心化，但同时具备这三个条件在行业中被称为 “区块链不可能三角”，在整个行业中是一直没有被解决的一个大难题。

但随着 2021 年末以太坊研究员&开发者 Dankrad Feist 提出以太坊新分片方案 Danksharding 后，似乎给解决 “区块链不可能三角” 带来了一种变革性方案，甚至有可能改写整个行业的游戏规则。

本份研报将尝试用白话形式讲清楚什么是以太坊的新分片方案 Danksharding 以及来龙去脉。关于为什么写这份研报是因为关于 Danksharding 的中文文章十分少并且大多都需要较高的知识门槛，因此菠菜将尝试为你掰开揉碎背后的复杂原理，通过用简单的大白话去让一个 Web3 初学者也可以看懂以太坊新分片方案 Danksharding 及其前置方案 EIP-4844。
{{< /admonition >}}

## 一、以太坊为什么需要扩容？
以太坊创始人 Vitalik Buterin 在 2014 年发表以太坊的白皮书 “下一代智能合约和去中心化应用平台” 后，区块链迎来了一个新的时代。智能合约的诞生使得人们可以在以太坊上创建去中心化的应用（DApp），也为区块链的生态带来了一系列创新如 NFT、DeFi、GameFi 等。


### 以太坊扩容背景

随着以太坊链上生态日渐壮大，越来越多的人开始使用以太坊，以太坊的性能问题开始暴露出来。当许多人同时在以太坊上进行交互时，区块链就会发生 “拥堵”，就像一条马路上的红绿灯时间是固定的，在一定数量内的车辆不会导致堵车，但突然在高峰期的时候许多车都往这条马路上开，绿灯时驶出马路车辆的数量远远没有新驶入这条马路等待红绿灯车辆的数量多，就会导致大拥堵，所有车辆通过这条马路的时间就会被一直拉长，那么在区块链中也是如此，所有人的交互请求确认时间都会被拉长。

但在区块链中，被拉长的不只是时间，还会导致高昂的 Gas 费（Gas 费可以理解为支付给矿工们的辛苦费，矿工负责打包处理区块链中的所有交易），因为矿工会优先处理出价最高的交易，这就会导致大家都去抬高 Gas 费去争取更快的交互请求确认引发一场 “Gas War”。比较有名的一次事件是 2017 年的一个 NFT 项目——CryptoKitties 加密猫的爆红将 Gas 费推至几百美金一次交互，在以太坊上进行一次交互需要消耗几十甚至上百刀的 Gas 费是多么昂贵的一个成本。

而导致如此昂贵 Gas 费的主要原因便是以太坊的性能已经无法满足现有用户的交互需求。在性能的计算上，以太坊与比特币不同，比特币由于只是单纯的账本处理转账信息，所以 TPS 是固定的每秒可以处理 7 笔交易，但在以太坊中不同。

以太坊由于智能合约的存在，每个交易的内容各不相同，所以每个区块可以处理多少笔交易（TPS）取决于一个区块中包含的交易的数据量大小，每个交易的数据量大小都是根据实时需求决定的，关于以太坊性能的机制我们可以了解到：（以下信息对理解 Danksharding 有帮助，一定要看噢~）

 - 以太坊是按照 Gas 费来规定一个区块的数据量上限的，一个区块最高可以承载 3000 万 GAS 的数据量
 - 以太坊不希望每个区块的数据量都太大，所以每个区块都有一个 Gas Target（目标 Gas）为 1500 万 Gas 的数据量
 - 以太坊有一套 Gas 的数据消耗标准，不同类型的数据消耗的 Gas 都是不一样的，但根据菠菜的估算，每个区块大概有 5kb~160kb 左右的大小，平均一个区块是 60~70kb 左右的数据大小
 - 一个区块的 Gas 消耗一旦超过了 Gas Target 也就是 1500 万 Gas，那么下一个区块的基础费用就会贵 12.5%，如果低于的话就会降低基础费用，这个机制是一个自动化的动态调节机制，可以在交易高峰时一直增加成本去缓解拥堵，同时在交易低迷时降低成本来吸引更多交易

那么通过了解了以上机制我们可以得知，以太坊的 TPS 是浮动的。我们可以通过区块链浏览器看到每个区块的交易数量来算出大概的 TPS，根据下图可以看到，平均一个区块按照刚好达到 Gas Target 来计算有 160 笔交易左右，最高的可以达到 300 笔交易以上。按照每个区块 12 秒的出块时间 TPS 大概为 13~30 笔交易左右，但根据目前已知以太坊的 TPS 最高可以达到每秒 45 笔交易。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-11.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

如果拿世界著名的交易系统 VISA 每秒可以处理几万笔交易的性能来说，想要成为 “世界计算机” 的以太坊每秒最多处理 45 笔交易的性能实在是太弱了。所以以太坊迫切需要扩容来解决性能问题，这关乎到以太坊的未来，但扩容并非一件易事，因为在区块链行业中存在一个 “不可能三角”。

### 什么是区块链不可能三角？
“区块链不可能三角” 指的是一个公共区块链无法同时满足三个特性：去中心化、安全性、可扩展性。
- 去中心化：指的是节点的去中心化程度，节点越多越分散越去中心化
- 安全性：指的是整个区块链网络的安全，攻击成本越高越安全
- 可扩展性：指的是区块链的处理交易性能，每秒可处理交易越多越具备可扩展性

如果我们从这三点的重要性来看的话，我们会发现去中心化和安全性是权重最高的，去中心化是以太坊的基石，正是去中心化赋予了以太坊中立、抗审查、开放性、数据所有权和近乎牢不可破的安全性。而安全性的重要性自然不必解释，但以太坊的愿景是在去中心化并且安全的前提下实现可扩展性，实现难度可想而知，所以这也被称为 “区块链不可能三角”。



### 目前以太坊的扩容解决方案有哪些？

我们知道了 “区块链不可能三角” 中，以太坊要想实现扩容的前提必须是保证去中心化和安全性，要做到确保去中心化和安全性的话就必须在实现扩容中不能过多提高对节点的能力要求。因为节点是维护整个以太坊网络中必不可少的角色，高要求的节点会阻碍更多人成为节点越来越中心化，节点当然是门槛越低越好，低门槛节点会让更多人参与进入让以太坊更加去中心化和安全。

那么目前针对以太坊的扩容方案有两种：Layer2 和分片（Sharding），Layer2 是针对底层区块链（Layer1）扩容的一种链下解决方案，原理是将区块链上的请求放在链下执行。Layer2 的方案有好几种，本份研报只重点介绍一种 Layer2 方案那就是 Rollup：Rollup 原理是将数百笔交易在链下像摊煎饼一样打包成一笔交易发送给以太坊来实现扩容，这样每个人平摊下来上传以太坊的费用就会很便宜，同时还可以继承以太坊的安全性。

Rollup 目前分为两种类型：Optimism Rollup（乐观 Rollup）和 ZK Rollup（零知识证明 Rollup），这两个 Rollup 的区别简单来说 Optimism Rollup 就是假设所有交易都是诚实可信的，把许多笔交易压缩成一笔交易提交给以太坊，在提交后会有一段时间窗口（挑战期-目前是一周），任何人都可以质疑发起挑战来验证交易的真实性，但用户如果要将 OP Rollup 上的 ETH 转到以太坊上则需要等待挑战期结束后才可以得到最终确认。

ZK Rollup 则是通过生成一个零知识证明来证明所有交易都是有效的，并将所有交易执行后的最终状态变化上传至以太坊。相比 Optimism Rollup 来说 ZK Rollup 更有前景，ZK Rollup 不需要像 Optimism Rollup 那样上传压缩后的所有交易细节，只需要上传一个零知识证明和最终的状态变化的数据即可，意味着在可扩展性上可以比 OP Rollup 压缩更多的数据，并且也不需要像 OP Rollup 那样等待长达一周的挑战期，但 ZK Rollup 最大的缺点就是开发难度极大，所以短期来看 Optimism Rollup 会占据 L2 大量的市场。

除了 Layer2 之外呢，还有一种扩容解决方案就是我们本文的主角分片（Sharding），我们知道了 Layer2 是将以太坊上的交易放到链下去处理。但不管 Layer2 怎么去处理数据，以太坊本身的性能还是没有变，那么 Layer2 能实现的扩容效果其实是没有那么显著的。

分片就是在以太坊这条 Layer1 的层面上去实现扩容，但我们知道在以太坊上实现扩容的前提是确保以太坊的去中心化和安全性，那么就不能去增加太多对节点的负担。

关于分片的具体实现方案也一直是以太坊社区不断讨论的一个话题，目前最新的方案便是本文的主题 Danksharding，那么也提到了 Danksharding 是最新的分片方案，在讲 Danksharding 之前呢菠菜也会简单介绍一下老的分片方案是什么样的以及为什么不采纳。

## 二、以太坊初始分片方案 Sharding1.0
在讲分片 1.0 方案之前，菠菜需要先介绍一下以太坊目前的 POS 共识机制是如何运行的，因为这是理解分片 1.0 方案和 Danksharding 的必备前置知识，在讲分片 1.0 方案上会简短概括（知道大概要做什么就可以了）。

### 以太坊的 POS 共识机制是如何运作的？
共识机制是使区块链中所有维护网络的节点达成一致的一套体系，其重要性不言而喻。以太坊在 2022 年 9 月 15 日完成了以太坊 2.0 升级阶段中的 “The Merge”，即 POW 工作量证明的以太坊主网与 POS 权益证明机制的信标链进行了合并，POS 权益证明机制正式取代了 POW 工作量证明机制成为了以太坊的共识机制。

我们知道在 POW 工作量证明机制中，矿工们是通过算力的堆叠来竞争出块权，那么在 POS 权益证明机制中，矿工们则是通过质押 32 个 ETH 成为以太坊的验证节点来竞争出块权（关于质押的方式这里不展开介绍）。

除了共识机制的变化，以太坊的出块时间也由之前浮动的出块时间变为了固定时间，分为插槽（Slot）和周期（Epoch）两个单位：Slot 为 12 秒，Epoch 为 6.4 分钟。一个 Epoch 中包含了 32 个 Slot，简单来说就是 12 秒出一个块，6.4 分钟出 32 个区块为一个周期（Epoch）。

当矿工质押 32 个 ETH 成为验证节点后，信标链就会通过随机算法来选择验证节点作为出块节点来打包区块，每一个区块都会随机选择一次出块节点。同时在每一个周期 Epoch 中，信标链还会将所有的验证节点平均并随机分配给每个区块一组由至少 128 个验证节点组成的 “委员会（Committees）”。

也就是说每个区块会被分配所有节点数量 32 分之 1 数量的验证节点，这些验证节点组成的 “委员会（Committees）” 需要对每个区块出块节点打包的区块进行验证投票。当出块节点打包区块后，超过三分之二以上的验证节点投票通过即可成功出块。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-13.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

### 初始分片方案 Sharding1.0 是什么样的？
在初始的分片方案 Sharding1.0 的设计理念中，以太坊从原本的一条主链被设计为了最多 64 条分片链，通过增加多条新链的方式来实现扩容。在这个方案中，每条分片链负责处理以太坊的数据并交给信标链，信标链则负责整个以太坊的协调，每一条分片链的出块节点和委员会都是由信标链来随机分配。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-14.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

信标链和分片链之间通过交联 (crosslinks) 来进行实现链接，信标链的区块会给同区块的分片区块一个哈希值，然后这个分片区块带着这个哈希值给到下一个信标区块就可以实现交联 (crosslinks) 了，如果错过了的话则给到下一个信标区块即可。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-15.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)


### 初始分片方案 Sharding1.0 方案存在哪些弊端？
简单来说分片 1.0 的方案就是把以太坊切割成很多条分片链来一起处理数据然后将数据交给信标链来达到扩容，但这个方案存在许多弊端：

- 开发困难：把以太坊分割成 64 条分片链的同时还要确保可以正常运行，在技术上要实现十分困难，并且越是复杂的系统越容易出现一些不可预料的漏洞，一旦出现问题要进行修复的话也会带来许多麻烦。

- 数据同步问题：信标链每一个周期 Epoch 就会重新打乱一次负责验证的 “委员会 (committees)”，那么每次重新分配验证节点就是一次大规模网络的数据同步，因为如果被节点分配到了一条新的分片链就需要同步这条分片链的数据，由于节点的性能带宽各不相同，很难能保证在规定时间内完成同步。但如果让节点直接同步所有分片链的数据的话，就会大大的增加对节点的负担，这就会使得以太坊越来越中心化。

- 数据量增长问题：虽然以太坊的处理速度提高了许多，但多条分片链同时处理数据也带来了存储数据量的大量增长，以太坊数据量的膨胀速度将会是之前的许多倍，对于节点的存储性能要求也会持续增加，从而导致更加中心化。

- 无法解决 MEV 问题：最大可提取价值 (MEV) 是指通过在区块中添加和排除交易并更改区块中的交易顺序，可以从区块生产中提取的超过标准区块奖励和燃料费用的最大值。在以太坊中一个交易发起后，这笔交易会被放在 mempool（一个保存待执行交易的池子）中等待被矿工打包，那么矿工就可以看到 mempool 中的所有交易，而矿工的权利是很大的，矿工掌握了交易的包含、排除和顺序。如果有人通过支付更多的 Gas 费贿赂矿工调整了交易池中的交易顺序而获利，这就属于一种最大可提取价值 MEV。

举个例子：

> 有一种 MEV 手段叫「三明治攻击」或「夹子攻击」，这种提取 MEV 的手段是通过在链上监控大额的 DEX 交易，比如有人想在 Uniswap 上购买价值 100 万美金的山寨币，而这一笔交易会将这个山寨币的价格拉高很多，在这笔交易被放入 mempool 的时候，监控机器人就可以检测到这一笔交易，这时机器人就贿赂打包这个区块的矿工将一笔买入这个山寨币的操作插队在这个人前面，随后在这个人的购买操作之后进行一个卖出的操作，就像一个三明治一样把这个进行大额 DEX 交易的人夹在中间，这样发动「三明治攻击」的人就从中获取了山寨币因为这个人大额交易拉盘的利润，而大额交易的这个人则造成了损失。

MEV 的存在也一直给以太坊带来一些负面的影响，比如「三明治攻击」给用户带来的损失和更差的用户体验、抢跑者竞争导致的网络拥堵、高 Gas 费甚至是节点中心化问题，因为通过获取 MEV 价值越多的节点可以通过收益不断在网络中占据更多的份额，因为更多收益=更多的 ETH=更多的质押权益，再加上 MEV 带来的高昂成本（抢跑造成的网络拥堵和高 GAS）会使得以太坊的用户不断流失，甚至如果 MEV 价值大幅超过了区块奖励还会造成整个以太坊的共识不稳定和安全性，而分片 1.0 方案无法解决 MEV 带来的一系列问题。

随着 2021 年末以太坊研究员&开发者 Dankrad Feist 提出以太坊新分片方案 Danksharding 后，Danksharding 被以太坊社区一致认为是实现分片扩容的最佳方案，甚至可能会给以太坊带来一场全新的革命。

## 三、什么是以太坊新分片方案 Danksharding？
Danksharding 使用了一套新的分片思路去解决以太坊的扩容问题，即围绕着 Layer2 的 Rollup 进行扩容的分片方案，这套新的分片方案可以在不大量增加节点负担且保证去中心化和安全性的同时解决可扩展性问题，同时也解决了 MEV 带来的负面影响。

我们可以从下图看到接下来的以太坊升级阶段”The Surge“和”The Scourge“的目标分别为：在 Rollup 中实现 10 万+的 TPS 和避免 MEV 带来的中心化以及其他协议上的风险。

![图 / 来源：vitalik.eth 翻译：ethereum.cn](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethUntitled-7-1.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

那么 Danksharding 到底是如何去解决以太坊的扩容问题的呢？让我们先从 Danksharding 的前置方案 EIP-4844:Proto-Danksharding 开始讲起。

### 前置方案 EIP-4844:Proto-Danksharding—新的交易类型 Blob
EIP-4844 给以太坊引入了一种新的交易类型—Blob Transcation，这种新的交易类型 Blob 可以为以太坊提供了一个额外的外挂数据库：
- 一个 Blob 的大小约为 128KB
- 一个交易可以最多携带两个 Blob-256KB
- 每一个区块的 Target Blob 为 8 个-1MB，最大可承载 16 个 Blob-2MB（Target 的概念在扩容背景中有提及）
- Blob 的数据是临时存储的，一段时间后会被清除（目前社区建议为 30 天）

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-16.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

目前以太坊每个区块平均大小只有 85KB 左右，Blob 给以太坊带来的额外存储空间是巨大的，要知道以太坊所有账本的总数据量大小从以太坊诞生至今也只有大约 1TB 左右，而 Blob 每年可以为以太坊带来 2.5TB~5TB 的额外数据量，是整个以太坊账本数据量的好几倍。

EIP-4844 引入的 Blob 交易可以说是为 Rollup 量身定制的，Rollup 的数据以 Blob 的形式上传至以太坊，额外的数据空间可以使 Rollup 实现更高的 TPS 和更低的成本，同时也将原本 Rollup 占据的区块空间释放给了更多用户。

由于 Blob 的数据是临时存储的，数据量的暴增并不会对节点的存储性能造成越来越重的负担，如果只是临时存储一个月的 Blob 数据量的话，从同步的数据量来看每一个区块节点需要多下载 1MB~2MB 的数据量，对于节点的带宽要求来说似乎不是什么负担。那么从数据的存储量来看，对于节点来说只需要多下载并保存固定的 200~400GB 左右的数据量（一个月的数据量），在保证去中心化和安全性的同时只付出了增加一点点节点负担的代价的情况下，换来的 TPS 的提高和成本的降低则是以数十甚至上百倍来计算的，这对于解决以太坊的可扩展性问题来说简直是绝佳的方案。

如果数据被清除了用户想访问以前的数据怎么办？

首先以太坊共识协议的目的不是为了保证所有历史数据的永远存储。相反，其目的是提供一个高度安全的实时公告板，并为其他去中心化协议留出长期存储空间。而公告板的存在是为了确保在公告板上发布的数据有足够长的时间停留，任何想要这些数据的用户或者协议，都有足够的时间来抓取数据这些数据并进行保存，所以保存这些 Blob 数据的职责就交给了其他角色比如 Layer2 的项目方、去中心化存储协议等。

### Danksharding—完整扩容方案
EIP-4844 实现了以太坊围绕 Rollup 进行扩容的第一步，但对于以太坊来说 EIP-4844 达到的扩容效果是远远不够的。完整的 Danksharding 方案将 Blob 可以承载的数据量从每个区块 1~2MB 进一步扩充至 16MB~32MB，并且提出了新的机制出块者-打包者分离（PBS）去解决 MEV 带来的问题。

那么我们需要知道要在 EIP-4844 的基础上继续进行扩容会存在哪些难题：

节点负担过大：我们知道 EIP-4844 中只有 1~2MB 大小的 Blob 对节点增加的负担是完全可以接受的，但如果将 Blob 的数据量扩大 16 倍至 16~32MB 的话，不管是在数据同步还是数据存储上的负担都会使得节点的负担过大从而以太坊的去中心化程度降低。

数据可用性问题：如果节点不去下载全部的 Blob 数据，就会面临数据可用性问题，因为数据不在链上开放且随时可访问，比如以太坊节点对 Optimism Rollup 上的某笔交易存疑想发起挑战，但 Optimism Rollup 不交出这段数据，那么拿不到原始数据就无法证明这个交易是有问题的，所以要解决数据可用性问题就必须确保数据是随时开放且可访问。

那么 Danksharding 是如何解决这些问题的呢？

#### 数据可用性采样（Data Availability Sampling）
Danksharding 提出了一个方案—数据可用性采样（Data Availability Sampling）来实现降低节点负担的同时也保证了数据可用性。

数据可用性采样（DAS）的思想是将 Blob 中的数据切割成数据碎片，并且让节点由下载 Blob 数据转变为随机抽查 Blob 数据碎片，让 Blob 的数据碎片分散在以太坊的每个节点中，但是完整的 Blob 数据却保存在整个以太坊账本中，前提是节点需要足够多且去中心化。

举个例子：比如 Blob 的数据被切割成了 10 个碎片，全网有 100 个节点，每个节点都会随机抽查下载一个数据碎片并且将抽查的碎片编号提交到区块中，只要一个区块中可以凑齐所有编号的碎片，那么以太坊就会默认这个 Blob 的数据是可用的，只要将碎片拼凑起来就可以还原出原始数据。但也会有极低的概率出现 100 个节点都没有抽到某一个编号的碎片的情况，这样数据就会缺失，在一定程度上降低了安全性，但在概率上是可以接受的。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-17.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

Danksharding 为了实现数据可用性采样（DAS）采用了两个技术：纠删码（Erasure Coding）和 KZG 多项式承诺（KZG Commitment）

#### 纠删码（Erasure Coding）
纠删码（Erasure Coding）是一种编码容错技术，使用纠删码切割数据可以使得所有以太坊节点在只拥有 50% 以上数据碎片的情况下就可以还原出原始数据，这样就大大减少了数据缺失的概率，具体的实现原理会比较复杂，这里用一个数学公式来举例子大概解释一下原理：
- 首先构造一个函数 f(x) = ax + b，随便取 4 个 x 值
- 设 m = f(0) = b、n = f(1) = a + b，可以得出 a = n – b、b = m
- 再设 p = f(2)、q = f(3)，可以得出 p = 2a + b = 2n – m, q = 3a + b = 3n – 2m
- 然后 m、n、p、q 四个碎片分散在全网的节点中
- 根据数学公式，我们只需要找到其中两个碎片就可以算出另外两个碎片是什么
- 如果找到 n 和 m，直接可以算出 q=3n-2m 和 p=2n-m
- 如果找到 q 和 p，可以将（2p=4n-2m）-（q=3n-2m）得出 2p-q=n 然后就可以直接算出 m 了

简单来说就是纠删码利用数学原理将 Blob 数据切割成很多个数据碎片，以太坊的节点不需要收集所有的数据碎片，只需要收集 50% 以上的碎片就可以还原出 Blob 的原始数据，这样极大的降低了碎片收集不够的概率，其概率可以忽略不计。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-18.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)


#### KZG 多项式承诺（KZG Commitment）
KZG 多项式承诺（KZG Commitment）是一种密码学技术，用来解决纠删码的数据完整性问题。由于节点只抽查被纠删码进行切割后的数据碎片，节点并不知道这个数据碎片是不是真的来源于 Blob 的原始数据，所以负责编码的角色还需要生成一个 KZG 多项式承诺来证明这个纠删码的数据碎片确实是原始数据中的一部分，KZG 的作用有点类似于默克尔树但形状不同，KZG 所有的证明都在同一个多项式上。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-19.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

Danksharding 通过纠删码和 KZG 多项式承诺实现了数据可用性采样（DAS）使得在 Blob 额外携带数据量扩充至 16MB~32MB 的情况下大幅的降低了节点的负担，目前以太坊社区还提出了一种叫 2D KZG scheme 的方案去进一步切割数据碎片降低带宽和计算量的要求，但最终具体使用的算法社区仍在热烈讨论中，包括 DAS 的设计上也在不断地优化完善。

对于以太坊来说数据可用性采样（DAS）解决了实现 Blob 数据量 16MB~32MB 扩容的同时降低了节点的负担，但似乎还存在一个问题：谁来对原始数据进行编码？

如果要对 Blob 原始数据进行编码的话前提是进行编码的节点手里必须有完整的原始数据，要做到这一点的话就会对节点有较高的要求。那么菠菜之前提到，Danksharding 提出了一个新的机制出块者-打包者分离（PBS）去解决 MEV 带来的问题，那么其实这个方案在解决 MEV 问题的同时，其实也解决了编码的问题。

#### 提议者-打包者分离（Proposer/Builder Separation）
首先我们知道数据可用性采样（DAS）降低了节点验证 Blob 的负担，实现了低配置和去中心化的验证，但创建这个区块的话就需要去拥有完整的 Blob 数据并进行编码处理，这提高了许多对以太坊全节点的要求，提议者-打包者分离（PBS）则提出将节点分为打包者（Builder）和提议者（Proposer）两个角色，性能高的节点可以成为打包者（Builder），而性能低的节点则成为提议者（Proposer）。

目前以太坊的节点分为两种：全节点和轻节点。全节点需要同步以太坊上所有的数据比如交易列表和区块 Body 等，全节点担任着区块打包和验证出块两个角色。由于全节点可以看见区块内所有的信息，所以全节点可以对区块的交易进行重新顺序或添加删除来获取 MEV 价值。轻节点则不需要同步所有的数据，只需要同步区块头负责验证出块即可。

在实现提议者-打包者分离（PBS）之后：

- 性能配置高的节点可以成为打包者（Builder），打包者只需要负责下载 Blob 数据进行编码并创建区块（Block），然后广播给其他的节点来进行抽查，对于打包者（Builder）来说，因为同步数据量和带宽要求较高，所以会相对中心化。
- 性能配置较低的节点可以成为提议者（Proposer），提议者只需要验证数据的有效性并创建和广播区块头（Block Header），但对于提议者（Proposer）来说，同步数据量和带宽要求较低，所以会去中心化。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-20.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

PBS 通过将打包和验证的角色进行分离实现了节点的工作分工，让性能配置高的节点负责下载全部数据进行编码分发，让性能配置低的节点来负责抽查验证，那么 MEV 问题是如何解决的呢？

#### 抗审查清单（crList）
由于 PBS 分离了打包和验证的工作，所以对于打包者（Builder）来说其实是拥有了更大的审查交易的能力，打包者可以故意忽略掉某些交易并且随意排序并插入自己想插入的交易去获取 MEV，但抗审查清单（crList）解决了这些问题。

抗审查清单（crList）的机制：

- 在打包者（Builder）打包区块交易之前，提议者（Proposer）会先公布一个抗审查清单（crList），这个 crList 中包含着 mempool 中的所有交易
- 打包者（Builder）只能选择打包并对 crList 里的交易进行排序，这意味着打包者不能插入自己的私有交易去获取 MEV，也不能去故意拒绝某个交易（除非 Gas limit 满了）
- 打包者（Builder）打包好之后将最终版本的交易列表 Hash 广播给提议者（Proposer），提议者选择其中一个交易列表生成区块头（Block Header）并广播
- 节点同步数据时会从提议者（Proposer）那获取区块头，然后从打包者（Builder）那获取区块 Body，确保区块 Body 是最终选择的版本

通过抗审查清单（crList）解决了像「三明治攻击」这种 MEV 带来的负面影响，节点无法再通过插入私有交易来获取类似的 MEV。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-21.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

以太坊关于 PBS 具体的实现方案仍在探讨中，目前可能初步实现的方案为双槽 PBS。

#### 双槽 PBS（Two-slot Proposer-Builder Separation）
双槽 PBS 采用竞标的模式来决定出块：

- 打包者（Builder）拿到 crList 后创建交易列表的区块头并出价
- 提议者（Proposer）选择最终竞标成功的区块头和打包者（Builder），提议者无条件获得中标费（不管是否生成有效区块）
- 验证委员会（Committees）确认中标的区块头
- 打包者（Builder）披露中标的区块 Body
- 验证委员会（Committees）确认中标的区块 Body 并进行验证投票（通过则出块，如果打包者故意不给区块 Body 则视为区块不存在）

虽然打包者（Builder）仍然可以通过调整交易顺序来获取 MEV，但双槽 PBS 的竞标机制使得这些打包者之间开始 “内卷”。在大家都要出价竞争出块的情况下，中心化的打包者们通过 MEV 获取的利润就会被不断挤压，最终的利润则会分配给去中心化的提议者（Proposer）们，这解决了中心化的打包者通过获取 MEV 越来越中心化的问题。

但双槽 PBS 有一个设计缺陷：我们看这个设计的名字中有 “双槽（Two-slot）”，就是有两个插槽 slot，这意味着在这个方案中进行一次有效的出块时间被延长至了 24 秒（一个 slot=12 秒），如何解决这个问题以太坊社区也一直在热烈讨论中。

![](https://cryptodao-1251547190.cos.ap-shanghai.myqcloud.com/dao/ethimage-22.png?imageMogr2/format/webp/interlace/1%7Cwatermark/1/image/aHR0cDovL2NyeXB0b2Rhby0xMjUxNTQ3MTkwLmNvcy5hcC1zaGFuZ2hhaS5teXFjbG91ZC5jb20vY2QtbG9nby5wbmc/dissolve/80/dx/30/dy/30/gravity/southeast/scatype/3/spcent/10)

## 四、总结
Danksharding 为以太坊解决 “区块链不可能三角” 提供了一种变革性的解决方案，即在确保以太坊去中心化和安全性的同时实现可扩展性：

- 通过前置方案 EIP-4844:Proto-Danksharding 引入了新的交易类型 Blob，Blob 携带的 1MB~2MB 额外数据量可以帮助以太坊在 Rollup 上实现更高的 TPS 和更低的成本
- 通过纠删码和 KZG 多项式承诺实现了数据可用性采样（DAS），让节点只需要抽查部分数据碎片即可验证数据的可用性并降低了节点的负担
- 通过实现了数据可用性采样（DAS），Blob 的额外数据量扩充至 16MB~32MB，让扩容效果更上一层楼
- 通过提议者-打包者分离（PBS）将验证-打包区块的工作分离为两个节点角色，实现了打包节点偏去中心化、验证节点去中心化
- 通过抗审查清单（crList）和双槽 PBS 极大降低了 MEV 带来的负面影响问题，打包者无法插入私有交易或审查某一笔交易

如果不出意外的话 Danksharding 的前置方案 EIP-4844 将会在以太坊上海升级之后的坎昆升级中正式落地，在 EIP-4844 方案落地实现后最直接的利好便是 Layer2 中的 Rollup 以及 Rollup 上的生态。更高的 TPS 和更低的成本十分适合链上的高频应用，我们不妨想象可能会诞生出一些 “杀手级应用”。Danksharding 实现的中心化出块+去中心化验证+抗审查将会给以太坊带来一轮全新的公链叙事，除了 Layer2 之外，模块化区块链与 Danksharding 后的以太坊又会碰撞出什么样的化学反应呢？

菠菜相信 Danksharding 的落地将会改写整个游戏规则，以太坊将会引领区块链行业走向一个新的时代！

## 五、参考文献
1. Data Availability，区块链的存储扩容
2. 一文读懂以太坊新升级方案 Danksharding
3. Proto-Danksharding FAQ – HackMD
4. V 神科普的「Danksharding」到底是什么？
5. V 神推荐丨深入了解以太坊的分片路线图，看这一份报告就足够
6. Buidler DAO 文章：如何在钱包被盗后从黑客手里抢救 NFT？
7. 历史重演？详解以太坊 2.0 与硬分叉

---

作者：[菠菜菠菜](https://twitter.com/wzxznl)，Web3Caff Research 研究员（重点研究方向为以太坊、DeFi、钱包、Layer2、区块链经济学，RMIT 在读区块链研究生，曾担任中国 2022 年区块链创新集编委会成员）

免责声明:  本报告由 Web3Caff Research 编写，所含信息仅供参考，不构成任何预测或投资建议、提议或要约，投资者请勿依赖此类信息购买、出售任何证券、加密货币或采取任何投资策略。报告中使用的术语和表达的观点旨在帮助理解行业动向，促进 Web3 包括区块链行业负责任发展，不应被解释为明确的法律观点或 Web3Caff Research 的观点。报告中的看法仅反映作者截至所述日期的个人意见，可能随后续情况而变化。