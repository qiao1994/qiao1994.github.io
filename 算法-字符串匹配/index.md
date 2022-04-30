# 算法:字符串匹配

## BF算法
暴力匹配

### 代码实现
```
<?php
/**
 * 暴力匹配
 * @param string $main 主串
 * @param string $pattern 模式串
 * @return mixed integer/boolean
 */
function bf($main, $pattern) {
    $main = str_split($main);
    $pattern = str_split($pattern);
    foreach ($main as $mk => $mv) {
        //匹配主串的mk~mk+pattern.size位置的内容
        for ($pk = 0; $pk < count($pattern); $pk++) {
            if ($main[$mk + $pk] == $pattern[$pk]) {
                if ($pk == (count($pattern) - 1)) {
                    //匹配完成
                    return $mk;
                }
                //当前字符匹配
                continue;
            } else {
                //不匹配
                break;
            }
        }
    }
    return false;
}
//$result = bf('abcdefg', 'bcq');
//var_dump($result);
```


## RK算法
通过哈希算法对主串中的 n-m+1 个子串分别求哈希值，然后然后逐个与模式串的哈希值比较大小。
为了提高哈希算法的效率，可能需要针对字符集设计相应的哈希算法，比如把每个字母都映射成数字等。

### 代码实现
```
<?php
/**
 * 用hash的方式做匹配，免去逐个匹配模式串的字符
 * 这里用的md5是随便选择的，md5实际上还是会考虑每个字符；一般实际应用场景会用如下两种hash函数来减少hash函数的复杂度
 * 1.把字符集中的每个字符都映射成数字，hash函数是对这个映射结果求一个[字符集大小]进制的数值
 * 2.如果字符集过大，那就不求真正的[字符集大小]进制的数值，而是直接用字面拼接值做为hash结果；这种情况会有hash冲突，所以hash匹配成功后还需要比较下原文是否一致
 * @param string $main 主串
 * @param string $pattern 模式串
 * @return mixed integer/boolean
 */
function rk($main, $pattern) {
    $pHash = md5($pattern);
    $mainArray = str_split($main);
    foreach ($mainArray as $mk => $mv) {
        //截取主串中mk~mk+count($pattern)位置的内容
        $mPart = substr($main, $mk, strlen($pattern));
        //echo "mPart : {$mPart}; $pattern" . PHP_EOL;
        $mPartHash = md5($mPart);
        if ($pHash == $mPartHash) {
            return $mk;
        }
    }
    return false;
}
//$result = rk('abcdefg', 'bc');
//var_dump($result);
```


## BM算法

模式串与主串匹配的过程，可以看成是模式串在主串下滑动的过程，上述两种算法都是每次滑动一位，所以效率较低。BM算法本质是寻找到一些能多滑动几位的规则，从而提高效率。

### 坏字符规则

#### 举个栗子
如下图，其中的`c`在模式串中压根不存在，所以不可能和模式串发生匹配，如果按照前面描述的算法，还是需要每次滑动一位
![每次滑动一位](https://qiao1994.github.io/images/pasted-14.png)

#### 模式串中不存在坏字符的情况
这个栗子里我们可以直接把模式串移动到`c`的后面(这里的`c`就是坏字符)
![模式串中不存在坏字符的](https://qiao1994.github.io/images/pasted-15.png)

#### 模式串中存在坏字符的情况
如果模式串中存在坏字符，那我们就不能直接把模式串滑动到坏字符后面了，需要把坏字符和模式串中坏字符的位置对齐
当模式串中存在多个坏字符时，让坏字符和模式串中最后一个坏字符的位置对齐(避免错过匹配)
![模式串中存在坏字符](https://qiao1994.github.io/images/pasted-16.png)

#### 坏字符的问题
当主串是`aaaaaaaaaaaaaaa`，模式串是`baaa`时，坏字符将是`a`，因为模式串中的`a`都在`b`的后面，所以无法计算移动距离

#### 实现tips
坏字符规则中最耗时的是在模式串中寻找坏字符的位置，我们可以先把模式串中每一个字符最后出现的位置准备到一个数组中，需要用的时候直接访问数组中的值即可。


### 好后缀规则

#### 举个栗子
* 好后缀在模式串中如果存在除了后缀以外的匹配，将两者对齐
![好后缀规则1](https://qiao1994.github.io/images/pasted-17.png)
* 当好后缀的后缀子串和模式串的前缀子串有重合时，将两者对齐
![好后缀规则2](https://qiao1994.github.io/images/pasted-18.png)

#### 实现tips
好后缀规则计算最复杂的部分就是在模式串中寻找`好后缀的匹配`和`好后缀的后缀子串和模式串的前缀子串的匹配`，对于此，我们初始化两个数组
* 用`suffix`数组存储`k`长度的后缀，在模式串中对应匹配的起始下标
* 用`prefix`来记录模式串的后缀子串是否能匹配模式串的前缀子串
![suffix和prefix数组](https://qiao1994.github.io/images/pasted-19.png)

### 何时使用哪个规则
我们分别计算两种规则的移动距离，选择其中最大的作为最终移动规则

### 代码实现
```
<?php
function bm($main, $pattern) {
    //好后缀信息初始化
    $suffix = [];
    $prefix = [];
    generateSuffixAndPrefix($pattern, $suffix, $prefix);

    $main = str_split($main);
    $pattern = str_split($pattern);

    //坏字符信息初始化
    $patternCharLastPosition = []; //模式串中每个字符最后出现的位置(为了在模式串中寻找坏字符的时候更高效)
    foreach ($pattern as $pk => $pv) {
        $patternCharLastPosition[$pv] = $pk;
    }
    $patternPosition = 0; //模式串第一个字符和主串对应的位置

    //主串中剩余的字符还足够给模式串匹配用，就继续循环
    while ((count($main) - 1 - $patternPosition) >= count($pattern)) {
        //打印当前状态
        echo implode('', $main) . PHP_EOL;
        for ($i = 0; $i < $patternPosition; $i++) {
            echo " ";
        }
        echo implode('', $pattern) . PHP_EOL;

        //从后往前匹配模式串
        for ($pk = count($pattern) - 1; $pk >= 0; $pk--) {
            //当前字符没有匹配成功;获取坏字符$pk
            if ($main[$patternPosition + $pk] != $pattern[$pk]) {
                echo "bad char : {$main[$patternPosition + $pk]}" . PHP_EOL;
                break;
            }
        }
        if ($pk < 0) {
            //匹配成功
             return $patternPosition;
        }
        //滑动模式串位置
        //如果模式串中没有坏字符,则直接移到坏字符右边
        if (!isset($patternCharLastPosition[$main[$patternPosition + $pk]])) {
            $patternCharLastPosition[$main[$patternPosition + $pk]] = -1;
        }
        $badCharMoveLength = $pk - $patternCharLastPosition[$main[$patternPosition + $pk]];

        //如果有好后缀的话 这里有问题
        if ($pk < (count($pattern) - 1)) {
            $goodSuffixMoveLength = moveByGoodSuffix($pk, count($pattern), $suffix, $prefix);
            $moveLength = $badCharMoveLength > $goodSuffixLength ? $badCharMoveLength : $goodSuffixLength;
        } else {
            $moveLength = $badCharMoveLength;
        }

        echo "goodSuffixLength : {$goodSuffixLength}; badCharMoveLength : {$badCharMoveLength}; moveLength : {$moveLength}" . PHP_EOL;

        $patternPosition = $patternPosition + $moveLength;

        echo PHP_EOL;
    }
    return false;
}
$result = bm('abcdefgafg', 'gafg');
var_dump($result);

function moveByGoodSuffix($badCharPosition, $patternLength, $suffix, $prefix) {
    $goodSuffixLength = $patternLength - 1 - $badCharPosition;
    if (isset($suffix[$goodSuffixLength])) {
        return $badCharPosition - $suffix[$goodSuffixLength] + 1;
    }
    for ($r = $badCharPosition + 2; $r < $patternLength - 1; $r++) {
        if ($prefix[$r]) {
            return $r;
        }
    }
    return $patternLength;
}

/**
 * 构造suffix和prefix
 * 左指针从0开始一直往右移动，一直和最后一个字符匹配，什么时候匹配到了，就基于这个左指针往左移，看有没有更长的匹配
 * @param string $pattern
 * @param array $suffix
 * @param array $prefix
 * @return void
 */
function generateSuffixAndPrefix($pattern, &$suffix, &$prefix) {
    $pattern = str_split($pattern);
    $m = count($pattern);
    for ($pk = 0; $pk < $m - 1; $pk++) {
        $left = $pk;
        $suffixLength = 0;
        $right = $m-1;
        echo "left : {$left}; right : {$right}; suffixLength:{$suffixLength}" . PHP_EOL;
        while (($left >= 0) && ($pattern[$left] ==  $pattern[$m-1-$suffixLength])) {
            $right = $m-1-$suffixLength;
            echo "left : {$left}; right : {$right}; suffixLength:{$suffixLength} success" . PHP_EOL;
            $left--; // i->0
            $suffixLength++; // 0->i
            $suffix[$suffixLength] = $left + 1;
        }
        if ($left == -1) {
            $prefix[$suffixLength] = true;
        }
    }
}
```

## KMP算法
### 举个栗子
当遇到坏字符时，把模式串往后移，移多少取决于好前缀的后缀子串和模式串的前缀子串的最长匹配位置。

![好前缀和坏字符](https://qiao1994.github.io/images/pasted-20.png)

![移动方式](https://qiao1994.github.io/images/pasted-21.png)

### 实现tips
此时核心诉求就变成了，对于模式串的每一个长度的`前缀子串的后缀子串`，寻找对应的`模式串的前缀子串`。
因为这个寻找与主串无关，根据模式串就可以实现，所以只需要根据模式串初始化好这个数组就可以(预处理这里用动态规划)
![next数组](https://qiao1994.github.io/images/pasted-22.png)


>这里真正难搞的是实现tips里的suffix、prefix和next数组，这些内容我也是勉强理解，等后面再回头看搞懂的时再回来补上


## Trie树
### 定义
给个图，一看就懂噻
![Trie树](https://qiao1994.github.io/images/pasted-23.png)

### Trie树很耗内存吗
如果用数组存储指针，那每个节点都需要存储完整的字符集，这种情况会比较消耗内存；但是如果牺牲一点性能，用散列表等结构存储，会有很大改善。

### Trie树的适用场景(局限性)
* 字符串的字符集不能太大
* 字符串的前缀重合较多
* 需要自己实现，复杂
* 用到了指针，不连续内存，对cpu缓存不友好

一般工程上更多使用红黑树或散列表，Trie树比较适合搜索引擎的模糊搜索词提醒等场景。

## AC自动机
AC自动机和Trie树的关系和KMP算法和BF算法的关系一样，都是在匹配失败后多跳几个节点以提高效率

>字符串匹配这块理解得实在不好，后续再返回来更深理解一遍
