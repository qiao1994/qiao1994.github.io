# 回溯总结


回溯主要用来解决组合、子集与排列问题，这里给出各类目的典型题与对比表格。

> 其中path表示递归过程中已走过的路径，即树枝

# 组合

1. 给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。链接：[组合](https://leetcode-cn.com/problems/combinations/)
2. 找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。链接：[组合总和iii](https://leetcode-cn.com/problems/combination-sum-iii/)
3. 给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的数字可以无限制重复被选取。链接：[组合总和](https://leetcode-cn.com/problems/combination-sum/)
4. 给定一个（可能有重复元素的）数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的每个数字在每个组合中只能使用一次。链接：[组合总和](https://leetcode-cn.com/problems/combination-sum-ii/)

| 题目       | 1                                   | 2        | 3             | 4                  |
| ---------- | ----------------------------------- | -------- | ------------- | ------------------ |
| 结果条件   | path(递归过程中走过的路径).len == k | sum == k | sum == target | sum == target      |
| startIndex | i + 1                               | i + 1    | i             | i + 1              |
| 去重       |                                     |          |               | 同层去重，树枝不去 |

# 子集

1. 给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。解集不能包含重复的子集。链接：[子集](https://leetcode-cn.com/problems/subsets/)
1. 给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。解集不能包含重复的子集。链接：[子集ii](https://leetcode-cn.com/problems/subsets-ii/)
1. 给定一个整型数组, 你的任务是找到所有该数组的递增子序列，递增子序列的长度至少是2。链接：[递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)

| 题目       | 1              | 2                         | 3                               |
| ---------- | -------------- | ------------------------- | ------------------------------- |
| 结果条件   | 任意(不return) | 任意                      | path.len >= 2                   |
| startIndex | i + 1          | i + 1                     | i + 1                           |
| 去重       |                | 同层去重：sort + continue | 同层去重：不能sort，用set或hash |


# 排列

1. 给定一个 没有重复 数字的序列，返回其所有可能的全排列。链接：[全排列](https://leetcode-cn.com/problems/permutations/)
2. 给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。链接：[全排列ii](https://leetcode-cn.com/problems/permutations-ii/)

| 题目       | 1                                            | 2                                |
| ---------- | -------------------------------------------- | -------------------------------- |
| 结果条件   | path.len == k                                | path.len == k                    |
| startIndex | 0                                            | 0                                |
| 去重       | 树枝去重：用used（因为排列无法用startIndex） | 树枝去重或同层去重都可以，用used |

# 备注

* 排序是为了准确判断i与i-1的关系，否则无法判断

* 序列考虑顺序，所以不同顺序算2个序列，集合不算 
* 元素可重用时，startIndex不+1；元素不可重用时，startIndex要+1
* 子集终止条件比较宽松，且不return
* 去重：
  * 同层去重：用sort加for时continnue的方式或者used的方式；不能sort时可用set或者hash
  * 树枝去重：只能用used
  * used去重：used[i - 1] = false 同层用过，used[i - 1] = true 树枝用过

参考资料：https://www.programmercarl.com/

