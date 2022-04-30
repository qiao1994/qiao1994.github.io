# MySQL优化

# mysql系统

* 定位慢查询
  * 慢查询日志
    * `show variables like '%slow%';` 可查看`log_slow_queries`和`slow_launch_time`
    * `show global status like 'slow%';` 可查看 `slow_launch_threads`和`slow_queries`
* 最大连接数
  * `show variables like 'max_connections';` 最大连接数配置
  * ` show global status like 'max_used_connections';`  最好max_used_connections/max_connections≈85%
* 使用`explain`查看执行计划
  * type    all/range/const
  * key 用到的索引，如果为null，可使用强制索引 `force index`
  * key_len 索引长度
  * rows 预估扫描长度
  * extra 不友好情况 `Using filesort, Using temporary`
* 引擎选择：<https://www.qiaomaoshuang.com/2019/04/18/MySQL%E7%9A%84%E5%BC%95%E6%93%8E%E3%80%81%E4%BA%8B%E5%8A%A1%E5%92%8C%E9%94%81/>
* 锁的选择：<https://www.qiaomaoshuang.com/2019/04/18/MySQL%E7%9A%84%E5%BC%95%E6%93%8E%E3%80%81%E4%BA%8B%E5%8A%A1%E5%92%8C%E9%94%81/>

# 建表

* 符合3NF
  * 1NF：表的列具有原子性，即列的信息不能分解
  * 2NF：表中的记录是唯一的，通常通过主键来实现
  * 3NF：无冗余信息
* 选择最合适的字段属性，尽量用小类型
  - varchar/char 变长/定长
  - not null 避免比较null
  - enum会被当作整型处理，推荐
* 图片等大内容不要用db存储

# SQL语句

* 大sql尽量拆分，利于多核cpu并发执行，容易命中缓存，不容易长时间锁表
* 事务尽量小
* SELECT
  * 不建议使用`select * ` ，要指定列名
* WHERE
  * 避免在where中进行null值判断，因为这样会放弃索引进行全表扫描
  * 不建议使用%模糊查询，这样会导致索引失效，进行全表扫描；如果必须，就建立全文索引
  * 建议在相同类型的字段上进行比较，避免隐式类型转换
  * 避免在where中对字段进行表达式操作 => 避免整个sql中出现运算，让db功能单一化
* LIMIT
  * 当只需要一条数据的时候，使用limit 1；这样explain的type会达到const级别
  * 分页的limit可以用上一页的最大id替代
  * 查询大量数据时，可以使用程序进行分段查询
* UNION
  * 如果两个结果没有重复数据，尽量用union all，因为union之后会做一次唯一性过滤操作
  * 使用union来代替手动创建临时表
* IN/EXISTS
  * 关于IN
    * 尽量用`between`替换
    * in和join相比，join更有优势一些，因为in需要创建临时表，但是要注意使用小表驱动大表
    * in和exists，遵循小表驱动的原则选择使用
      * in：子查询是驱动表
      * exists：外层表是驱动表
  * 少使用not in；not exists可以用join代替
* group by
  * 默认分组后会排序，增加order by null即可避免排序
* 锁定表代替事务，事务的独占性会锁住表 TODO

# 索引

* 不建在建有索引的字段使用函数操作
* 如果要排序字段没有索引，建议减少对该字段的排序
* or两侧的字段都有索引，才会走索引；可以用union来替代or
* 对于联合索引来说，要遵守最左前缀法则；同时如果存在范围查询(between/>/<)等条件时，会造成后面的索引失效
* 在大量用于join、where和order  by的字段建立索引
* 不要在enum等大量重复内容的字段上建立索引，会拖慢速度（估计是因为字典树退化成链表导致的）

