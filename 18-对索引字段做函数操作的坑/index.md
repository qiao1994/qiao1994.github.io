# 18.对索引字段做函数操作的坑


当MySQL认为需要对索引字段做一点事后才能匹配时，索引会不幸的失去排序能力。

## 案例一：条件字段函数操作

`select count(*) from tradelog where month(t_modified)=7;`

这条sql语句会扫描全索引，因为对索引字段做函数操作，可能会破坏索引值的有序性，因此优化器就决定放弃走树搜索功能。

> where id+1=1000 会有这个问题，但是where id=1000-1不会有这个问题

## 案例二：隐式类型转换

`select * from tradelog where tradeid=110717;` (tradeid是varchar(32))

这样即使tradeid有索引，也需要走全表扫描；因为tradeid会被转化为int，这行sql会被转化为`select * from tradelog where  CAST(tradid AS signed int) = 110717;`，同样是对索引字段做函数操作的原因。

## 案例三：隐式字符编码转换

当两个表的字符集不一致时，如utf8和utf8mb4，跨表查询时会把utf8转为utf8mb4(小范围的转为大范围的)；这是如果条件是a[utf-8]=b[utf8mb4]，则同样会有"对索引字段做函数操作"的问题；但是如果是a[utf8mb4]=b[utf8]，就不会有这个问题(因为没有随索引字段做函数操作，而是对值做的操作)
