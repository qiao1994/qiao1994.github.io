# 11.怎么给字符串加索引


* 直接创建完整索引，这样可能比较占用空间；

* 创建前缀索引，节省空间，但会增加查询扫描次数，并且不能使用覆盖索引；

  * 前缀索引会损失区分度，所以需要确定好能容忍的损失，根据统计结果选择合适的前缀长度

  > mysql> select 
  > count(distinct left(email,4)）as L4,
  > count(distinct left(email,5)）as L5,
  > count(distinct left(email,6)）as L6,
  > count(distinct left(email,7)）as L7,
  > from SUser;

* 倒序存储，再创建前缀索引，用于绕过字符串本身**前缀的区分度不够**的问题；
* 创建hash字段索引，查询性能稳定，有额外的存储和计算消耗，跟第三种方式一样，都不支持范围扫描。


