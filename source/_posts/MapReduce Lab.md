---
title: MapReduce Lab
url: mapreduce-lab
date: 2023-11-25 18:01:00
description: MIT 6.824 Lab 1
categories: Computer Science
tags: [System,Projects]
---

## 开篇废话

自从去年这个时候找完工作，就再也没有认真地、系统地了解一些专业相关的东西，上班以后更是放飞自我，再不搞搞真的没人要了😭。思来想去，还是决定深入了解下分布式系统，其一，分布式现在确实是一切计算与存储的基石，无处不在；其二，感觉的确对这块有些兴趣，想深入了解。

众所周知，想要深入、全面了解一个比较成熟的话题，要么看书，要么上课。看书实在是太折磨了，所以这次决定好好整整6.824这门课（服了，几年前也是这么说的）：

- notes: 每节课都有了，不用自己记笔记
- video: 打算看Robert Morris的，油管自取
- paper: 硬着头皮，结合各路大神的理解看吧
- lab: Spring 2023，重中之重，据说爆难，应该会写博客记录吧

## Env setup

- Mac or Linux, Windows/WSL seems impossible
- Go 1.15 or later
- VS Code with extensions

```bash
apt install golang
export PATH=$PATH:/usr/local/go/bin  # add this to .bashrc
```

## Sequential MR

Application(`wc.go/indexer.go`)已经实现了`Map`和`Reduce`，`mrsequential.go`用single process实现了串行的MR，即：

1. 对每个splitted的文件，通过`mapf`得到包含`(word,"1")`这种kv对的数组`kva`，展开后`append`到大数组`intermediate`
2. 将`intermediate`按照key排序，使得相同key的kv pair聚在一起
3. 对每个distinct key，将其对应的值`append`到`values`，传给`reducef`统计，输出结果

`go build -buildmode=plugin ../mrapps/wc.go`将application编译为`.so`文件，在`mrsequential.go`里通过`loadPlugin`调用`.so`文件拿到`mapf/reducef`，即`go run mrsequential.go wc.so pg*.txt`，最终的统计结果在`mr-out-0`中

## Distributed MR

一个coordinator process，多个并行的worker processes，coordinator和worker之间通过RPC通信。coordinator和worker的入口分别在`main/mrcoordinator.go`和`main/mrworker.go`，具体的实现在`mr/coordinator.go`，`mr/worker.go`和`mr/rpc.go`

### worker reqs a task and coordinator reponses with a task file name

第一步的目标就是worker通过RPC请求一个任务，coordinator响应请求，返回需要执行的文件名，worker获取到该文件。

因此，首先需要弄清楚worker和coordinator是如何通过RPC交互的。`mr/worker.go`中，`Worker()`调用`CallExample()`发起RPC请求，通过`call()`调用`Coordinator`的`Example()`方法并传入`&args`和`&reply`。`mr/coordinator.go`中，`Example()`方法作为RPC handler。

可以先运行coordinator，再运行worker，就会发现work能够打印出coordinator返回的值，至此也完成了两者间的交互。









### 



