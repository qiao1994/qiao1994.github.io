# Redis抱佛脚


## 特点

性能、并发

## 问题

- 需要使用物理内存，不支持海量数据
- 缓存何数据库双写一致性问题
  - 要求强一致性的问题不用缓存
- 缓存穿透
  - 互斥锁
  - 异步更新
  - 拦截
- 缓存雪崩
  - 预热
  - 默认值
- 并发竞争
  - 互斥锁
  - 队列

## 为啥快？

- 纯内存
- 单线程无上下文切换
- I/O多路复用

## 数据类型

- String
- Hash
- List
- Set
- Sorted Set

## 过期策略

- 定期删除
- 惰性删除
- LRU

## 高并发解决思路

* 写内存，不写磁盘
* 异步处理
* 分布式处理

## 持久化方式

* AOF (追加写日志文件)
* 内存快照 (全量备份)
* VM

## 参考资料

* https://zhuanlan.zhihu.com/p/59168140
* https://zhuanlan.zhihu.com/p/57568995
