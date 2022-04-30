# 24.MySQL是怎么保证主备一致的


## MySQL主备架构

![一图流](https://qiao1994.github.io/images/MySQL/24.png)

* 在备库B上通过change master命令，设置主库A的IP、端口、用户名、密码，以及要从哪个位置开始请求binlog，这个位置包含文件名和日志偏移量。
* 在备库B上执行start slave命令，这时候备库会启动两个线程，就是图中的io_thread和sql_thread。其中io_thread负责与主库建立连接。
* 主库A校验完用户名、密码后，开始按照备库B传过来的位置，从本地读取binlog，发给B。
* 备库B拿到binlog后，写到本地文件，称为中转日志（relay log）。
* sql_thread读取中转日志，解析出日志里的命令，并执行。

## binlog的格式

### statement

只记录sql日志原文

某些情况下有主备不一致风险，如`delete from xx where a=1 and b=1 limit 1`，由于主备可能使用不同的索引，所以加上limit后，有可能删除不同的行。

### row

忠实记录每次操作前后的所有数据

问题是太过臃肿，如删除十万行数据，statement格式只需要一行语句，而row格式需要记录十万行数据。

优势是不会有主备不一致风险，且恢复数据时更容易。

### mixed

在没有主备不一致风险时，使用statement格式；有风险时，使用row格式；结合了两者的优势。

## binlog的附加内容

binlog会带上时间戳，来保证备库使用binlog复制数据时能顺序执行；

binlog会带上server_id，来避免双主时的循环复制问题


