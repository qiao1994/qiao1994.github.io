# 17.随机获取记录的正确姿势


## 内存临时表

这一行sql语句的执行逻辑 `select word from words order by rand() limit 3;` 假定表内现在有10000行数据

* 创建一个临时表。这个临时表使用的是memory引擎，表里有两个字段，第一个字段是double类型，为了后面描述方便，记为字段R，第二个字段是varchar(64)类型，记为字段W。并且，这个表没有建索引。
* 从words表中，按主键顺序取出所有的word值。对于每一个word值，调用rand()函数生成一个大于0小于1的随机小数，并把这个随机小数和word分别存入临时表的R和W字段中，到此，扫描行数是10000。
* 现在临时表有10000行数据了，接下来你要在这个没有索引的内存临时表上，按照字段R排序。
* 初始化 sort_buffer。sort_buffer中有两个字段，一个是double类型，另一个是整型。
* 从内存临时表中一行一行地取出R值和位置信息，分别存入sort_buffer中的两个字段里。这个过程要对内存临时表做全表扫描，此时扫描行数增加10000，变成了20000。
* 在sort_buffer中根据R的值进行排序。注意，这个过程没有涉及到表操作，所以不会增加扫描行数。
* 排序完成后，取出前三个结果的位置信息，依次到内存临时表中取出word值，返回给客户端。这个过程中，访问了表的三行数据，总扫描行数变成了20003。

简单说就是先把所有记录生成一个随机数值放到内存中，再把这些记录按照随机值排序，最后取前三条记录。

## 磁盘临时表

当需要占用的内存过大，就会使用磁盘临时表，同时使用优先队列排序法，只得到三行（如果是要获取很多行，会使用外部排序，即归并排序），不会把所有记录都排序。执行流程如下：

* 对于这10000个准备排序的(R,rowid)，先取前三行，构造成一个堆；
* 取下一个行(R’,rowid’)，跟当前堆里面最大的R比较，如果R’小于R，把这个(R,rowid)从堆中去掉，换成(R’,rowid’)；
* 重复第2步，直到第10000个(R’,rowid’)完成比较。

## 随机排序方法

### 方法1

* 取得这个表的主键id的最大值M和最小值N;
* 用随机函数生成一个最大值到最小值之间的数 X = (M-N)*rand() + N;
* 取不小于X的第一个ID的行。

这种方法得到的结果并不是真正的随机结果，因为主键id可能有空洞。

### 方法2

* 取得整个表的行数，并记为C。
* 取得 Y = floor(C * rand())。 floor函数在这里的作用，就是取整数部分。
* 再用limit Y,1 取得一行。

#### 获取三个随机值

* 取得整个表的行数，记为C；
* 根据相同的随机方法得到Y1、Y2、Y3；
* 再执行三个limit Y, 1语句得到三行数据。


