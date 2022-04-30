# 3.I/O性能分析


## I/O栈
![I/O栈](https://qiao1994.github.io/images/Linux/io-1.png)


## 性能指标

* 文件系统I/O性能指标

  * 存储空间使用情况
    * 容量
    * 使用量
    * 剩余量
  * 索引节点使用情况
    * 容量
    * 使用量
    * 剩余量
  * 缓存使用情况
    * 页缓存
    * 目录项缓存
    * 索引节点缓存
    * 各文件系统的缓存
  * 文件I/O
    * IOPS(r/s、w/s)
    * 响应时间(延迟)
    * 吞吐量(B/s)

* 磁盘系统I/O性能指标

  * 使用率

    > 指磁盘忙处理 I/O 请求的百分比。

  * IOPS

  * 吞吐量

  * 响应时间

## 工具字典

![根据指标找工具](https://qiao1994.github.io/images/Linux/io-2.png)

![根据工具查指标](https://qiao1994.github.io/images/Linux/io-3.png)



## 分析思路

![分析思路](https://qiao1994.github.io/images/Linux/io-4.png)
