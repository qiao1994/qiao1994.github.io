# Hbase


HBase 作为 Google BigTable 的开源实现，完整地继承了 BigTable 的优良设计。架构上通过数据分片的设计配合 HDFS，实现了数据的分布式海量存储；数据结构上通过列族的设计，实现了数据表结构可以在运行期自定义；存储上通过 LSM 树的方式，使数据可以通过连续写磁盘的方式保存数据，极大地提高了数据写入性能。

# 可伸缩架构

HBase 是按 Key 的区域进行分片，这个分片也就是 HRegion。应用程序通过 HMaster 查找分片，得到 HRegion 所在的服务器 HRegionServer，然后和该服务器通信，得到需要访问的数据。

HRegionServer 是物理服务器，每个 HRegionServer 上可以启动多个 HRegion 实例。当一个 HRegion 中写入的数据太多，达到配置的阈值时，一个 HRegion 会分裂成两个 HRegion，并将 HRegion 在整个集群中进行迁移，以使 HRegionServer 的负载均衡。

HRegion 会把数据存储在若干个 HFile 格式的文件中，这些文件使用 HDFS 分布式文件系统存储，在整个集群内分布并高可用。当一个 HRegion 中数据量太多时，这个 HRegion 连同 HFile 会分裂成两个 HRegion，并根据集群中服务器负载进行迁移。如果集群中有新加入的服务器，也就是说有了新的 HRegionServer，由于其负载较低，也会把 HRegion 迁移过去并记录到 HMaster，从而实现 HBase 的线性伸缩。

![img](https://static001.geekbang.org/resource/image/9f/f7/9f4220274ef0a6bcf253e8d012a6d4f7.png)

![img](https://static001.geekbang.org/resource/image/9f/ab/9fd982205b06ecd43053202da2ae08ab.png)

# 可扩展数据模型

使用列族可以解决传统关系型数据库无法应对针对列的频繁需求变更的问题；在创建表的时候，只需要指定列族的名字，无需指定字段（Column）。那什么时候指定字段呢？可以在数据写入时再指定。通过这种方式，数据表可以包含数百万的字段，这样就可以随意扩展应用程序的数据结构了。并且这种数据库在查询时也很方便，可以通过指定任意字段名称和值进行查询。

![img](https://static001.geekbang.org/resource/image/74/6f/74b3aac940abae8a571cc94f2226656f.png)

# 高性能存储

为了提高数据写入速度，HBase 使用了一种叫作 LSM 树的数据结构进行数据存储。LSM 树的全名是 Log Structed Merge Tree，翻译过来就是 Log 结构合并树。数据写入的时候以 Log 方式连续写入，然后异步对磁盘上的多个 LSM 树进行合并。

![img](https://static001.geekbang.org/resource/image/5f/3b/5fbd17a9c0b9f1a10347a4473d00ad3b.jpg)



# 扩展阅读

思考：使用LSM树的Hbase是否crash-safe的？是，每次写内存前会先写WAL(Writing Ahead Log，即HLog)，类似关系数据库InnoDB的redo log，保证崩溃时可以恢复已经写入的数据(Crash-safe)。

* 逻辑存储模型：一个多维的[哈希](https://so.csdn.net/so/search?q=哈希&spm=1001.2101.3001.7020)表，行键(Rowkey)-列族(Column Family)-列(Column Qualifier)-时间戳(Timestamp) 为定位一个具体单元数据(CELL)的坐标
* 物理存储模型：非严格的列式存储，是面向列族
* 查询：最多只需要**三次跳转**就可以定位任意一个Region，每个Region中只需要查找指定列族对应的HFile按照行键即可快速查找**有序数据**。HBase中都是二进制存储偏移，便于快速定位，且最近查找的数据都是缓存在内存中，进一步加快了查找效率。
* 写入：按顺序写如下内容
  * Writing Ahead Log，即HLog，确保crash-safe
  * MemStore
  * HFile：不会直接写入原来的HFile，而是会写入新的小HFile中，这样避免了文件锁的问题，支持较高并发，实际在后台有线程会处理当前小HFile的合并，这也是利用LSM树合并非常快的优点。

参考：

* https://time.geekbang.org/column/article/70253
* https://blog.csdn.net/wenzhou1219/article/details/88919682


