# 36.临时表


## 临时表

创建语句`reate temporary table temp_t(id int primary key)engine=innodb;`

由session创建，且只能被创建的session访问到，可以和普通表同名（优先访问临时表），当session结束后，就释放掉了。

会创建一个**ql{进程id}_{线程id}_序列号**的表，专门用于存放临时表

## 主从复制时对临时表的策略

statement格式下，临时表的操作需要被binlog忠实记录，否则无法正常进行。

row格式下，临时表的操作不需要被记录，因为row格式的binlog会存储具体数据，无临时表也可以。
