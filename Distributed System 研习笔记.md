# Distributed System 研习笔记
### TF-IDF in spark and hadoop
term-frequency and inverted document frequency 
考虑到/词汇在篇章中出现的概率/和/词汇在文档库中出现概率的反比例/的乘积。意义是确定该主题词在篇章中的重要性，作为评判不同篇章相似度的指标。

#### 为何需要用到分布式系统呢？
传统的方法采用遍历式，单线程，效率低。利用spark平台或者Hadoop平台提供类似 `java stream` 的map类型操作，可以一行代码搞定并行操作，提高效率。

具体来说对于一篇文章，统计文中单词universe的数量，方法则是生成’key-value’对 — (TERM\_i\_,1) （此为MAP操作），然后累加得到相同’TERM\_i\_’的总数（此为reduce操作），对文章长度度归一化后得到词频(term frequency)。DF亦是这个原理

### Page-rank with Spark
对于一个迭代的数值计算过程，page-rank算法将分布式的多源计算采用spark类型的平台并行运行。迭代过程中，spark的特性 `in-memory caching of data`助力迭代速率。成为一个业界实践典范。

### 数据传输—分布式系统工作的基础之一
不同hosts之上的JVM之间通讯依赖 `socket` ,主要在客户端与服务器端实现了`getInputStream()` 和 `getOutputStream()`两种方法

Serialization 是传送大体积对象所用的几类办法实现的几类方式
1. 自定义序列化方法。缺点：当多层引用的对象目标时，会导致复杂的问题。
2. XML 为标准的`data interchange standard` 缺点是：许多`metadata overhead`会附加在数据上，作为格式辅助。空间开销变大。
3. 实现`𝚂𝚎𝚛𝚒𝚊𝚕𝚒𝚣𝚊𝚋𝚕𝚎 Interface` 克服了以上两种缺点，克服了”infinite reference loop”的缺点。
4. Interface Definition Language (IDL) 支持多语言跨平台数据传输。缺点：需要额外的IDL build时间和`.proto`辅助文件的储存的空间消耗。 如果只需在JVM平台实现对象传输的话，不必采用该方法。

RMI (Remote Method Invocation) 是实现分布式对象方法调用的java工具。本质是一层封装了socket的平台，并映射了global variable和local variable。对象需注册并设置代理人。

Multicast  -\> Publish and Subscribe (high level Frame API)

### Pthread 线程使用基本方法指南
当发生以下情形之一时，线程就会结束：

* 线程运行的函数return了，也就是线程的任务已经完成；
* 线程调用了 pthread_exit 函数；
* 其他线程调用 pthread_cancel 结束这个线程；
* 进程调用 exec() or exit()，结束了；
* main() 函数先结束了，而且 main() 自己没有调用 pthread_exit 来等所有线程完成任务.
## Spark related notes
### 如何区分转化操作与行动操作
1. 如果对于一个特定的函数是属于转化操作还是行动操作感到困惑，你可以看看它的返回值类型：转化操作返回的是 RDD，而行动操作返回的是其他的数据类型。
2. 转化操作的惰性理解。
