# 算法:归并排序

# 基本原理
要排序一个数组，只需要把它的左半部分和右半部分分别排序，再把两个部分按排序合并，即完成了排序。
而当两部分划分到最小，即左部分和右部分都只有一个元素时，直接合并即可。
归并排序的思想是分治，先分(分两部分,分别排序)后治(把两部分合并)

# 主要难点(个人感觉)
merge函数的边界条件需要仔细斟酌,不然容易越界或死循环

# 性能分析
时间复杂度是O(nlogn):归并排序的主要内容是分X治；其中logn是分叉过程中二叉树的最大高度，n是治(merge函数)的耗时
空间复杂度是O(n):因为merge函数在同一时刻需要最大为n的临时空间

# 代码

## 递归实现
```
<?php
    $arr = [11, 8, 3, 9, 7, 1, 2, 5];
    main($arr);
    /**
     * 入口函数
     * @param array $arr
     * @rturn array $arr(有序)
     */
    function main(&$arr) {
        merge_sort($arr, 0, count($arr) - 1);
        return $arr;
    }

    /**
     * 把数组$arr的$l-$h排序
     * @param array $arr
     * @param integer $l
     * @param integer $h
     * @return void
     */
    function merge_sort(&$arr, $l, $h) {
        echo "当前在执行{$l} ~ {$h}的排序\n";
        if ($l >= $h) {
            echo "当前到达一个叶节点 {$l}\n";
            return ;
        }
        $m = floor(($h+$l)/2);
        merge_sort($arr, $l, $m); //左侧有序
        merge_sort($arr, $m+1, $h); //右侧有序
        //左侧和右侧都有序,怎么递归是它的事,但是已经有序了,合并即可
        merge($arr, $l, $m, $h);
    }

    /**
     * 数组$arr的$l-$m,$m-$h两部分有序,把有序的两部分合并为一部分,依然保持有序
     * @param array $arr
     * @param integer $l
     * @param integer $m
     * @param integer $h
     * @return void
     */
    function merge(&$arr, $l, $m, $h) {
        $arrResult = [];
        $i = $l;
        $j = $m + 1;
        echo "开始合并{$l}~{$m}和{$j}~{$h}\n";
        while (($i <= $m) || ($j <= $h)) {
            //echo "合并中:i=>{$i};j=>{$j}\n";
            // 有一个数组已经遍历完成的情况
            if ($i > $m) {
                $arrResult[] = $arr[$j];
                $j++;
                continue;
            }
            if ($j > $h) {
                $arrResult[] = $arr[$i];
                $i++;
                continue;
            }
            if ($arr[$j] < $arr[$i]) {
                //当$j位置的数据小的时才让$j在前面,保证排序稳定性
                $arrResult[] = $arr[$j];
                $j++;
            } else {
                $arrResult[] = $arr[$i];
                $i++;
            }
        }
        echo "合并结果" . json_encode($arrResult) . "\n";
        //把arrResult写回原数组
        $i = $l;
        foreach ($arrResult as $v) {
            $arr[$i] = $v;
            $i++;
        }
    }
```


## 执行结果
```
当前在执行0 ~ 7的排序
当前在执行0 ~ 3的排序
当前在执行0 ~ 1的排序
当前在执行0 ~ 0的排序
当前到达一个叶节点 0
当前在执行1 ~ 1的排序
当前到达一个叶节点 1
开始合并0~0和1~1
合并结果[8,11]
当前在执行2 ~ 3的排序
当前在执行2 ~ 2的排序
当前到达一个叶节点 2
当前在执行3 ~ 3的排序
当前到达一个叶节点 3
开始合并2~2和3~3
合并结果[3,9]
开始合并0~1和2~3
合并结果[3,8,9,11]
当前在执行4 ~ 7的排序
当前在执行4 ~ 5的排序
当前在执行4 ~ 4的排序
当前到达一个叶节点 4
当前在执行5 ~ 5的排序
当前到达一个叶节点 5
开始合并4~4和5~5
合并结果[1,7]
当前在执行6 ~ 7的排序
当前在执行6 ~ 6的排序
当前到达一个叶节点 6
当前在执行7 ~ 7的排序
当前到达一个叶节点 7
开始合并6~6和7~7
合并结果[2,5]
开始合并4~5和6~7
合并结果[1,2,5,7]
开始合并0~3和4~7
合并结果[1,2,3,5,7,8,9,11]
```
