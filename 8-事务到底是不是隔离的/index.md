# 8.事务到底是不是隔离的


## 两种"视图"

* view 用查询语句定义的虚拟表，在调用的时候执行查询语句并生成结果`create view ... `
* InnoDB在实现MVCC时用到的一致性读视图，即consistent read view，用于支持RC（Read Committed，读提交）和RR（Repeatable Read，可重复读）隔离级别的实现。

## “快照”在MVCC里是怎么工作的?

* 每个事务都有一个唯一的trx_id，由innodb引擎分配，递增
* 数据表中的一行记录，可能有多个版本(row)，每个版本有自己的row trx_id
* 快照通过当前数据+undolog来恢复到事务启动时的状态来获取的

这就是一致性读

## 更新逻辑

更新数据都是先读后写的，而这个读，只能读当前的值，称为“当前读”，否则就可能会丢失其他事务的修改

![当前读](https://qiao1994.github.io/images/MySQL/current-reading.png)

## 读提交和可重复读的区别

- 在可重复读隔离级别下，只需要在事务开始的时候创建一致性视图，之后事务里的其他查询都共用这个一致性视图，查询只承认在事务启动前就已经提交完成的数据；
- 在读提交隔离级别下，每一个语句执行前都会重新算出一个新的视图，查询只承认在语句启动前就已经提交完成的数据。

> 而当前读，总是读取已经提交完成的最新版本
