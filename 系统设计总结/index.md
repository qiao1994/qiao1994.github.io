# 系统设计总结


| 项目                      | 数据库                                                       | 关键问题                                                     | 数据分片                                                     | Cache                 |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------- |
| 1.概述                    |                                                              |                                                              |                                                              |                       |
| 2.TinyURL                 | NoSQL                                                        | l Hash方式选择，考虑容量、数量l 离线预生产Key                | l 按首字母：数据不均匀l 按hash分：√                          | l 淘汰策略：LRU       |
| 3.Pastebin                | l MySQL or NoSQ(元信息)l 对象存储(数据)                      | l 离线预生产Key                                              | 同上                                                         | 同上                  |
| 4.Instagram               | l 对象存储or HDFS(数据)l NoSQL(PhotoID=>metadata)l wide-column datastore (relationship[user with phots and user follow]) | 推(粉丝多的人)拉(粉丝少的人)结合                             | l UserID：不均匀l PhoteId:√可以离线预生成PhotoID             | l CDNl LRUl 80-20原则 |
| 5.Dropbox                 | l MySQL(ACID)                                                | l 数据分片：备份成本与同步速度l 保持长连接：long pollingl 同步数据时需要用消息队列来削峰l 数据去重：实时去重 or 异步去重 | l 垂直分区（用户信息与文件信息分开）：一致性、扩展性l 按首字母：数据不均匀l 按Hash分：√ | l Memcachedl LRU      |
| 6.Facebook Messenger      | HBase：column-oriented key-value NoSQL                       | l Pull or Push？Push每个client都需要和server建立长链接（long polling or webscokets）l 在线状态可以按需取用，不一定需要实时更新 | l UserID：okl MessageID：获取一串信息将非常慢                |                       |
| 7.Twitter                 | 同Instagram                                                  |                                                              | l UserID：热门用户导致数据不均匀l TwitterId：查询一个用户的一串信息时，非常慢l 按时间：读很友好，但是写基本都是写到最近的服务器，不均匀l 按时间+TwitterId：把twitterId设计成时间+序号的方式 |                       |
| 8.Youtube or Netflix      | l MySQL：metadatal HDFS：videol BigTable：trumbnails         | l 读写分离l 视频去重可以分块去                               | l UserID：热门用户导致数据不均匀l VideoId：查询视频时需扫描所有服务器，然后做merge；加cache缓解 |                       |
| 9.Typeahead Suggestion    |                                                              |                                                              |                                                              |                       |
| 10.API Rate Limiter       |                                                              | l 固定窗口(hash[userid][ip][x秒] => count)l 滑动窗口(hash[userid][ip][1/60单位] => count) |                                                              |                       |
| 11.Twitter Search         |                                                              |                                                              |                                                              |                       |
| 12.Web Crawler            |                                                              |                                                              |                                                              |                       |
| 13.Facebook Newsfeed      |                                                              |                                                              |                                                              |                       |
| 14.Yelp or Nearby Friends |                                                              |                                                              |                                                              |                       |
| 15.Uber backend           |                                                              |                                                              |                                                              |                       |
| 16.总结                   |                                                              |                                                              |                                                              |                       |

参考资料：https://www.educative.io/courses/grokking-the-system-design-interview
