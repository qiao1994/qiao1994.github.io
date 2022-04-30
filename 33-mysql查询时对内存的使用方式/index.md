# 33.MySQL查询时对内存的使用方式


## 全表扫描对server层的影响

**MySQL是“边读边发的”**

![一图流](https://qiao1994.github.io/images/MySQL/33.png)

## 全表扫描对innodb的影响

innodb的Buffer Pool中是使用改进过的LRU算法进行淘汰的，单次遍历全表不会对内存命中率有很大影响


