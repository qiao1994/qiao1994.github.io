# 1.CPU性能分析

## 性能指标

* CPU使用率：非空闲时间占总 CPU 时间的百分比
  * 用户CPU使用率
  * 系统CPU使用率
  * 等待I/O的CPU使用率
  * 软/硬中断CPU使用率
  * 虚拟机CPU使用率
  * 运行客户CPU使用率
* 平均负载：系统的平均活跃进程数
* 上下文切换
  * 无法获取资源而导致的自愿上下文切换
  * 被系统强制调度导致的非自愿上下文切换
* CPU缓存命中率

## 工具字典

![根据指标找工具](https://qiao1994.github.io/images/Linux/cpu-1.png)

![根据工具查指标](https://qiao1994.github.io/images/Linux/cpu-2.png)



## 分析思路

![分析思路](https://qiao1994.github.io/images/Linux/cpu-3.png)
