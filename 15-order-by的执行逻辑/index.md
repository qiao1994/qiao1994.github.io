# 15.order by的执行逻辑


## 全字段排序

分析这个sql语句的执行逻辑

`select city,name,age from t where city='杭州' order by name limit 1000;`

* 初始化sort_buffer(一段专门用于排序的内存)，确定放入name、city、age这三个字段；
* 从索引city找到第一个满足city='杭州’条件的主键id，也就是图中的ID_X；
* 到主键id索引取出整行，取name、city、age三个字段的值，存入sort_buffer中；
* 从索引city取下一个记录的主键id；
* 重复步骤3、4直到city的值不满足查询条件为止，对应的主键id也就是图中的ID_Y；
* 对sort_buffer中的数据按照字段name做快速排序(如果内存满足要求，则在内存中完成，否则使用外部排序[多个文件做归并])；
* 按照排序结果取前1000行返回给客户端。

## rowid排序

如果MySQL任务要排序的行太长(超过配置`max_length_for_sort_data`的长度)，就会采用rowid排序

* 初始化sort_buffer，确定放入两个字段，即name和id；
* 从索引city找到第一个满足city='杭州’条件的主键id，也就是图中的ID_X；
* 到主键id索引取出整行，取name、id这两个字段，存入sort_buffer中；
* 从索引city取下一个记录的主键id；
* 重复步骤3、4直到不满足city='杭州’条件为止，也就是图中的ID_Y；
* 对sort_buffer中的数据按照字段name进行排序；
* **遍历排序结果，取前1000行，并按照id的值回到原表中取出city、name和age三个字段返回给客户端。**

## 两者差异

rowid排序是在内存实在不够的情况下才会选择的算法，因为它需要回表去取数据，增加扫描行数。

## 如何才能不需要排序？

给要where和order by的字段增加索引，如本语句中，增加`city,name`索引，则执行流程会简化为

* 从索引(city,name)找到第一个满足city='杭州’条件的主键id；
* 到主键id索引取出整行，取name、city、age三个字段的值，作为结果集的一部分直接返回；
* 从索引(city,name)取下一个记录主键id；**索引是有序的，所以不需要排序，依次取满足条件的行即可**
* 重复步骤2、3，直到查到第1000条记录，或者是不满足city='杭州’条件时循环结束。

## 如何能既不排序，又不回表？

利用索引覆盖，增加`city,name,age`索引

* 从索引(city,name,age)找到第一个满足city='杭州’条件的记录，取出其中的city、name和age这三个字段的值，作为结果集的一部分直接返回；
* 从索引(city,name,age)取下一个记录，同样取出这三个字段的值，作为结果集的一部分直接返回；
* 重复执行步骤2，直到查到第1000条记录，或者是不满足city='杭州’条件时循环结束。
