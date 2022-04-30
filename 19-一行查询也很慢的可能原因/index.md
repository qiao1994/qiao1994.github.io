# 19.一行查询也很慢的可能原因


## 第一类：查询长时间不返回

`select * from t where id=1;`

### 等MDL锁

有一个线程正在表t上请求或者持有MDL写锁，把select语句堵住了

### 等flush

有一个线程正要对表t做flush操作，把select语句堵住了

### 等行锁

`select * from t where id=1 lock in share mode; `

由于访问id=1这个记录时要加读锁，如果这时候已经有一个事务在这行记录上持有一个写锁，我们的select语句就会被堵住。

## 第二类：查询慢

下图这种情况，会导致`select * from t where id=1;`执行慢，因为MySQL需要执行undo log，恢复到原始的版本。

![一图流](https://qiao1994.github.io/images/MySQL/19.png)




