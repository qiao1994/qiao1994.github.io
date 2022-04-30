# 算法:快速排序

# 基本原理
快排的基本思想也是分治，它的基本思路是从数组中选择一个值作为分割点,小于这个分割点的值都放在分割点左侧;递归执行,直到分割点的左侧和右侧都只有一个元素,数组有序。

# 主要难点
我写这段代码的时候思路没从归并排序中跳出来踩了个坑：选择分割点的时候我沿着归并排序的思路就把分割点选为数组的中点,并且让分区函数返回这个中点,结果就是这个中点并不是分割好了的数组中的新位置,导致不能遍历完全,排序不完全。
其实快排要处理的是分区后的数组,真正有价值的是分区后分割点的位置尽量居中,所以分区前应该选择大小适中的分割点,选择中点完全没意义。

# 代码
```
<?php
    $arr = [8, 10, 2, 3, 6, 1, 5];
    main($arr);
    function main($arr) {
        quick_sort($arr, 0, count($arr) - 1);
    }


    /**
     * 快速排序
     * @param array $arr
     * @param integer $l
     * @param integer $h
     * @return void
     */
    function quick_sort(&$arr, $l, $h) {
        if ($l >= $h) {
            return ;
        }
        $m = partition($arr, $l, $h); //执行分区,拿到新的数组中分割点数的位置
        echo "{$l}~{$h} 处理完成; 新的分割点为{$m}" . PHP_EOL;
        quick_sort($arr, $l, $m - 1);
        quick_sort($arr, $m + 1, $h);
    }


    /**
     * 分区函数:取$arr[$h]的值为分割点,把大于它的放到它左侧,小于它的放到它右侧,返回这个分割点的新位置
     * @param array $arr
     * @param integer $l
     * @param integer $h
     * @return integer $privot
     */
    function partition(&$arr, $l, $h)  {
        echo "开始处理{$l}~{$h}" . PHP_EOL;
        //选取$h为分区点
        $left = [];
        $right = [];
        for ($i = $l; $i < $h; $i++) {
            if ($arr[$i] < $arr[$h]) {
                //左侧
                $left[] = $arr[$i];
            } else {
                //右侧
                $right[] = $arr[$i];
            }
        }
        //回灌到$arr
        $i = $l;
        for ($j = 0; $j < count($left); $j++) {
            $arr[$i] = $left[$j];
            $i++;
        }
        $arr[$i] = $arr[$h];
        $privot = $i;
        $i++;
        for ($j = 0; $j < count($right); $j++) {
            $arr[$i] = $right[$j];
            $i++;
        }
        echo "分割点的值:{$arr[$privot]}" . PHP_EOL;
        echo "left: " . json_encode($left) . PHP_EOL;
        echo "right: " . json_encode($right) . PHP_EOL;
        echo "arr: " . json_encode($arr) . PHP_EOL;
        //注意:要返回的是分割点的值在新数组中的下标
        return $privot;
    }
```
# 执行结果
```
开始处理0~6
分割点的值:5
left: [2,3,1]
right: [8,10,6]
arr: [2,3,1,5,8,10,6]
0~6 处理完成; 新的分割点为3
开始处理0~2
分割点的值:1
left: []
right: [2,3]
arr: [1,2,3,5,8,10,6]
0~2 处理完成; 新的分割点为0
开始处理1~2
分割点的值:3
left: [2]
right: []
arr: [1,2,3,5,8,10,6]
1~2 处理完成; 新的分割点为2
开始处理4~6
分割点的值:6
left: []
right: [8,10]
arr: [1,2,3,5,6,8,10]
4~6 处理完成; 新的分割点为4
开始处理5~6
分割点的值:10
left: [8]
right: []
arr: [1,2,3,5,6,8,10]
5~6 处理完成; 新的分割点为6
```


# 优化思路
用临时数组实现的分区函数不是原地排序的空间复杂度是O(n)
有一个很巧妙的办法能实现原地的分区函数
它的思路是用`$i`作为一个分区点,`$i`左侧的内容为已处理区间,右侧为未处理区间,每次都从未处理区间取出一个值,如果它小于分割点,则把它交换到已处理区间尾部,即`$arr[$i]`,这样遍历之后即完成了原地分区,`$i`位置就是分割点

```
    /** 
     * 原地分区函数:功能同partition,但不需要n级别的额外的空间
     * @param array $arr
     * @param integer $l
     * @param integer $h
     * @return integer $privot
     */
    function partitionAutochthonous(&$arr, $l, $h) {
        $pivot = $arr[$h];
        $i = $l; 
        for ($j = $l; $j < $h; $j++) {
            if ($arr[$j] < $pivot) {
                swap($arr, $i, $j);
                $i++;
            }   
        }   
        swap($arr, $i, $h);
        return $i; 
    }   

    /** 
     * 交换操作,把数组中下标为$i和$j的数据换位置
     * @param array $arr
     * @param integer $i
     * @param integer $j
     */
    function swap(&$arr, $i, $j) {
        $temp = $arr[$i];
        $arr[$i] = $arr[$j];
        $arr[$j] = $temp;
    }
```
# 性能分析
## 空间复杂度
如果使用原地的分区函数,那么快速排序就是一个原地排序,它的空间复杂度为O(1);如果不使用原地分区函数,那么它的空间复杂度为O(n)
## 时间复杂度
快速排序的时间复杂度取决于分区操作的次数,分区操作的时间复杂度为O(n);
* 如果能每次都恰好选择大小适中的分割点,那么分区次数就是完全二叉树的高度logn,那么快速排序的时间复杂度和归并排序相同,都是O(nlogn);
* 如果每次都选择最差的分割点,即所有数字都在分割点左侧,那就需要n次分区操作,基本退化为选择排序,此时的时间复杂度为O(n^2);
* 如果随机选取分割点,快速排序的平均时间复杂度为O(nlogn)

# 快速排序和归并排序的区别
## 思路:快速排序自顶向下;归并排序自底向上
快速排序是先分区,然后再处理子问题;
归并排序是先处理子问题,然后再合并
## 性能:快速排序空间占优;归并排序时间稳定
归并排序的时间复杂度很稳定,但是空间复杂度也稳定较高,因为合并操作无法原地完成;
快速排序虽然时间不够稳定,但是整体可接受,且空间复杂度很有优势,所以快排应用更加广泛
