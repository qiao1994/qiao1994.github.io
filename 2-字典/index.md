# 2.字典

* map是一个哈希表的特定实现，是引用类型
* map的键的类型是收到约束的，一定要是可判等的
  * 因为map会先求键的哈希，哈希可能存在冲突，对比完哈希值后还要对比一下原值，所以需要是可判等的
  * 所以map的键值类型不能是函数类型、字典类型和切片类型
  * 最合适的键值类型是宽度低的类型，因为哈希计算快
* panic的来源
  * 给一个仅声明但是未初始化的词典(nil)增加键-值对

