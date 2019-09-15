
# 7 回收硬盘空间（Reclaiming Disk Space）

## 原文与翻译

Once the latest transaction in a coin is buried under enough blocks, the spent transactions before it can be discarded to save disk space. To facilitate this without breaking the block's hash, transactions are hashed in a Merkle Tree **[2][5][7]** , with only the root included in the block's hash. Old blocks can then be compacted by stubbing off branches of the tree. The interior hashes do not need to be stored.

如果一枚硬币最近发生的交易发生在足够多的区块之前，那么，这笔交易之前该硬币的花销交易记录可以被丢弃 —— 目的是为了节省磁盘空间。为了在不破坏该区块的哈希的前提下实现此功能，交易记录的哈希将被纳入一个 Merkle 树 **[2][5][7]** 之中，而只有树根被纳入该区块的哈希之中。通过砍掉树枝方法，老区块即可被压缩。内部的哈希并不需要被保存。

![merkle_tree_in_7](pics/merkle_tree_in_7.svg)

A block header with no transactions would be about 80 bytes. If we suppose blocks are generated every 10 minutes, 80 bytes * 6 * 24 * 365 = 4.2MB per year. With computer systems typically selling with 2GB of RAM as of 2008, and Moore's Law predicting current growth of 1.2GB per year, storage should not be a problem even if the block headers must be kept in memory.

一个没有任何交易记录的区块头大约是 80 个字节。假设每十分钟产生一个区块，80 字节乘以 6 乘以 24 乘以 365，等于每年 4.2M。截止 2008 年，大多数在售的计算机配有 2GB 内存，而按照摩尔定律的预测，每年会增加 1.2 GB，即便是区块头必须存储在内存之中也不会是什么问题。

**[2]:** Design of a secure timestamping service with minimal trust requirements Henri Massias, Xavier Serret-Avila, Jean-Jacques Quisquater 20th Symposium on Information Theory in the Benelux (1999-05) http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.13.6228

**[5]:** Secure names for bit-strings Stuart Haber, W. Scott Stornetta Proceedings of the 4th ACM conference on Computer and communications security - CCS ’97(1997) https://doi.org/dtnrf6 DOI: 10.1145/266420.266430

**[7]:** Protocols for Public Key Cryptosystems Ralph C. Merkle 1980 IEEE Symposium on Security and Privacy (1980-04) https://doi.org/bmvbd6 DOI: 10.1109/sp.1980.10006

## Python 实现 Merkle Tree

【Python 知识点】 点击链接跳转至《自学是门手艺》相应知识点

- [函数](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/Part.2.D.1-args.ipynb)
- [类](https://github.com/selfteaching/the-craft-of-selfteaching/blob/master/Part.3.B.2.classes-2.ipynb)
- [数据容器](https://github.com/selfteaching/the-craft-of-selfteaching)

本实例改自 https://github.com/JaeDukSeo/Simple-Merkle-Tree-in-Python


```python
from IPython.display import display, Markdown

# 0. Import the needed library
import hashlib,json
from collections import OrderedDict

# 1. Declare the class trees
class Jae_MerkTree:

    # 2. Initiate the class object
    def __init__(self,listoftransaction=None):
        self.listoftransaction = listoftransaction
        self.past_transaction = OrderedDict()

    # 3. Create the Merkle Tree  
    def create_tree(self):

        # 3.0 Continue on the declaration
        listoftransaction = self.listoftransaction
        past_transaction = self.past_transaction
        temp_transaction = []

        # 3.1 Loop until the list finishes
        for index in range(0,len(listoftransaction),2):

            # 3.2 Get the most left element 
            current = listoftransaction[index]

            # 3.3 If there is still index left get the right of the left most element
            if index+1 != len(listoftransaction):
                current_right = listoftransaction[index+1]

            # 3.4 If we reached the limit of the list then make a empty string
            else:
                current_right = ''

            # 3.5 Apply the Hash 256 function to the current values
            current_hash = hashlib.sha256(current.encode())

            # 3.6 If the current right hash is not a '' <- empty string
            if current_right != '':
                current_right_hash = hashlib.sha256(current_right.encode())

            # 3.7 Add the Transaction to the dictionary 
            past_transaction[listoftransaction[index]] = current_hash.hexdigest()

            # 3.8 If the next right is not empty
            if current_right != '':
                past_transaction[listoftransaction[index+1]] = current_right_hash.hexdigest()

            # 3.9 Create the new list of transaction
            if current_right != '':
                temp_transaction.append(current_hash.hexdigest() + current_right_hash.hexdigest())

            # 3.01 If the left most is an empty string then only add the current value
            else:
                temp_transaction.append(current_hash.hexdigest())

        # 3.02 Update the variables and rerun the function again 
        if len(listoftransaction) != 1:
            self.listoftransaction = temp_transaction
            self.past_transaction = past_transaction

            # 3.03 Call the function repeatly again and again until we get the root 
            self.create_tree()

    # 4. Return the past Transaction 
    def get_past_transacion(self):
        return self.past_transaction

    # 5. Get the root of the transaction
    def get_root_leaf(self):
        last_key = list(self.past_transaction.keys())[-1]
        return self.past_transaction[last_key]
```


```python
# a) Create the new class of Jae_MerkTree
Jae_Tree = Jae_MerkTree()

# b) Give list of transaction
transaction = ['a','b','c','d']

# c) pass on the transaction list 
Jae_Tree.listoftransaction = transaction

# d) Create the Merkle Tree transaction
Jae_Tree.create_tree()

# e) Retrieve the transaction 
past_transaction = Jae_Tree.get_past_transacion()

# f) Get the last transaction and print all 
display(Markdown("**First Example - Even number of transaction Merkel Tree**"))
display(Markdown('**Final root of the tree:** \n\n' + Jae_Tree.get_root_leaf()))
display(Markdown("**merkle tree:** \n\n" + json.dumps(past_transaction, indent=4)))
```


**First Example - Even number of transaction Merkel Tree**



**Final root of the tree:** 

58c89d709329eb37285837b042ab6ff72c7c8f74de0446b091b6a0131c102cfd



**merkle tree:** 

{
    "a": "ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb",
    "b": "3e23e8160039594a33894f6564e1b1348bbd7a0088d42c4acb73eeaed59c009d",
    "c": "2e7d2c03a9507ae265ecf5b5356885a53393a2029d241394997265a1a25aefc6",
    "d": "18ac3e7343f016890c510e93f935261169d9e3f565436429830faf0934f4f8e4",
    "ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb3e23e8160039594a33894f6564e1b1348bbd7a0088d42c4acb73eeaed59c009d": "62af5c3cb8da3e4f25061e829ebeea5c7513c54949115b1acc225930a90154da",
    "2e7d2c03a9507ae265ecf5b5356885a53393a2029d241394997265a1a25aefc618ac3e7343f016890c510e93f935261169d9e3f565436429830faf0934f4f8e4": "d3a0f1c792ccf7f1708d5422696263e35755a86917ea76ef9242bd4a8cf4891a",
    "62af5c3cb8da3e4f25061e829ebeea5c7513c54949115b1acc225930a90154dad3a0f1c792ccf7f1708d5422696263e35755a86917ea76ef9242bd4a8cf4891a": "58c89d709329eb37285837b042ab6ff72c7c8f74de0446b091b6a0131c102cfd"
}


**【小练习】**

基于上面的代码，尝试绘制可视化的 Merkle Tree。
