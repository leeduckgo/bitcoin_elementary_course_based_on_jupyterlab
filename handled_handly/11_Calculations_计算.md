
# 11 计算（Calculations）

## 原文与翻译
We consider the scenario of an attacker trying to generate an alternate chain faster than the honest chain. Even if this is accomplished, it does not throw the system open to arbitrary changes, such as creating value out of thin air or taking money that never belonged to the attacker. Nodes are not going to accept an invalid transaction as payment, and honest nodes will never accept a block containing them. An attacker can only try to change one of his own transactions to take back money he recently spent.

假设一个场景，某个攻击者正在试图生成一个比诚实链更快的替代链。就算他成功了，也不会使当前系统置于模棱两可的尴尬境地，即，他不可能凭空制造出价值，也无法获取从未属于他的钱。网络节点不会把一笔无效交易当作支付，而诚实节点也永远不会接受一个包含这种支付的区块。攻击者最多只能修改属于他自己的交易，进而试图取回他已经花出去的钱**（双重支付）**。

The race between the honest chain and an attacker chain can be characterized as a Binomial Random Walk. The success event is the honest chain being extended by one block, increasing its lead by +1, and the failure event is the attacker's chain being extended by one block, reducing the gap by -1.

诚实链和攻击者之间的竞争可以用二项随机漫步来描述。成功事件是诚实链刚刚被添加了一个新的区块，使得它的优势增加了 $1$；而失败事件是攻击者的链刚刚被增加了一个新的区块，使得诚实链的优势减少了 $1$。

The probability of an attacker catching up from a given deficit is analogous to a Gambler's Ruin problem. Suppose a gambler with unlimited credit starts at a deficit and plays potentially an infinite number of trials to try to reach breakeven. We can calculate the probability he ever reaches breakeven, or that an attacker ever catches up with the honest chain, as follows[^8]:

攻击者能够从落后局面追平的概率类似于赌徒破产问题。假设，一个拿着无限筹码的赌徒，从亏空开始，允许他赌无限次，目标是填补上已有的亏空。我们能算出他最终能填补亏空的概率，也就是攻击者能够赶上诚实链的概率[^8]，如下：

$$ \begin{eqnarray} \large p &=& \text{ 诚实节点找到下一个区块的概率}\ \\ \large q &=& \text{ 攻击者找到下一个区块的概率}\ \\ \large q_z &=& \text{ 攻击者落后 $z$ 个区块却依然能够赶上的概率} \end{eqnarray} $$

$$ \large q_z = \begin{Bmatrix} 1 & \textit{if } p \leq q\ \\\\ (q/p)^z & \textit{if } p > q \end{Bmatrix} $$

Given our assumption that $p \gt q$, the probability drops exponentially as the number of blocks the attacker has to catch up with increases. With the odds against him, if he doesn't make a lucky lunge forward early on, his chances become vanishingly small as he falls further behind.

既然我们已经假定 $p > q$, 既然攻击者需要赶超的区块数量越来越多，那么其成功概率就会指数级下降。于赢面不利时，如果攻击者没有在起初就能幸运地做一个前移步刺，那么他的胜率将在他进一步落后的同时消弭殆尽。

We now consider how long the recipient of a new transaction needs to wait before being sufficiently certain the sender can't change the transaction. We assume the sender is an attacker who wants to make the recipient believe he paid him for a while, then switch it to pay back to himself after some time has passed. The receiver will be alerted when that happens, but the sender hopes it will be too late.

现在考虑一下一笔新交易的收款人需要等多久才能充分确定发款人不能更改这笔交易。我们假定发款人是个攻击者，妄图让收款人在一段时间里相信他已经支付对付款项，随后将这笔钱再转回给自己。发生这种情况时，收款人当然会收到警告，但发款人希望那时木已成舟。

The receiver generates a new key pair and gives the public key to the sender shortly before signing. This prevents the sender from preparing a chain of blocks ahead of time by working on it continuously until he is lucky enough to get far enough ahead, then executing the transaction at that moment. Once the transaction is sent, the dishonest sender starts working in secret on a parallel chain containing an alternate version of his transaction.

收款人生成了一对新的公私钥，而后在签署之前不久将公钥告知发款人。这样可以防止一种情形：发款人提前通过连续运算去准备一条链上的区块，并且只要有足够的运气就会足够领先，直到那时再执行交易。一旦款项已被发出，那个不诚实的发款人开始秘密地在另一条平行链上开工，试图在其中加入一个反向版本的交易。

The recipient waits until the transaction has been added to a block and $z$ blocks have been linked after it. He doesn't know the exact amount of progress the attacker has made, but assuming the honest blocks took the average expected time per block, the attacker's potential progress will be a Poisson distribution with expected value:

收款人等到此笔交易被打包进区块，并已经有 $z$ 个区块随后被加入。他并不知道攻击者的工作进展究竟如何，但是假定诚实区块按照期望的平均速度生成，则攻击者的潜在进展符合泊松分布，其期望值为：

$$ \large \lambda = z \frac qp $$

To get the probability the attacker could still catch up now, we multiply the Poisson density for each amount of progress he could have made by the probability he could catch up from that point:

为了算出攻击者依然可以赶上的概率，我们要把每一个攻击者已有的进展的泊松密度，乘以他可以从那一点能够追上来的概率：

$$ \large \sum_{k=0}^{\infty} \frac{\lambda^k e^{-\lambda}}{k!} \cdot \begin{Bmatrix} (q/p)^{(z-k)} & \textit{if } k\leq z\ \\\\ 1 & \textit{if } k > z \end{Bmatrix} $$

![attack_probability](pics/attack_probability.jpg)

Rearranging to avoid summing the infinite tail of the distribution...

为了避免对密度分布的无穷级数求和重新整理…

$$ \large 1 - \sum_{k=0}^{z} \frac{\lambda^k e^{-\lambda}}{k!} \left ( 1-(q/p)^{(z-k)} \right ) $$

Converting to C code...

转换为 C 语言程序……

```
#include <math.h>
double AttackerSuccessProbability(double q, int z)
{
	double p = 1.0 - q;
	double lambda = z * (q / p);
	double sum = 1.0;
	int i, k;
	for (k = 0; k <= z; k++)
	{
		double poisson = exp(-lambda);
		for (i = 1; i <= k; i++)
			poisson *= lambda / i;
		sum -= poisson * (1 - pow(q / p, z - k));
	}
	return sum;
}

```

Running some results, we can see the probability drop off exponentially with $z$.

获取部分结果，我们可以看到概率随着 $z$ 的增加指数级下降：

```
   q=0.1
   z=0    P=1.0000000
   z=1    P=0.2045873
   z=2    P=0.0509779
   z=3    P=0.0131722
   z=4    P=0.0034552
   z=5    P=0.0009137
   z=6    P=0.0002428
   z=7    P=0.0000647
   z=8    P=0.0000173
   z=9    P=0.0000046
   z=10   P=0.0000012
   
   q=0.3
   z=0    P=1.0000000
   z=5    P=0.1773523
   z=10   P=0.0416605
   z=15   P=0.0101008
   z=20   P=0.0024804
   z=25   P=0.0006132
   z=30   P=0.0001522
   z=35   P=0.0000379
   z=40   P=0.0000095
   z=45   P=0.0000024
   z=50   P=0.0000006
```

Solving for P less than 0.1%...

若是 P 小于 0.1%……

```
   P < 0.001
   q=0.10   z=5
   q=0.15   z=8
   q=0.20   z=11
   q=0.25   z=15
   q=0.30   z=24
   q=0.35   z=41
   q=0.40   z=89
   q=0.45   z=340
```

**[8]** An Introduction to Probability Theory and its Applications** William Feller *John Wiley & Sons* (1957) https://archive.org/details/AnIntroductionToProbabilityTheoryAndItsApplicationsVolume1

## 赌徒破产问题

这是一个题外话，但因为是一个很有意义的主题，所以还是插入到了这里……


>假设有两个人玩公平的抛硬币赌输赢的游戏，规则是：
>
>赌注大小恒定
>
>直至一方输光游戏才能结束
>
>请问，最终决定输赢的是什么（单选）？
>
>**A.手气 B.谁先抛硬币 C.抛硬币次数 D.总游戏时长 E.以上皆是 F.以上皆不是**

答案是什么？这篇文章对此做了很清晰的阐述——

[理财：别做险盲](http://xiaolai.co/books/1ef3bd91da19da1423a7c04fe1882ec9/B13.html)

## 赌博破产问题 Python 模拟

调整以下代码中的参数，会发现一些有意思的结果。例如——

- 把 round_num 改成 1000 试试

- 庄家来一点「抽水」

【Python 知识点】 点击链接跳转至《自学是门手艺》相应知识点
- [函数](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/Part.2.D.1-args.ipynb)
- [流程控制 —— for 循环](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/Part.1.E.3.controlflow.ipynb)


```python
import random
'''
抛硬币游戏：
抛硬币，正反两面概率各50%，正面你赢，反面庄家赢。
10位玩家，赌本10元，进行100轮。
'''


round_num = 10
person_num = 10
player_total_win_money = 0

for person in range(1,person_num + 1):
    
    gambling_money = 10
    
    for r in range(1,round_num + 1):
        coin = random.randint(0, 1) # 0为正，1为反
        if coin == 0 :
            gambling_money = gambling_money + 1 
        elif coin == 1 :
            gambling_money = gambling_money - 1
        
        if gambling_money == 0 :
            # 因破产而退出赌博
            break
        else:
            pass
    player_total_win_money += gambling_money - 10
    print("第 {} 号玩家经过 {} 轮赌博，最后的资产为 {} 元".format(person,r,gambling_money))
print("=========")
if player_total_win_money > 0:
    print("玩家赢！赢了：{} 元".format(player_total_win_money))
elif player_total_win_money <0:
    print("庄家赢！赢了：{} 元".format(-player_total_win_money))
else:
    print("庄家和玩家平手！")
```

    第 1 号玩家经过 10 轮赌博，最后的资产为 4 元
    第 2 号玩家经过 10 轮赌博，最后的资产为 6 元
    第 3 号玩家经过 10 轮赌博，最后的资产为 6 元
    第 4 号玩家经过 10 轮赌博，最后的资产为 0 元
    第 5 号玩家经过 10 轮赌博，最后的资产为 12 元
    第 6 号玩家经过 10 轮赌博，最后的资产为 6 元
    第 7 号玩家经过 10 轮赌博，最后的资产为 12 元
    第 8 号玩家经过 10 轮赌博，最后的资产为 8 元
    第 9 号玩家经过 10 轮赌博，最后的资产为 12 元
    第 10 号玩家经过 10 轮赌博，最后的资产为 8 元
    =========
    庄家赢！赢了：26 元


## 数学概念解析

- **二项随机漫步**

如图所示，本节中的「二项随机漫步」可以看做两个人在玩大富翁游戏。在落后 z 步的情况下丢骰子，如果骰数大于 x ，则攻击者前进一步；如果骰数小于 x ，则诚实节点前进一步。

- **泊松分布**

泊松分布适合于描述单位时间内随机事件发生的次数的概率分布。如：

**某一服务设施在一定时间内受到的服务请求的次数，电话交换机接到呼叫的次数、汽车站台的候客人数、机器出现的故障数、自然灾害发生的次数、DNA 序列的变异数、放射性原子核的衰变数、激光的光子数分布等等。**

攻击链延长的数量（进展）同样符合泊松分布。


## 延伸阅读
如果读到这里还是觉得不懂，可以看这个链接，描述得很详细：

https://zhuanlan.zhihu.com/p/56151888

## 攻击者追块概率计算 Python 版

【Python 知识点】
- [math库文档](https://docs.python.org/zh-cn/3/library/math.html)


```python
import math
def attacker_success_probability(q, z):   
    sum = 1.0   
    p = 1.0 - q    
    lamba = z * (q / p)   
    i = 0;   
    k = 0;   
    for k in range(z + 1):      
        poisson = math.exp(-lamba)
        for i in range(1, k + 1): 
            poisson *= (lamba / i) 
        sum -= poisson * (1 - pow(q / p, z - k))
    return sum

```


```python
q= 0.1
print("q = {}".format(q))
for z in range(0,10):
    prob = attacker_success_probability(q, z)
    print(" z = {}, P = {:.10f}".format(z, prob))
```

    q = 0.1
     z = 0, P = 1.0000000000
     z = 1, P = 0.2045872739
     z = 2, P = 0.0509778928
     z = 3, P = 0.0131722417
     z = 4, P = 0.0034552435
     z = 5, P = 0.0009136822
     z = 6, P = 0.0002428027
     z = 7, P = 0.0000647353
     z = 8, P = 0.0000172998
     z = 9, P = 0.0000046312



```python
q= 0.3
print("q = {}".format(q))
for z in range(0,50,5):
    prob = attacker_success_probability(q, z)
    print(" z = {}, P = {:.10f}".format(z, prob))
```

    q = 0.3
     z = 0, P = 1.0000000000
     z = 5, P = 0.1773523114
     z = 10, P = 0.0416604800
     z = 15, P = 0.0101007622
     z = 20, P = 0.0024803982
     z = 25, P = 0.0006132284
     z = 30, P = 0.0001522339
     z = 35, P = 0.0000378958
     z = 40, P = 0.0000094517
     z = 45, P = 0.0000023608

