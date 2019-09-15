
# 4 工作量证明 (Proof-of-Work)

## 原文与翻译

To implement a distributed timestamp server on a peer-to-peer basis, we will need to use a proof-of-work system similar to Adam Back's Hashcash**[6]**, rather than newspaper or Usenet posts. The proof-of-work involves scanning for a value that when hashed, such as with SHA-256, the hash begins with a number of zero bits. The average work required is exponential in the number of zero bits required and can be verified by executing a single hash.

为了实现一个基于点对点的分布式时间戳服务器，我们需要使用类似亚当·伯克的哈希现金**[6]**那样的一个工作证明系统，而不是报纸或者新闻组帖子那样的东西。所谓的工作证明，就是去寻找一个数值；这个数值要满足以下条件：为它提取散列数值之后 —— 例如使用 SHA-256 计算散列数值 —— 这个散列数值必须以一定数量的 0 开头。每增加一个 0 的要求，将使得工作量指数级增加，并且，这个工作量的验证却只需通过计算一个哈希。

For our timestamp network, we implement the proof-of-work by incrementing a nonce in the block until a value is found that gives the block's hash the required zero bits. Once the CPU effort has been expended to make it satisfy the proof-of-work, the block cannot be changed without redoing the work. As later blocks are chained after it, the work to change the block would include redoing all the blocks after it.

在我们的时间戳网络中，我们是这样实现工作证明的：不断在区块之中增加一个随机数（Nonce），直到一个满足条件的数值被找到；这个条件就是，这个区块的哈希以指定数量的 0 开头。一旦 CPU 的耗费算力所获的的结果满足工作证明，那么这个区块将不再能被更改，除非重新完成之前的所有工作量。随着新的区块不断被添加进来，改变当前区块即意味着说要重新完成所有其后区块的工作。

![proof-of-work](pics/proof-of-work.png)

The proof-of-work also solves the problem of determining representation in majority decision making. If the majority were based on one-IP-address-one-vote, it could be subverted by anyone able to allocate many IPs. Proof-of-work is essentially one-CPU-one-vote. The majority decision is represented by the longest chain, which has the greatest proof-of-work effort invested in it. If a majority of CPU power is controlled by honest nodes, the honest chain will grow the fastest and outpace any competing chains. To modify a past block, an attacker would have to redo the proof-of-work of the block and all blocks after it and then catch up with and surpass the work of the honest nodes. We will show later that the probability of a slower attacker catching up diminishes exponentially as subsequent blocks are added.

工作证明同时解决了如何决定谁能代表大多数做决定的问题。如果所谓的“大多数”是基于“一个 IP 地址一票”的方式决定的话，那么任何一个可以搞定很多 IP 地址的人就可以被认为是“大多数”。工作证明本质上来看，是“一个 CPU（可以理解为一份算力） 一票”。所谓的“大多数决定”是由最长链所代表的，因为被投入最多工作的链就是它。如果大多数 CPU 算力被诚实的节点所控制，那么诚实链成长最为迅速，其速度会远超其他竞争链。为了更改一个已经产生的区块，攻击者将不得不重新完成那个区块以及所有其后区块的的工作证明，而后还要追上并超过诚实节点的工作。后文展示为什么一个被拖延了的攻击者能够追上的可能性将随着区块的不断增加而指数级降低。

To compensate for increasing hardware speed and varying interest in running nodes over time, the proof-of-work difficulty is determined by a moving average targeting an average number of blocks per hour. If they're generated too fast, the difficulty increases.

**为了应对硬件算力综合的不断增加，以及随着时间推进可能产生的节点参与数量变化，工作证明难度由此决定：基于平均每小时产生的区块数量的一个移动平均值。如果区块生成得过快，那么难度将会增加。**

## 重要概念解析

- [Nonce](https://en.bitcoin.it/wiki/Nonce)

![nonce](pics/nonce.png)

比特币区块中的 nonce 是一个 32 位（4 字节）的字段。有了这个字段，区块的哈希值（一串 16 进制数字）小于或等于目前网络的目标，也即表示矿工实现了某个工作量。这个字段和其它字段是独立的，也即不会影响到其它的字段。 

需要注意的是，Nonce 是一个变化的值。可以把它当成比特币系统的一个**「操控杆」**。正是有了这个操控杆，不管有多少算力投入了这个系统，也能保证平均十分钟出一个块。

## Nonce 生成实战

【Python 知识点】 点击链接跳转至《自学是门手艺》相应知识点
- [函数](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/Part.2.D.1-args.ipynb)
- [流程控制 —— if 语句与 while 循环](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/Part.1.E.3.controlflow.ipynb)


```python
import hashlib
import random
import time

text = "Long bitcoin, short the world."

# 定义 valid 函数，如果生成的 hash 开头有 zero_num 个零，则 nonce 达到要求
def valid(hash,zero_num):
    return hash[0: zero_num] == "0" * zero_num
```


```python
# 通过随机碰撞💥寻找 Nonce 的函数
def find_nonce(text, zero_num):
    start = time.time()
    find = False
    while not find:
        nonce = random.randint(1, 10000000)
        payload = (text + str(nonce)).encode()
        hash = hashlib.sha256(payload).hexdigest()
        if valid(hash,zero_num):
            print("sha256({} + text) => {}".format(nonce, hash))
            print("nonce is {}".format(nonce))
            end = time.time()
            print("找到 nonce 时间: {}".format(end-start))
            find = True
```


```python
find_nonce(text, 1)
```

    sha256(8676133 + text) => 0ae8c647021d4f38c4325fe38f7474ea6b8ceb851632e40eb0abfddcc472e5df
    nonce is 8676133
    找到 nonce 时间: 0.0003352165222167969



```python
find_nonce(text, 2)
```

    sha256(4350470 + text) => 00165f427b28a8e8ec8a83e5cab331a43121ef9752a7c821d8a666a4b57f3af9
    nonce is 4350470
    找到 nonce 时间: 0.002769947052001953



```python
find_nonce(text, 3)
```

    sha256(7990813 + text) => 000a4e2d72e1d447f9a564d718434db5eaf5ba2690b42207d952a640bfeb1ef9
    nonce is 7990813
    找到 nonce 时间: 0.006875276565551758



```python
find_nonce(text, 5)
```

    sha256(8812787 + text) => 000007e8cf2efd2ad71a5e207a112c60d35aef3bc227e707727365d6982d3ae2
    nonce is 8812787
    找到 nonce 时间: 0.07970809936523438


上述实验表现了如下事实：

**1）计算 Nonce 需要花费时间进行随机碰撞，但是验证 Nonce 只需要一次哈希运算；**

**2）调整难度便是调整「出块速度」。**
