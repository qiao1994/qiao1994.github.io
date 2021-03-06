# 28.读写分离的坑


在从库上会读到系统的一个过期状态，称为过期读。

## 过期读的解决方案

### 强制走主库方案

必须拿到最新结果的方案，强制请求主库；非必须最新结果的方案，请求从库。

### sleep方案

主库更新后，从库查询之前，先sleep一下。

不靠谱

### 判断主备无延迟方案

* 查询前先判断主从延迟是否为0，如果不是，一直等。
* 对比位点确保主备无延迟
* 对比GTID确保主备无延迟

### 配合semi-sync方案

引入半同步复制

1. 事务提交的时候，主库把binlog发给从库；
2. 从库收到binlog以后，发回给主库一个ack，表示收到了；
3. 主库收到这个ack以后，才能给客户端返回“事务完成”的确认。

#### 问题

1. 一主多从的时候，在某些从库执行查询请求会存在过期读的现象；
2. 在持续延迟的情况下，可能出现过度等待的问题。

### 等主库位点方案

master_pos_wait

等待，直到实例中的binlog位点执行到了指定位置。

### 等GTID方案

wait_for_executed_gtid_set

等待，直到实例中执行了指定GTID。
