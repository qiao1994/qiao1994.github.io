# Hadoop


Hadoop 主要是由三部分组成，分布式文件系统 HDFS、分布式计算框架 MapReduce与分布式集群资源调度框架 Yarn

## RAID与HDFS：单机/分布式文件系统

如果一个文件的大小超过了一张磁盘的大小，该如何存储？

单机时代，主要的解决方案是 RAID；分布式时代，主要解决方案是分布式文件系统。

### RAID

RAID（独立磁盘冗余阵列）技术是将多块普通磁盘组成一个阵列，共同对外提供服务。主要是为了改善磁盘的存储容量、读写速度，增强磁盘的可用性和容错能力。

存储方式：

![img](https://static001.geekbang.org/resource/image/54/af/54e170b7438fe3b8f8196dbfbc943baf.jpg)

对比表格：

![img](https://static001.geekbang.org/resource/image/e2/2f/e2fb7ec97e6127c1b03e83daeff0232f.jpg)

### HDFS

![img](https://static001.geekbang.org/resource/image/65/d7/65efd126cbcf3930a706f64c6e6457d7.jpg)

* 文件数据以数据块的方式进行切分，数据块可以存储在集群任意 DataNode 服务器上，所以 HDFS 存储的文件可以非常大，一个文件理论上可以占据整个 HDFS 服务器集群上的所有磁盘，实现了大容量存储。
* HDFS 一般的访问模式是通过 MapReduce 程序在计算时读取，MapReduce 对输入数据进行分片读取，通常一个分片就是一个数据块，每个数据块分配一个计算进程，这样就可以同时启动很多进程对一个 HDFS 文件的多个数据块进行并发访问，从而实现数据的高速访问。
* DataNode 存储的数据块会进行复制，使每个数据块在集群里有多个备份，保证了数据的可靠性，并通过一系列的故障容错手段实现 HDFS 系统中主要组件的高可用，进而保证数据和整个系统的高可用。

## MapReduce：分布式计算框架
### 基本情况

MapReduce 既是一个编程模型，又是一个计算框架。也就是说，开发人员必须基于 MapReduce 编程模型进行编程开发，然后将程序通过 MapReduce 计算框架分发到 Hadoop 集群中运行。

一个栗子：WordCount程序，WordCount 主要解决的是文本处理中词频统计的问题，就是统计文本中每一个单词出现的次数。

![img](https://static001.geekbang.org/resource/image/55/ba/5571ed29c5c2254520052adceadf9cba.png)



### MapReduce 作业启动和运行机制

MapReduce 运行过程涉及三类关键进程。

1. 大数据应用进程。这类进程是启动 MapReduce 程序的主入口，主要是指定 Map 和 Reduce 类、输入输出文件路径等，并提交作业给 Hadoop 集群，也就是下面提到的 JobTracker 进程。这是由用户启动的 MapReduce 程序进程，比如我们上期提到的 WordCount 程序。
2. JobTracker 进程。这类进程根据要处理的输入数据量，命令下面提到的 TaskTracker 进程启动相应数量的 Map 和 Reduce 进程任务，并管理整个作业生命周期的任务调度和监控。这是 Hadoop 集群的常驻进程，需要注意的是，JobTracker 进程在整个 Hadoop 集群全局唯一。
3. TaskTracker 进程。这个进程负责启动和管理 Map 进程以及 Reduce 进程。因为需要每个数据块都有对应的 map 函数，TaskTracker 进程通常和 HDFS 的 DataNode 进程启动在同一个服务器。也就是说，Hadoop 集群中绝大多数服务器同时运行 DataNode 进程和 TaskTracker 进程。

具体流程：



![img](https://static001.geekbang.org/resource/image/2d/27/2df4e1976fd8a6ac4a46047d85261027.png)

### MapReduce 数据合并与连接机制

每个 Map 任务的计算结果都会写入到本地文件系统，等 Map 任务快要计算完成的时候，MapReduce 计算框架会启动 shuffle 过程，在 Map 任务进程调用一个 Partitioner 接口，对 Map 产生的每个 进行 Reduce 分区选择，然后通过 HTTP 通信发送给对应的 Reduce 进程。这样不管 Map 位于哪个服务器节点，相同的 Key 一定会被发送给相同的 Reduce 进程。Reduce 任务进程对收到的 进行排序和合并，相同的 Key 放在一起，组成一个 传递给 Reduce 执行。

![img](https://static001.geekbang.org/resource/image/d6/c7/d64daa9a621c1d423d4a1c13054396c7.png)

## Yarn：分布式集群资源调度框架

在Hadoop1中，把 MapReduce 程序分发到大数据集群的服务器上这一过程是由TaskTracker 和 JobTracker 通信来完成，这种方式把服务器集群资源调度管理和 MapReduce 执行过程耦合在一起，如果想在当前集群中运行其他计算任务，比如 Spark 或者 Storm，就无法统一使用集群中的资源了。

Hadoop 2 最主要的变化，就是将 Yarn 从 MapReduce 中分离出来，成为一个独立的资源调度框架，其主要架构如下：

![img](https://static001.geekbang.org/resource/image/af/b1/af90905013e5869f598c163c09d718b1.jpg)

# 总结

HDFS、MapReduce、Yarn三者有一个很大的共同点，都是主从架构，主节点负责管理从节点，并对外呈现统一的逻辑情况、提供服务，从节点负责执行具体的存储与计算。

| 项        | 主节点(集群唯一) | 从节点(每台机器) |
| --------- | ---------------- | ---------------- |
| HDFS      | NameNode         | DataNode         |
| MapReduce | JobTracker       | TaskTracker      |
| Yarn      | ResourceManager  | NodeManager      |

参考资料：https://time.geekbang.org/column/intro/100020201?tab=catalog

---



# Hive：Hadoop 大数据仓库

Hive 能够直接处理输入的 SQL 语句（Hive 的 SQL 语法和数据库标准 SQL 略有不同），调用 MapReduce 计算框架完成数据分析操作。

Hive 内部预置了很多函数，Hive 的执行计划就是根据 SQL 语句生成这些函数的 DAG（有向无环图），然后封装进 MapReduce 的 map 和 reduce 函数中。


