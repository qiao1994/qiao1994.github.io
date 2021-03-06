# 1.架构到底是什么

# 梳理常见概念

## 系统与子系统
* 系统是一群有关连的个体按照既定的规则组成，并且产生了新的能力，关键是产生了新的能力
* 子系统也是系统，只是与系统之间有大和小的关系

## 模块与组件
* 模块和组件，系统的组成部分，只是从不同的角度差分系统
* 从逻辑的角度拆分系统后得到的单元就是模块
* 从物理的角度拆分系统后得到的单元就是组件
* 划分模块的主要目的是职责分离；划分组件的主要目的是单元复用
* component也可以翻译为“零件”，这就更好理解了

## 框架与架构
* 框架是组件规范，框架是提供基础功能的产品
* 架构是指软件系统的基础结构
* 框架关注的是规范；架构关注的是结构

# 总结
架构是顶层设计；框架是面向编程和配置的半成品；组件是从技术维度上的复用；模块是从业务维度上职责的划分；系统是相互协同可运行的实体
