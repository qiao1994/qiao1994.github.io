# MySQL join操作备忘


join本质上是用驱动表的每个记录去一一匹配被驱动表（会包含重复记录），然后根据条件做优化。

* 关键点
  * 尽量把条件放到ON后，这里的条件可以用在初次查询中，WHERE中的条件只能用在最后的过滤中
  * 选择数量小的作为驱动表
  * 选择最高效的join算法
* 算法
  * Simple Nested-Loop Join：一一遍历，从驱动表中选择一条记录，然后循环匹配表的每一行记录，效率最低。
  * Index Nested-Loop Join：索引匹配，如果匹配表的条件上有索引，则直接通过索引匹配到对应的记录
  * Block Nested-Loop Join：Simple Nested-Loop Join的加缓存版，一次读多条记录到缓存内
