# 算法:插入排序

# 基本原理
把数据分成两个区间,已排序区间和未排序区间,每次遍历从未排序区间取出一个元素,插入到左侧已排序区间中的合适位置,遍历n次后数据有序

# 主要难点
如果对于插入排序的原理理解不透彻，循环的时候比较难处理


# 代码
```
<?php
    $arr = [4, 5, 6, 1, 3, 2];
    $arr = insertSort($arr);
    print_r($arr);

    /**
     * 插入排序
     * 把数据分成两个区间,已排序区间和未排序区间,每次遍历从未排序区间取出一个元素,插入到左侧已排序区间中的合适位置,遍历n次,数据有序
     * @param array $arr
     * @return array $arr
     */
    function insertSort($arr) {
        $n = count($arr);
        for ($i = 0; $i < $n - 1; $i++) {
            //$i表示已排序区间的最后一个元素的下标,该元素的右侧为未排序区间
            //$arr[$i+1]待插入,在0~i中选择一个位置插入到该位置之前
            for ($j = 0; $j <= $i; $j++) {
                if ($arr[$i+1] < $arr[$j]) {
                    //i+1插入到j之前
                    insert($arr, $j, $i+1);
                    break;
                }
            }
        }
        return $arr;
    }

    /**
     * 把数组中j位置的数据插入到i位置之前
     * @param array $arr
     * @param integer $i
     * @param integer $j
     * @rertun void 
     */
    function insert(&$arr, $i, $j) {
        echo 'insert:' . $j . '=>' .  $i . PHP_EOL; 
        $temp = $arr[$j];
        for ($k = $j; $k > $i; $k--) {
            $arr[$k] = $arr[$k-1];
        }
        $arr[$i] = $temp;
    }
```

# 执行结果
```
insert:3=>0
insert:4=>1
insert:5=>1
Array
(
    [0] => 4
    [1] => 5
    [2] => 6
    [3] => 1
    [4] => 3
    [5] => 2
)
```

# 优化思路

当前代码选择插入位置时，j是从0移动到i的，这样找到插入位置以后，还需要把插入位置到i的元素依次后移，代码比较复杂。
一个解决办法就是j从i移动到0，如果arr[i+1]比arr[j]大，则直接后移。
这样优化以后，每次选择插入位置并插入的遍历数量会少一些。

# 优化后代码
```
<?php
    $arr = [4, 5, 6, 1, 3, 2];
    $arr = insertSort($arr);
    print_r($arr);

    /**
     * 插入排序
     * 把数据分成两个区间,已排序区间和未排序区间,每次遍历从未排序区间取出一个元素,插入到左侧已排序区间中的合适位置,遍历n次,数据有序
     * @param array $arr
     * @return array $arr
     */
    function insertSort($arr) {
        $n = count($arr);
        for ($i = 0; $i < $n - 1; $i++) { //$i的取值范围是0~n-2;因为当0~n-1都有序时,0~n-2也一定是有序的,且$i=n-1时无$i+1可比
            //$i表示已排序区间的最后一个元素的下标,该元素的右侧为未排序区间
            //$arr[$i+1]为目标元素,待插入,需在0~i中选择一个位置插入
            $target = $arr[$i+1];
            for ($j = $i; $j >= 0; $j--) {
                echo "j=>{$j}" . PHP_EOL;
                if ($target < $arr[$j]) {
                    //当前元素比目标元素大,需要右移为目标元素让出位置
                    $arr[$j+1] = $arr[$j];
                } else {
                    //当前元素不比目标元素大,不需要继续右移了
                    break;
                }
            }
            //把目标元素放入正确的位置
            $arr[$j+1] = $target;
        }
        return $arr;
    }
```
