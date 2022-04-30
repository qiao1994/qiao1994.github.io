# cached、buffers、swap是啥


## 它们在哪(物理上)，是干嘛的(逻辑上)？

按速度从快到慢依次列出物理硬件(一级列表)；二级列表为物理硬件上的逻辑分配

* **cpu**
* **cpu缓存**
* **内存**
  * cached
    * 读写文件的页缓存
    * Slab分配器中的可回收内存
  * buffers  读写原始磁盘块的缓存
* **磁盘**
  * swap 当内存条不够时，从磁盘上拿出一块空间放一些接近过期的内存数据
  * disk 持久化存储数据

## 如何查看剩余内存
![示意图](https://qiao1994.github.io/images/Linux/memory.png)
Mem, total ：物理上的内存总量

Mem, used ：系统已经分配给buffers+cached的内存总量，只是已分配的，并不是已经使用了的

Mem, free ：系统没有分配的物理内存，即啥都没在用的，并不是“空闲的”

Mem, shared：由多个进程共享的内存

Mem, buffers：系统已经分配，但是没有使用的buffers

Mem, cached：系统已分配，但是没有使用的cached

buffers/cache, total：系统已分配，并且已使用的buffers+cached，**真正的已用内存**

buffers/cache, free：系统已分配，但没有使用的buffers+cached，和系统没有分配的物理内容存，**真正的空闲内存**

Swap, totel：系统分配的swap总量

Swap, used：已用的swap量

Swap, free：空闲的swap量




>不小心暴露了我的开发机有188G内存的本质，hhh...
