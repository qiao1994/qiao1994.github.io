# 14.高性能数据库集群：读写分离

# 读写分离原理
主从架构，主机读写，从机读取，主机通过复制将数据同步到从机。

# 复制延迟的解决方案
* 写操作后的读操作发送给主机：耦合代码
* 读从机失败后再读一次主机：增加主机压力
* 关键业务读写主机，非关键业务读从机

# 分配机制
* 代码封装
* 引入中间件
