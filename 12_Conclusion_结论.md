
# 12 结论（Conclusion）

## 原文与翻译

We have proposed a system for electronic transactions without relying on trust. We started with the usual framework of coins made from digital signatures, which provides strong control of ownership, but is incomplete without a way to prevent double-spending. To solve this, we proposed a peer-to-peer network using proof-of-work to record a public history of transactions that quickly becomes computationally impractical for an attacker to change if honest nodes control a majority of CPU power. The network is robust in its unstructured simplicity. Nodes work all at once with little coordination. They do not need to be identified, since messages are not routed to any particular place and only need to be delivered on a best effort basis. Nodes can leave and rejoin the network at will, accepting the proof-of-work chain as proof of what happened while they were gone. They vote with their CPU power, expressing their acceptance of valid blocks by working on extending them and rejecting invalid blocks by refusing to work on them. Any needed rules and incentives can be enforced with this consensus mechanism.

我们提出了一个不必依赖信任的电子交易系统。起点是一个普通的使用数字签名的硬币框架开始，虽然它提供了健壮的所有权控制，却无法避免双重支付。为了解决这个问题，我们提出一个使用工作证明机制的点对点网络去记录一个公开的交易记录历史，只要诚实节点能够控制大多数 CPU 算力，那么攻击者就仅从算力方面就不可能成功篡改系统。这个网络的健壮在于它的无结构的简单。节点们可以在很少协同的情况下瞬间同时工作。它们甚至不需要被辨认，因为消息的路径并非取决于特定的终点；消息只需要被以最大努力为基本去传播即可。节点来去自由，重新加入时，只需要接受工作证明链，作为它们离线之时所发生之一切的证明。它们通过它们的 CPU 算力投票，通过不断为链添加新的有效区块、拒绝无效区块，去表示它们对有效交易的接受与否。任何必要的规则和奖励都可以通过这个共识机制来强制实施。

![bitcoin_overview](pics/bitcoin_overview.png)

## 一点私人感悟

经过 10 多年的发展，比特币已经由当初的「社会实验」变成了一个异常成功的「银行系统」，甚至由此延伸出了一整个被称作「区块链」的世界。

未来会如何演进？我们难以做出预测。但是，**来自未来的风已经吹起来了**。

这本教程一方面写给自己，因为我始终坚信，搞懂比特币是搞懂区块链的基础；

另一方面，也是写给所有有志于进入区块链世界的探险者们，**愿大家能从这个入口了解这个全新的世界，在这个全新的世界里开疆拓土。**

## 进阶课程
**[基于 JupyterLab 的比特币教程（进阶）](https://github.com/leeduckgo/bitcoin_advanced_course_based_on_jupyterlab)**

**目录**
- 前言
- 优质的比特币保存方案
- P2SH 与多重签名
- 比特币升级之路
  - 大区块方案
  - 隔离见证
- 分布式系统与比特币
  - 传统分布式系统简史
  - BFT 家族
  - BFT 与 PoW
  - 【思考】传统 BFT 能简单的移植吗？
- PoS
- DPoS
- 比特币二层网络
  - 闪电⚡️网络
  - 其它二层网络项目
- 【概念辨析】Complexity_复杂度
- 【概念辨析】Throughput_and_Latency_吞吐与延迟
- 【可能的未来】Move 与 Libra 
- 比特币相关资源
