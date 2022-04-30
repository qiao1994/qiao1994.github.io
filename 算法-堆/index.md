# 算法:堆

# 定义
* 堆是一个完全二叉树
* 堆中的每个节点的值都必须大于等于(或小于等于)其子树中每个节点的值

> 后续讨论默认为大顶堆

# 关键点
## 存储
完全二叉树用数组存储最合适，见[数据结构-二叉树/#存储方式](https://www.qiaomaoshuang.com/2018/12/18/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84-%E4%BA%8C%E5%8F%89%E6%A0%91/#%E5%AD%98%E5%82%A8%E6%96%B9%E5%BC%8F)

## 插入
* 往堆的最后一个叶节点后插入一个元素
* 从该节点自底向上**堆化** (即把不符合堆定义的树更新为符合堆定义的树)

## 删除堆顶元素
* 用最后一个叶节点替换堆顶元素
* 删除最后一个叶节点
* 从堆顶节点自顶向下堆化

### 为什么不直接把堆顶元素置空，然后执行堆化呢？
这样空节点有可能不落在最后一个叶节点，这样就不满足完全二叉树的要求了，所以直接把最后一个节点拎出来。

## 堆化
### 自底向上
当前节点和父节点做比较，如果当前节点比父节点大，则交换位置；递归到顶。

### 自顶向下
当前节点和左右子树做比较，如果左右子树中有比当前节点小的，则把两个子树中最大的那一个和当前节点交换位置；递归到底。

## 堆排序
堆排序分为两部分，首先是建堆，然后排序
### 建堆
#### 思路一：从前往后，自底向上
用往堆中逐项插入节点的形式建堆。
即最开始认为只有第一个元素是堆中的元素，插入第二个元素，自底向上堆化，依次类推，直至所有数据插入完成。
这个思路堆化的时间复杂度是`O(nlogn)`，即：需要插入n个元素，每次插入需要的堆化的logn次操作。

#### 思路二：从后往前，自顶向下
从最后一个元素开始，逐项自底向上堆化，最终建立完整堆。
看起来思路二更复杂一些，因为自顶向下堆化比自底向上要复杂；但是自顶向下有个特性，就是叶子节点无需堆化，这样我们只遍历所有非叶子节点即可。
这个思路堆化的时间复杂度看起来是`O(n/2*logn)`,但是不够精确,经过一系列复杂(我也不太会的)计算,最终的复杂度是`O(logn)`

### 排序
排序的思路类似插入，把堆顶元素(最大)和最后一个叶子节点(下标为n,是最大元素排序后应该在的位置)互换位置;然后把1~n-1堆化,递归后就得到了有序的数组。

### 为什么快排比堆排序快？
堆排序是稳定的`O(nlogn)`排序，看起来比快排还要优秀，但是实际上快排比堆排序要快。
* 堆排序数据访问的方式没有快速排序友好：快排是连续访问数组，堆排序主要是跳跃访问数据，对cpu缓存不友好
* 对于同样的数据，在排序过程中，堆排序的交换次数要多于快速排序；快速排序的交换次数等于逆序度，但是堆排序会打乱数据。


# 代码实现
```
<?php
    //run();

    function run() {
        $heap = new Heap([1, 2, 5, 10, 6, 90, 49]);
        $heap->sort();
        $heap->insert(66);
        $heap->deleteTop();
        var_dump($heap->data);
    }

    //数组方式存储树,预设为大顶堆
    class Heap {

        public $data = [];

        //建堆
        public function __construct($data) {
            //第一种:从顶开始,逐项从尾部插入,自底向上堆化O(nlogn)
            /*
            $this->data[] = null;
            foreach ($data as $item) {
                $this->insert($item);
            }
            */
            //第二种:从第一个不是叶节点的节点开始逐项往前,逐项自顶向下堆化 O(n)[因为叶子节点下面只有自己,所以不需要自顶向下堆化,省掉了所有叶子节点的堆化过程]
            //堆是完全二叉树,要找到叶子节点的起始位置,只需知道最后一个不是叶子节点的位置是最后一个叶节点的父节点(n/2),所以从1-n/2是非叶节点,n/2+1~n是叶节点
            $this->data = array_merge([null], $data);
            for ($i = floor(count($this->data)/2); $i >= 1; $i--) {
                $this->heapifyTopToBottom($i, count($this->data));
            }
        }

        /**
         * 往堆里插入一个数据:把数据插入到最后一个叶节点的位置,然后自底向上堆化
         * @param integer $value
         * @return void
         */
        public function insert($value) {
            //把要插入的数据放到最后一个叶子节点
            $this->data[] = $value;
            //自底向上堆化
            $i = count($this->data) - 1;
            while (floor($i/2) > 0 && $this->data[$i] > $this->data[floor($i/2)]) {
                $this->swap($i, floor($i/2));
                $i = floor($i/2);
            }
        }

        /**
         * 删除堆顶元素
         * @return void
         */
        public function deleteTop() {
            //把最后一个元素替换到堆顶
            $lastLeafPosition = count($this->data) - 1;
            $this->data[1] = $this->data[$lastLeafPosition];
            unset($this->data[$lastLeafPosition]);
            $this->heapifyTopToBottom(1, $lastLeafPosition);
        }


        /**
         * 堆排序:把堆顶元素(最大)和最后一个叶子节点(下标为n,是最大元素排序后应该在的位置)互换位置;然后把1~n-1堆化,递归后就得到了有序的数组
         * @return void
         */
        public function sort() {
            for ($i = (count($this->data) - 1); $i >= 1; $i--) {
                //交换$i和1
                $this->swap(1, $i);
                //堆化
                $this->heapifyTopToBottom(1, $i);
            }
        }


        /**
         * 自顶向下堆化
         * @param integer $i 起始点
         * @param integer $size 要堆化内容的长度
         */
        public function heapifyTopToBottom($i, $size) {
            while (true) {
                $maxPosition = $i;
                if (($i*2 < $size) && ($this->data[$i] < $this->data[$i*2])) {
                    //左子树大
                    $maxPosition = $i * 2;
                }

                if ((($i*2+1) < $size) && ($this->data[$maxPosition] < $this->data[$i*2+1])) {
                    //右子树更大
                    $maxPosition = $i * 2 + 1;
                }

                //echo "i=>{$i}; v[i]=>{$this->data[$i]}; v[i*2]=>{$this->data[$i*2]}; v[i*2+1]=>{$this->data[$i*2+1]}" . PHP_EOL;
                
                if ($maxPosition != $i) {
                    $this->swap($i, $maxPosition);
                    $i = $maxPosition;
                } else {
                    break;
                }
            }
        }

        /**
         * 交换$this->data中的两个数据
         * @param integer $i
         * @param integer $j
         * @return void
         */
        public function swap($i, $j) {
            $temp = $this->data[$i];
            $this->data[$i] = $this->data[$j];
            $this->data[$j] = $temp;
        }
    }
```

> 这篇博客是目前为止最耗时间的一篇，mark一下~
