# Redis

## 一，基础

1. 什么是Redis：

   > + 是一个 Key-Value 类型的在内存中操作的 NoSQL 数据库;
   >
   > + 优点：
   >
   >   > + 性能优秀，因为是在内存中操作的数据库，读写速度很快；
   >   > + 单进程单线程，线程安全；
   >   > + 支持数据持久化，可以将内存中的数据保存到磁盘中，重启时加载；
   >   > + 主从复制，哨兵模式，高可用；
   >   > + 用作分布式锁；
   >   > + 作为消息中间件使用

2. Redis 支持的数据类型：

   > + string (字符串)：一个 key 对应一个value，value不仅是 string ，由于string 类型是二进制安全的，所以string 可以包含任何类型；每个值最大储存512M；
   > + list (列表)：简单的字符串列表，按照插入顺序排序；（常用命令：lpush、rpush、lpop、rpop、lrange(获取列表片段)等）
   > + hashes (散列)：是一个 key-value 的集合。Redis 的 hash 是一个 key-value 映射表，适合存储对象；（常用命令：hget,hset,hgetall等）
   > + set (集合)：set 是 string 的无序集合。集合是通过 hashtable 实现的，所以 set 中的元素没有重复，且是无序的；
   > + sorted sets (有序集合)：和 set 一样是集合，也不允许有重复的元素；（常用命令：zadd、zrange、zrem、zcard等。）

