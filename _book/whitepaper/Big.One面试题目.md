## 分布式系统

1. 什么场景下你会在两个系统中采用异步通信机制？
2. 怎么测试一个分布式系统？
3. RPC 的通用缺点是什么？
4. 如何实现分布式系统中，多个组件之间的一致性（幂等实现最终一致性、分布式事务）

## 开放式问题

1. 作为一个软件工程师，你想要既要有创新力，又要产出具有可预测性。采用什么策略才能使这两个目标可以共存呢？
2. 什么是好的代码？
3. 假设你的公司给你一周的时间，用来改善你和同事的生活，你将如何使用这一周？
4. 列出最近你读过的5本书。
5. 在你当前的工作流中，什么事情是你计划下一步需要自动化的？

## 基础知识

Git 的使用、HTTP 协议、进程线程协程区别、死锁的定义等基础知识

1. HTTP 中 GET 和 POST 的区别？说出用法和实际请求内容上的差异。

2. 举例说出 6 个 HTTP 的状态码及其作用

3. 说出 HTTPS 的原理？设计 API 接口如何保证安全调用且不会被重放攻击？

4. TCP 和 UDP 的区别

5. 什么是进程、什么是线程、什么是协程、什么是线程池？有什么区别？

6. 什么是死锁？发生条件是什么？

7. git 中 rebase, squash, amend 的作用

8. 如何重构这段代码？

   ```go
   function() {
       RESULT error = S_OK;
       if(SUCCEEDED(Operation1())) {
           if(SUCCEEDED(Operation2())) {
               if(SUCCEEDED(Operation3())) {
               } else {
                   error = OPERATION3FAILED;
               }
           } else {
               error = OPERATION2FAILED;
           }
       } else {
           error = OPERATION1FAILED;
       }
       return error;
   }
   ```

## 语言相关问题

1. 告诉我你的首选语言的三个最坏的缺陷。
2. 闭包是什么？它有什么用途？
3. 泛型有什么用途？
4. 你认为好的语言好在哪里？差的语言差在哪里？
5. 为什么有些语言设计上没有异常机制？这有什么优缺点？
6. 什么是动态方法派发(Dynamic Method Dispatch)？
7. 什么是栈？什么是堆？

## 数据库

1. 建立一个 (A, B, C) 三个字段的联合索引，哪些查询可以命中索引？（需要能解释到哪些范围查询可以命中，如果能解释清楚为什么会产生这样的结果最好）
2. `INNER JOIN`, `OUTER JOIN`, `LEFT JOIN`, `RIGHT JOIN` 的区别
3. 什么是事务，事务和锁的关系是什么？
4. 什么是悲观锁和乐观锁？分别应用在什么场景？
5. 什么是第三范式（3NF）？
6. MySQL 如何实现主从复制？具体到内部原理和流程。
7. 线上 MySQL 服务 CPU 飙升到 100%，如何诊断问题。
8. MySQL 四种事务隔离级别的不同之处。
9. 解释 InnoDB 事务的实现方式？（高级问题）
10. 什么是 N+1 问题？

# 大数据

1. HDFS/Yarn 的系统架构
2. Spark 宽窄依赖/任务提交流程/Shuffle 过程
3. Kafka 的系统架构/ZeroCopy/ISR 机制
4. Zookeeper 系统架构
5. HBase 系统架构/布隆过滤器/LSMTree
6. Elasticsearch 系统架构/倒排索引
7. 分布式协议：Paxos/Raft

# 算法

1. 在不调用库函数的前提下，给定正整数n，给按字典序从小到大输出1-n的全排列
2. 给定一个栈或者双端队列，给定序列1-n，定义合理的操作为把序列当前第一个元素入栈，栈内元素弹出（入双端队列，出双端队列），那么按出容器的顺序可以生成新的序列， 给定1-n的一个排列，如何判断是否能由如上的操作生成，你的程序算法复杂度为多少

## Go 语言相关

1. cap 和 len 分别获取的是什么？
2. 解释下 context 是做什么用的，为什么需要这个东西。
3. Go 的内存回收是如何做到的？如果发现线上某程序内存持续增长，可能会是哪些原因？（考垃圾收集、goroutine 泄露、连接泄露、channel 泄露）
4. 除了 mutex 以外还有那些方式安全读写共享变量？
5. Go 采用什么并发模型？体现在哪里？
6. 在 Vendor 特性之前包管理工具是怎么实现的？
7. 你如何 debug Go 程序?
8. close channel 的行为
9. defer 的执行顺序

## Go 高级知识

1. GMP 模型，goroutine 是如何调度的。P 和 os thread 的关系。（如果懂 erlang 或 lua 的话可以做下对比）
2. 如何实现一个简单的 goroutine leak 检测库，用于在测试运行结束后打印出所测试程序泄露的 goroutine 的 stacktrace
3. 有没有实际用过 sync.Pool ，怎么用的？
4. go 实现一个类似 sidekiq 的系统。（服务A产生三种不同权重的任务类型，如何调度？）

以下代码的执行顺序

```go
package main

import (
    "fmt"
)

func main() {
    defer_call()
}

func defer_call() {
    defer func() { fmt.Println("打印前") }()
    defer func() { fmt.Println("打印中") }()
    defer func() { fmt.Println("打印后") }()

    panic("触发异常")
}
```

A: 先执行 defer 在执行 panic，defer 按照后进先出的顺序执行。

指出以下代码的问题

```go
type student struct {
    Name string
    Age  int
}

func pase_student() {
    m := make(map[string]*student)
    stus := []student{
        {Name: "zhou", Age: 24},
        {Name: "li", Age: 23},
        {Name: "wang", Age: 22},
    }
    for _, stu := range stus {
        m[stu.Name] = &stu
    }

}
```

A: &stu 指向的都是同一个内存地址

以下代码怎么输出

```go
func main() {
    runtime.GOMAXPROCS(1)
    wg := sync.WaitGroup{}
    wg.Add(20)
    for i := 0; i < 10; i++ {
        go func() {
            fmt.Println("A: ", i)
            wg.Done()
        }()
    }
    for i := 0; i < 10; i++ {
        go func(i int) {
            fmt.Println("B: ", i)
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```

A: showA；showB

以下代码能编译通过吗？为什么？

```go
package main

import (
	"fmt"
)

type People interface {
	Speak(string) string
}

type Stduent struct{}

func (stu *Stduent) Speak(think string) (talk string) {
	if think == "bitch" {
		talk = "You are a good boy"
	} else {
		talk = "hi"
	}
	return
}

func main() {
	var peo People = Stduent{}
	think := "bitch"
	fmt.Println(peo.Speak(think))
}
```

A: 不能。两种方法，要么 peo = &Stduent{} 要么 Speak 方法去掉pointer

以下代码会怎么执行

```go
package main

func main() {
	c := make(chan string, 10)
	close(c)
	c <- "test"
}
```

A: 写入 channel 的时候会 panic