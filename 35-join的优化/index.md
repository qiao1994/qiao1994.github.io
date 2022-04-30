# 35.join的优化


## Multi-Range Read优化 MRR

拿到一批主键后再去查询(回表或者join)，可以利用顺序访问速度较快的特性，提升查询速度。

## Batched Key Access BKA

这个算法利用MRR，可以对Index Nested-Loop Join做优化

## BNL的优化

在被驱动表上建立索引，转为INLJ，再利用BKA优化；

如果原被驱动表不能加索引，就建立临时表，在临时表上建立索引，把原表数据插入后再执行join


