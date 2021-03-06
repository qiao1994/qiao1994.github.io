# 2.日志系统


## redo log innodb的“语音便签”

我早上上班路上一边开车一边听专栏，听到需要记录的东西会在等红灯的时候用语音便签记录下，晚上睡觉前才会整理成博客。这个过程中，边开车边整理博客我是做不到的，所以我先用语音便签做了一个简单记录，等我空闲的时候才会把语音便签整理到博客上。

MySQL也一样，如果每次更新、删除操作都去直接更新磁盘文件，那能容忍的并发恐怕极其有限了。在innodb中采用了redo log的方式先把操作记录下来，等空闲的时候再把redo log批量更新到磁盘中。

redo log的文件数量和大小可以配置，当达到配置的值时，会触发批量写入磁盘操作，清空redo log。

###  名词

* WAL技术 Write-Ahead Logging 先写日志，再写磁盘
* crash-safe 有了redo log，可以保证即使服务突然异常，也不会丢失数据

## binlog Server层的“语音便签”

上面的redo log是引擎层的日志，server层也有自己的日志，称为binlog（归档日志）

### 问题

* 为什么有两份日志？
  * binlog只有归档能力，没有crash-safe能力；innodb为了crash-safe能力引入了redo log
* binlog和redo log有啥不同？
  * redo log是innodb特有的，binlog是MySQL的server层的日志，所有引擎都可以使用
  * redo log是物理日志，记录的是“在某个数据页上做了什么修改”；binlog是逻辑日志，记录的是这个语句的原始逻辑，比如“给ID=2这一行的c字段加1 ”
  * redo log是循环写的，空间固定会用完；binlog是可以追加写入的。“追加写”是指binlog文件写到一定大小后会切换到下一个，并不会覆盖以前的日志。

## 两阶段提交

### 一次更新操作的流程

* 执行器找引擎取到要修改的记录
* 引擎查看记录是否在内存中，如果在内存中，直接返回给执行器；否则需要从磁盘读入内存，然后再返回
* 执行器拿到数据后计算新的数值，再调用引擎写入这行新数据
* 引擎将这行数据更新到内存中，同时写入redo log(prepare状态)；然后告知执行器执行完了，随时可以提交事务
* 执行器把这个操作的binlog写入磁盘中
* 执行器通知引擎，引擎把刚刚写入的redo log状态更新为commit
![两阶段提交](https://qiao1994.github.io/images/MySQL/two-stage-commit.png)

###  为什么需要两阶段提交？

如果MySQL服务异常宕机，我们需要使用定时镜像+binlog来恢复数据。

如果没有两阶段提交，要么先写binlog，要么先写redo log，考虑只写了一个日志的情况，即写完一个日志就宕机的情况。

* **先写redo log后写binlog**
  * redo log恢复后的数据是更新后的
  * binlog恢复的数据是更新前的
* **先写binlog后写redo log**
  * binlog恢复的数据是更新后的
  * redo log恢复的数据是更新前的

如果没有两阶段提交，上述情况下就出现了引擎状态(库的实际状态)和用binlog恢复出来的库状态不一致的问题；不只有数据库，两阶段提交也是跨系统维持数据逻辑一致性时常用的一个方案。


