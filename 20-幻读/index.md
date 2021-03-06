# 20.幻读


## 幻读是什么？

幻读指的是一个事务在前后两次查询同一个范围的时候，后一次查询看到了前一次查询没有看到的行。

> 在可重复读隔离级别下，普通的查询是快照读，是不会看到别的事务插入的数据的。因此，幻读在“当前读”下才会出现。

## 幻读有什么问题？

### 语义上

读到异常数据的会话，它的加锁目的没有实现，中途被其他会话插入了数据。

### 数据一致性

binlog是在事务提交的时候写入的，当多个事务会话同时执行时，某种场景下会出现数据和binlog不一致的问题。

## 如何解决幻读？

使用间隙锁能解决幻读的问题

但是这样做会影响并发度，如果业务允许，可以降低隔离级别为读提交，但是会引入数据和binlog不一致的问题，需要把binlog设置为row。
