# Distributed System 研习笔记
### TF-IDF in spark and hadoop
term-frequency and inverted document frequency 
考虑到/词汇在篇章中出现的概率/和/词汇在文档库中出现概率的反比例/的乘积。意义是确定该主题词在篇章中的重要性，作为评判不同篇章相似度的指标。

#### 为何需要用到分布式系统呢？
传统的方法采用遍历式，单线程，效率低。利用spark平台或者Hadoop平台提供类似 ‘java stream’ 的map类型操作，可以一行代码搞定并行操作，提高效率。

具体来说对于一篇文章，统计文中单词universe的数量，方法则是生成’key-value’对 — (TERM\_i\_,1) （此为MAP操作），然后累加得到相同’TERM\_i\_’的总数（此为reduce操作），对文章长度度归一化后得到词频(term frequency)。DF亦是这个原理

### Page-rank with Spark
对于一个迭代的数值计算过程，page-rank算法将分布式的多源计算采用spark类型的平台并行运行。迭代过程中，spark的特性 in-memory caching of data助力迭代速率。成为一个业界实践典范。