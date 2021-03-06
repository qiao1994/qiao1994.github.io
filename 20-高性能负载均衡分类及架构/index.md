# 20.高性能负载均衡：分类及架构

# 负载均衡分类
## DNS负载均衡
### 优点
* 简单，成本低
* 就近访问，提升访问速度

### 缺点
* 更新不及时
* 扩展性差
* 分配策略简单

>有些公司针对业务特点实现自己的DNS系统，优缺点与此刚好相反

## 硬件负载均衡 F5 A10
### 优点
* 功能强大：全面支持各层级的负载均衡，支持全面的负载均衡算法，支持全局负载均衡
* 性能强大：软件负载均衡最高支持约10万级并发，硬件可以支持100万以上的并发
* 稳定性高
* 支持安全防护：兼具防火墙、防DDoS攻击等安全功能

### 缺点
* 价格昂贵
* 扩展能力差

## 软件负载均衡 Nginx LVS
### 优点
* 简单
* 便宜
* 灵活

### 缺点
* 性能一般：Nginx大约能支撑5万并发
* 功能不如硬件负载均衡那么强大
* 一般不具备防火墙等安全功能

# 负载均衡典型架构
* DNS用于实现地理级别的负载均衡
* 硬件负载均衡用于实现集群级别的负载均衡
* 软件负载均衡用于实现机器级别的负载均衡
