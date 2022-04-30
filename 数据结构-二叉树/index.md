# 数据结构:二叉树

# 定义
## 树
摘自维基百科
>树（英语：Tree）是一种无向图（undirected graph），其中任意两个顶点间存在唯一一条路径。或者说，只要没有回路的连通图就是树。

## 二叉树
每个节点最多有左右两个节点

## 完全二叉树
* 叶子节点都在最下两层
* 最后一层的叶子全靠左
* 除了最后一层，其他层的节点都要达到最大

![完全二叉树](https://qiao1994.github.io/images/pasted-7.png)

## 满二叉树
除了叶子节点，每个节点都有左右两个子节点

# 存储方式
## 链式存储
用类似链表的形式存储，每个节点有左和右两个指针，分别指向左子树和右子树

## 顺序存储
把树的每个节点按照一定的规则存入数组中，存入规则如下
* 根节点在`i=1`的位置
* 根节点的左子节点在`2*i`的位置
* 根节点的右子节点在`2*i+1`的位置
* 依次类推

读取规则也一样，假设当前节点在`i`的位置，则
* 它的左子节点：`2*i`
* 它的右子节点：`2*i+1`
* 它的父节点：`floor(i/2)`

当二叉树是完全二叉树时，使用顺序存储只浪费了`0`这一个位置，但是如果二叉树本身不够完全，则会浪费较多空间。

# 二叉树的遍历
二叉树有前序、中序、后续三种遍历方式，本质都是递归，非常简单，实现如下
## 代码
```
<?php

    class node {
        
        public $data;
        public $left;
        public $right;

        public function __construct($data) {
            $this->data = $data;
        }
    }
    
    // 先建立一棵树
    $root = new node(1);
    $node2 = new node(2);
    $node3 = new node(3);
    $root->left = $node2;
    $root->right = $node3;
    $node4 = new node(4);
    $node5 = new node(5);
    $node2->left = $node4;
    $node2->right = $node5;
    $node6 = new node(6);
    $node7 = new node(7);
    $node3->left = $node6;
    $node3->right = $node7;

    preTraversal($root);
    echo PHP_EOL;
    inTraversal($root);
    echo PHP_EOL;
    postTraversal($root);
    echo PHP_EOL;

    //前序遍历
    function preTraversal($root) {
        if ($root == null) {
            return ;
        }
        echo $root->data . ' ';
        preTraversal($root->left);
        preTraversal($root->right);
    }

    //中序遍历
    function inTraversal($root) {
        if ($root == null) {
            return ;
        }
        inTraversal($root->left);
        echo $root->data . ' ';
        inTraversal($root->right);
    }

    //后序遍历
    function postTraversal($root) {
        if ($root == null) {
            return ;
        }
        postTraversal($root->left);
        postTraversal($root->right);
        echo $root->data . ' ';
    }
```
## 执行结果
```
1 2 4 5 3 6 7 
4 2 5 1 6 3 7 
4 5 2 6 7 3 1
```

# 二叉查找树
## 定义
任意一个节点的左子树中每个节点的值都要小于这个节点的值，右子树节点的值都大于这个节点的值。
它的主要意义在于：`O(logn)`级的增删改查，中序遍历可以直接输出有序结果(这一点散列表做不到)
## 查找
* 如果要查找的值等于根节点，直接返回
* 如果要查找的值小于根节点，递归查找左子树
* 如果要查找的值大于根节点，递归查找右子树

## 插入
* 如果要查找的值大于根节点
	* 根节点的右子树为空，则直接插入到根节点的右子树中
   * 根节点的右子树不为空，则递归右子树找到插入位置
* 如果要查找的值小于根节点，处理方法同上

## 删除
* 如果要删除的节点没有子节点，直接把该节点的父节点中指向要删除节点的指针置为null
* 如果要删除的节点只有一个子节点，直接把该节点的父节点中指向要删除节点的指针指向它的子节点
* 如果要删除的节点有两个子节点，需要找到要删除节点的右子树中的最小节点，把它替换到要删除的节点上

![删除图解](https://qiao1994.github.io/images/pasted-8.png)
