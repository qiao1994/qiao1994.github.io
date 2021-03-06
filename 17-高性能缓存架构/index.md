# 17.高性能缓存架构

# 某些场景下，单纯依靠存储性能的提升不能解决问题
这些场景下高性能存储也无能为力，这就需要用到缓存系统
* 需要经过复杂的运算后得出的数据
* 读多写少的数据

# 缓存的架构设计要点
## 缓存穿透
### 存储数据不存在
可能被黑客利用做攻击，解决办法是当读不存在值的时，设置一个默认值到缓存中。
### 缓存数据生成耗费大量时间或资源
比如分页缓存，前10页失效的可能性不大，因为用户会一直有大量读取请求；但是后面也没的内容可能失效，日常请求也足够用，但是当爬虫循环请求所有页面时，数据库会被拖慢。
没有太好的解决办法，如果禁止爬虫会影响SEO和推广；做好监控，发现问题后及时处理。

## 缓存雪崩
缓存雪崩是指当缓存失效后引起系统性能急剧下降的情况，当多个请求到达时，会请求多次数据库。
### 更新锁
对缓存更新操作进行加锁保护，保证只有一个现成能够进行缓存更新。
### 后台更新
由后台现成来更新缓存，缓存本身的有效期设置为永久，后台线程定时更新。
#### 特殊场景：当内存不够时，会删除一些数据，这些数据就返回空了
* 后台频繁读缓存，如果发现数据被删除，就立即更新缓存
* 业务线程发现缓存失效后，通过消息队列发送一条消息给后台线程更新缓存 √

## 缓存热点
设置多副本解决；需要注意，不同的缓存副本不要设置统一的过期时间，否则会引起缓存雪崩

## 实现方式
* 程序代码实现
* 中间件实现
