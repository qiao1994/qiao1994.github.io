# 《深入理解Yii2.0》学习笔记


## 《深入理解Yii2.0》

今天是周日

花了一个上午+半个下午的时间看完了这本“书”，链接：<http://www.digpage.com/index.html>

其中【Yii基础】和【Yii模式部分】两部分写得很赞，很有启发，非常感谢作者，这里记录下这两部分的笔记；

但是【Yii与数据库】部分稍感深度不够，只罗列了事件和事件的基本原理，没有给出各个数据库相关的类以及关联关系。

### Yii基础

![Base](/images/PHP/base.svg)

### 依赖注入

![Di](/images/PHP/di.svg)

### 整体

![All](/images/PHP/all.svg)

> 图中的所有绑定、解绑、注册、注入等操作，都是在对应的属性数组中插入或删除元素。



## 粗读代码

上面的书里给出了Yii的概览情况，我又趁热打铁，花了后面半个下午的事件粗读了几个重要类的代码，这里贴出梗概备忘。

### 数据模型

![数据模型](/images/PHP/model.svg)

### 控制器

![控制器](/images/PHP/controller.svg)

### 独立动作

![独立动作](/images/PHP/independent-action.svg)



How a full day

bye.
