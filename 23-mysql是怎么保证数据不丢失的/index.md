# 23.MySQL是怎么保证数据不丢失的


时序上redo log先prepare， 再写binlog，最后再把redo log commit

## binlog的写入机制

事务执行过程中，先把日志写到binlog cache，事务提交的时候，再把binlog cache写到binlog文件中，一个事务的binlog是不能被拆开的，因此不论这个事务多大，也要确保一次性写入。

系统给binlog cache分配了一片内存，每个线程一个，参数 binlog_cache_size用于控制单个线程内binlog cache所占内存的大小。如果超过了这个参数规定的大小，就要暂存到磁盘。

![一图流](https://qiao1994.github.io/images/MySQL/23.png)

- 图中的write，指的就是指把日志写入到文件系统的page cache，并没有把数据持久化到磁盘，所以速度比较快。
- 图中的fsync，才是将数据持久化到磁盘的操作。一般情况下，我们认为fsync才占磁盘的IOPS。

sync_binlog可以控制fsync的时机，可选的情况有：每次事务都fsync，每次事务都不fsync，N个事务执行一次fsync。

## redo log的写入机制

![一图流](https://qiao1994.github.io/images/MySQL/23-2.png)

redo log的写入机制和binlog的写入机制类似，都是buffer->write->fsync，区别是redo log的buffer阶段是所有线程共享一段内存(因为redo log不需要保证时序)

innodb后台有一个专门负责写入的线程，定时把执行buffer->write->fsync。

## io瓶颈的解决方案

* 设置 binlog_group_commit_sync_delay 和 binlog_group_commit_sync_no_delay_count参数，减少binlog的写盘次数。这个方法是基于“额外的故意等待”来实现的，因此可能会增加语句的响应时间，但没有丢失数据的风险。
* 将sync_binlog 设置为大于1的值（比较常见是100~1000）。这样做的风险是，主机掉电时会丢binlog日志。
* 将innodb_flush_log_at_trx_commit设置为2。这样做的风险是，主机掉电的时候会丢数据。
