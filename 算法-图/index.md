# 算法:图

# 定义
大概长这样
![图](/images/pasted-11.png)

# 存储
## 邻接矩阵
邻接矩阵存储是用一个二位数组存储图，如果`i`和`j`之间有边，则矩阵的`[i][j]`位置有值。
* 劣势是存储存在浪费情况
	* 无向图一条边需要存储在两个位置
	* 顶点多但是边少的情况存在大量的空间浪费
* 优势是获取两个顶点是否存在连接的效率很高`O(1)`

![邻接矩阵](/images/pasted-12.png)

## 邻接表
邻接表是用类似哈希表的方式存储表，数组中每个节点的链表中存储这个顶点关联的顶点
![邻接表](/images/pasted-13.png)

# 搜索
## 广度优先搜索
以起点的顶点为中心，搜索它周围所有的顶点；
再以它周围的每一个顶点，搜索它周围所有没被搜索过的顶点；
以此类推，直到找到目标顶点

## 深度优先搜索
从起点顶点出发，沿着一个方向一直前进(每次递归调用就是前进了一步)，如果找到了就直接返回；
否则就回退一步(递归函数执行完成，就会返回到调用栈的上一个函数中，即回退了一步)，换一个没有前进过的方向继续尝试；
直到找到目的顶点
## 代码
```
<?php
require_once('linked-list.php');
require_once('queue.php');
class Graph {
    //邻接表(链表数组)
    public $adjacencyList = [];

    /**
     * 增加一条边 (无向图,所以需要增加两个指向)
     * @param integer $start
     * @param integer $end
     * @return void
     */
    public function addEdge($start, $end) {
        //其他语言可能需要指定邻接表的大小，并把邻接表都初始化为链表，这里选择动态创建
        if (!isset($this->adjacencyList[$start])) {
            //没有拉链，创建
            $this->adjacencyList[$start] = new LinkedList($end);
        } else {
            //已有拉链，在链尾增加元素
            $this->adjacencyList[$start]->add($end);
        }

        if (!isset($this->adjacencyList[$end])) {
            $this->adjacencyList[$end] = new LinkedList($start);
        } else {
            $this->adjacencyList[$end]->add($start);
        }
    }


    /**
     * 广度优先搜索
     * @param integer $start
     * @param integer $end
     * @return void
     */
    public function bfs($start, $end) {
        if ($start == $end) {
            return ;
        }
        $visited = []; //记录已经被访问过的点
        $queue = new Queue(); //用来存储已经被访问，但相连的顶点还没有被访问的点
        $prev = []; //记录搜索路径 $prev[x] = y 表示遍历是从y来到x

        //初始化$start顶点在上述三个map中的状态
        $visited[$start] = true;
        $queue->in($start);

        //遍历队列中所有需要处理的顶点
        while (!$queue->isEmpty()) {

            $center = $queue->out(); //本次遍历的立足点
            $handle = $this->adjacencyList[$center]->head; //本次遍历的起点

            //遍历
            while ($handle != null) {
                if (!isset($visited[$handle->data])) {
                    echo "center : {$center}; handle : {$handle->data}" . PHP_EOL;
                    //prev
                    $prev[$handle->data] = $center;
                    //终止条件
                    if ($handle->data == $end) {
                        $this->print($prev, $start, $end);
                        return ;
                    }
                    //visited
                    $visited[$handle->data] = true;
                    //queue
                    $queue->in($handle->data);
                }
                $handle = $handle->next;
            }
        }
    }



    public $found = false;

    /**
     * 深度优先搜索:一条道走到黑,如果找到了就返回;否则回退几步换个没走过的方向继续尝试,直到找到目的地
     * @param integer $start
     * @param integer $end
     * @return void
     */
    public function dfs($start, $end) {
        $this->found = false;
        $visited = [];
        $prev = [];
        $this->recursionDfs($start, $end, $visited, $prev);
        $this->print($prev, $start, $end);
    }

    /**
     * 深度优先搜索递归函数
     * @param integer $center
     * @param integer $end
     * @param array $visited 
     * @param array $prev
     */
    public function recursionDfs($center, $end, &$visited, &$prev) {
        if ($this->found) {
            return ;
        }

        $visited[$center] = true;

        //终止条件
        if ($center == $end) {
            $this->found = true;
            return ;
        }

        $handle = $this->adjacencyList[$center]->head;
        while ($handle != null) {
            if (!isset($visited[$handle->data])) {
                echo "center : {$center}; handle : {$handle->data}" . PHP_EOL;
                $prev[$handle->data] = $center;
                //前进一步
                $this->recursionDfs($handle->data, $end, $visited, $prev);
            }
            $handle = $handle->next;
        }
        //最终没有匹配成功,后退一步
    }

    /**
     * 根据$prev打印路径
     * @param array $prev
     * @param integer $start
     * @param integer $end
     * @return void
     */
    public function print($prev, $start, $end) {
        if (($prev[$end]) && ($start != $end)) {
            $this->print($prev, $s, $prev[$end]);
        }
        echo $end . ' ';
    }
}

$graph = new Graph();
$graph->addEdge(0, 1);
$graph->addEdge(0, 3);
$graph->addEdge(1, 2);
$graph->addEdge(1, 4);
$graph->addEdge(2, 5);
$graph->addEdge(3, 4);
$graph->addEdge(4, 5);
$graph->addEdge(4, 6);
$graph->addEdge(5, 7);
$graph->bfs(0, 7);

$graph = new Graph();
$graph->addEdge(1, 2);
$graph->addEdge(1, 4);
$graph->addEdge(2, 3);
$graph->addEdge(2, 5);
$graph->addEdge(3, 6);
$graph->addEdge(5, 7);
$graph->addEdge(5, 6);
$graph->addEdge(6, 8);
$graph->dfs(1, 7);
```
## 运行结果
```
center : 0; handle : 1
center : 0; handle : 3
center : 1; handle : 2
center : 1; handle : 4
center : 2; handle : 5
center : 4; handle : 6
center : 5; handle : 7
1 2 5 7 
center : 1; handle : 2
center : 2; handle : 3
center : 3; handle : 6
center : 6; handle : 5
center : 5; handle : 7
center : 6; handle : 8
center : 1; handle : 4
1 2 3 6 5 7 
```
## 辅助类
### LinkedList
```
<?php
class Node {
    public function __construct($value) {
        $this->data = $value;
    }

    public $data = '';
    public $next = null;

} 

class LinkedList {
    public function __construct($value) {
        $this->head = new Node($value);
    }
    
    public $head = null;

    public function printList() {
        $node = $this->head;
        while ($node->next != null) {
            echo $node->data . ' ';
            $node = $node->next;
        }
    }

    public function add($value) {
        $tempNode = $this->head;
        while ($tempNode->next != null) {
            $tempNode = $tempNode->next;
        }
        $tempNode->next = new Node($value);
    }
}
```
### Queue
```
<?php
class Queue {
    public $data = [];

    public function in($value) {
        $this->data[] = $value;
    }

    public function out() {
        return array_shift($this->data);
    }

    public function isEmpty() {
        return count($this->data) == 0;
    }
}
```
