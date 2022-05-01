# Spark


Spark是比MapReduce更快、更易用的大数据计算框架

# 比MapReduce更易用

Spark可以理解成是面向对象的大数据计算。

> MapReduce 针对输入数据，将计算过程分为两个阶段，一个 Map 阶段，一个 Reduce 阶段，可以理解成是面向过程的大数据计算。
>
> 而 Spark 则直接针对数据进行编程（Scala语言），将大规模数据集合抽象成一个 RDD 对象，然后在这个 RDD 上进行各种计算处理，得到一个新的 RDD，继续计算处理，直到得到最后的结果数据。所以 Spark 可以理解成是面向对象的大数据计算。

Spark 也有自己的生态体系，以 Spark 为基础，有支持 SQL 语句的 Spark SQL，有支持流计算的 Spark Streaming，有支持机器学习的 MLlib，还有支持图计算的 GraphX。

# 比MapReduce更快

* MapReduce 一个应用一次只运行一个 map 和一个 reduce（串行）
* Spark 可以根据应用的复杂程度，分割成更多的计算阶段（stage），这些计算阶段组成一个有向无环图 DAG，Spark 任务调度器可以根据 DAG 的依赖关系执行计算阶段。（根据依赖关系部分并行）
* 使用内存存储中间计算结果更高效
