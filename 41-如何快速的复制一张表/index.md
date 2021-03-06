# 41.如何快速的复制一张表


## 复制一张表的方式

* 物理拷贝的方式速度最快，尤其对于大表拷贝来说是最快的方法。如果出现误删表的情况，用备份恢复出误删之前的临时库，然后再把临时库中的表拷贝到生产库上，是恢复数据最快的方法。但是，这种方法的使用也有一定的局限性：
  - 必须是全表拷贝，不能只拷贝部分数据；
  - 需要到服务器上拷贝数据，在用户无法登录数据库主机的场景下无法使用；
  - 由于是通过拷贝物理文件实现的，源表和目标表都是使用InnoDB引擎时才能使用。
* 用mysqldump生成包含INSERT语句文件的方法，可以在where参数增加过滤条件，来实现只导出部分数据。这个方式的不足之一是，不能使用join这种比较复杂的where条件写法。
* 用select … into outfile的方法是最灵活的，支持所有的SQL写法。但，这个方法的缺点之一就是，每次只能导出一张表的数据，而且表结构也需要另外的语句单独备份。

后两种方式都是逻辑备份方式，是可以跨引擎使用的。
