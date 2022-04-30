# 算法:二分查找

# 基本思路
在已经升序排列且无重复数据的数组中，每次查找都取数组的中点值和目标值对比大小，如果目标值偏大，则目标值在中点右侧；如果目标值偏小，则目标值在中点左侧。

# 代码
```
<?php
    $arr = [1, 2, 3, 5, 6, 8, 9, 10, 15, 20, 100];

    $result = binarySearch(3, $arr);
    var_dump($result);
    $result = binarySearchRecursion(100, $arr, 0, 10);
    var_dump($result);

    /**
     * 二分查找
     * @param integer $target
     * @param array $arr
     * @return integer 被查找值在数组中的位置
     */
    function binarySearch($target, $arr) {
        $l = 0;
        $h = count($arr) - 1;
        while($l <= $h) {
            $m = intval(floor(($l+$h)/2)); //因为floor返回的是float类型(float范围比int大，所以floor只能返回float)
            echo "low : {$l} ; high : {$h}; m : {$m}" . PHP_EOL;
            if ($arr[$m] == $target) {
                return $m;
            } else if ($target > $arr[$m]) {
                $l = $m + 1;
            } else {
                $h = $m - 1;
            }
        }
        return -1;
    }

    /**
     * 二分查找
     * @param integer $target
     * @param array $arr
     * @param integer $l
     * @param integer $h
     * @return integer 被查找值在数组中的位置
     */
    function binarySearchRecursion($target, $arr, $l, $h) {
        /*
        为啥不需要这段呢?
        假设最小位置是0，最大位置是10
        考虑如果要查找0位置的数字，使用floor取整数是可以的，但是如果要查找10位置的数字就找不到了？
        用ceil取整的话刚好相反，10位置的数字可以找到，但是0位置的找不到？
        解决问题的点在于递归调用时的$m+1和$m-1, 调用时会让$m前进或后退一步
        如果是10位置的数值,则此时l=10 h=10,避免出现l=9 h=10 m=9(floor) 的尴尬局面;0位置同理
        if ($target == $arr[$l]) {
            return $l;
        }
        if ($target == $arr[$h]) {
            return $h;
        }
        */
        if ($l > $h) {
            return -1;
        }
        $m = intval(floor(($l+$h)/2));
        echo "low : {$l} ; high : {$h}; m : {$m}" . PHP_EOL;
        if ($target == $arr[$m]) {
            return $m;
        }
        if ($target > $arr[$m]) {
            return binarySearchRecursion($target, $arr, $m + 1, $h);
        } else {
            return binarySearchRecursion($target, $arr, $l - 1, $m);
        }
    }
```

# 执行结果

```
low : 0 ; high : 10; m : 5
low : 0 ; high : 4; m : 2
int(2)
low : 0 ; high : 10; m : 5
low : 6 ; high : 10; m : 8
low : 9 ; high : 10; m : 9
low : 10 ; high : 10; m : 10
int(10)
```

# 适用场景
二分查找的时间复杂度为O(logn)，效率非常高，所以你懂得，一定有它的局限性。
* 数据结构一定是顺序表结构(数组)，因为二分查找需要随机访问
* 原始数据要有序
* 数据量太小不适合，不如直接遍历
* 数据量太大不适合，因为它依赖数组，数组需要连续内存空间，比较难得

# 变体

各种变体的主要区别在于当`$target = $arr[$m]`时，不直接返回`$m`，而是根据需求更新对应的`$l`或`$h`

* 查找第一个值等于给定值的元素
* 查找最后一个值等于给定值的元素
* 查找第一个大于等于给定值的的元素
* 查找最后一个小于等于给定值的元素








