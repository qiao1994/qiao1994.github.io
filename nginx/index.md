# Nginx学习笔记


## 基本组成

nginx由内核和模块组成,模块分为handler、filter、proxy类模块

## 进程模型

nginx默认是多进程方式，一个master进程监听socket，并fork多个(worker_process个)worker进程，每个worker进程有个数量为N(worker_connections个)的连接池；这样的一个nginx理论上最大能同时处理worker_process*worker_connections个请求。

## 配置文件

nginx的配置文件分为以下几部分

- main
  - events
  - HTTP
    - server
      - location
  - upstream

### 关键参数

- worker_process worker进程数
- worker_connections 每个worker连接池中连接的数量
- use epoll 使用epoll模式

## I/O多路复用

- IO模式
  - 阻塞IO：发起请求后等待IO完成才继续执行
  - 非阻塞IO：发起请求后立即拿到结果，但是如果IO没有就绪，还需要不断轮询直到成功
  - IO多路复用：
    - select：轮询文件描述符，有最大轮询数量限制
    - poll：轮询文件描述符，无最大轮询数量限制(用了链表)
    - epoll：无需轮询，当有io就绪后通过回调的方式通知内核，内核再调起用户进程
      - 水平触发(LT)：每次都会通知当前已经就绪的文件描述符，直到用户进程处理该描述符
      - 边缘触发(ET)：只在一个描述符由非就绪转为就绪后通知一次，假定用户进程一定会处理
    - 对比：select/poll适合活跃fd比例高的情况(遍历效率高)；epoll适合活跃fd比例低的情况(避免遍历带来的浪费)
  - 异步IO：发起请求后立即拿到结果，当IO就绪后，通知进程

## 与PHP交互

### 一些定义

- CGI：通用网关接口
- FastCGI：常驻进程的CGI程序
- PHP-FPM：PHP FastCGI Process Manager
  - 主要配置：
    - pm.max_requests 处理N个请求后就重新创建进程
    - 进程启动模式：
      - dynamic
        - pm.max_children 最大wrapper进程数
        - pm.start_servers 启动时的wrapper进程数
        - pm.min_spare_servers 保证最小空闲进程数，如果空闲进程小于这个值，就会尝试创建新的进程
        - pm.max_spare_servers 最大空闲进程数，如果空闲进程大于这个值，就会尝试清理
      - static
        - pm.max_children 启动N个wrapper进程
      - 对比：动态适合小内存机器，灵活分配进程，省内存。静态适用于大内存机器，动态创建回收进程对服务器资源也是一种消耗。

### 关联关系

Nginx拿到php请求后，会转发到指定的socket(同时转发的还有服务器信息和请求信息)，FastCGI的wrapper进程拿到请求后转发给后端的CGI-APP处理(这里即PHP程序)，处理完成后再把结果原路返回给Nginx。

### 关联方式

- TCP：即ip:port形式，可以跨服务器
- UNIX Domain Socket：经过内核，不经过网络

## 参考资料

- 原理：https://zhuanlan.zhihu.com/p/67636582
- 参数调优
  - https://www.cnblogs.com/yueminghai/p/8657861.html
  - https://www.cnblogs.com/songgj/p/9113901.html
  - https://blog.csdn.net/fyy1219/article/details/80270845
- 与php交互
  - https://blog.csdn.net/wuhuagu_wuhuaguo/article/details/83032578
  - https://www.jianshu.com/p/eab11cd1bb28
  - https://www.jianshu.com/p/c892f3f0c9bd
- I/O多路复用
  - https://www.jianshu.com/p/dfd940e7fca2
  - https://segmentfault.com/a/1190000003063859
