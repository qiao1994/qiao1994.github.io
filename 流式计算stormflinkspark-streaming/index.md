# 流式计算：Storm、Flink、Spark Streaming


# Storm

有storm之前，如果要做流式计算，需要按照如下方式：

![img](https://static001.geekbang.org/resource/image/19/31/199c65da1a9dfae48f42c32f6a82c831.png)

有了 Storm 后，开发者无需再关注数据的流转、消息的处理和消费，只要编程开发好数据处理的逻辑 bolt 和数据源的逻辑 spout，以及它们之间的拓扑逻辑关系 toplogy，提交到 Storm 上运行就可以了。

![img](https://static001.geekbang.org/resource/image/78/5b/780899b3fda0ea39acbdfb9545fbc55b.png)

Storm架构（主从）：

![img](https://static001.geekbang.org/resource/image/d3/8a/d33aa8765ad381824fd9818f93074a8a.png)

# Spark Streaming

Spark Streaming 巧妙地利用了 Spark 的分片和快速计算的特性，将实时传输进来的数据按照时间进行分段，把一段时间传输进来的数据合并在一起，当作一批数据，再去交给 Spark 去处理。下图这张图描述了 Spark Streaming 将数据分段、分批的过程。

如果时间段分得足够小，每一段的数据量就会比较小，再加上 Spark 引擎的处理速度又足够快，这样看起来好像数据是被实时处理的一样，这就是 Spark Streaming 实时流计算的奥妙。

![img](https://static001.geekbang.org/resource/image/fb/c3/fb535e9dc1813dbacfa03c7cb65d17c3.png)

# Flink

Spark Streaming 是将实时数据流按时间分段后，当作小的批处理数据去计算。那么 Flink 则相反，一开始就是按照流处理计算去设计的

Flink 处理实时数据流的方式跟 Spark Streaming 也很相似，也是将流数据分段后，一小批一小批地处理。

（这里感觉不太透彻，也没有对比三者的优劣，作为科普）

![img](https://static001.geekbang.org/resource/image/92/9f/92584744442b15d541a355eb7997029f.png)
