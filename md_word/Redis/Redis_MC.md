# Redis

## 一，基础

###1.什么是Redis：

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

###2.Redis 支持的数据类型：

> + string (字符串)：一个 key 对应一个value，value不仅是 string ，由于string 类型是二进制安全的，所以string 可以包含任何类型；每个值最大储存512M；
> + list (列表)：简单的字符串列表，按照插入顺序排序；（常用命令：lpush、rpush、lpop、rpop、lrange(获取列表片段)等）
> + hashes (散列)：是一个 key-value 的集合。Redis 的 hash 是一个 key-value 映射表，适合存储对象；（常用命令：hget,hset,hgetall等）
> + set (集合)：set 是 string 的无序集合。集合是通过 hashtable 实现的，所以 set 中的元素没有重复，且是无序的；
> + sorted sets (有序集合)：和 set 一样是集合，也不允许有重复的元素；（常用命令：zadd、zrange、zrem、zcard等。）

### 3. Redis 的持久化

> + 持久化指的是将内存中的数据写到磁盘中，防止因服务器宕机而导致数据丢失；
>
> + **Redis 提供了两种方式：RDB 和 AOF**
>
>   > + RDB (快照)：保存某个时间节点的全量数据快照；
>   >
>   >   > 手动触发：
>   >   >
>   >   > + save：阻塞 Redis 的服务进程，直到 RDB 文件被创建完毕；
>   >   > + BGSAVE：Fork 出一个子进程来创建 RDB文件，不阻塞服务器进程，lastsave 可以常看最近备份时间；
>   >   >
>   >   > 自动触发：
>   >   >
>   >   > + 根据 redis.config 配置定时触发；
>   >   > + 主从复制，主节点自动触发；
>   >
>   > + AOF (保存写状态)：
>   >
>   >   > 记录除了查询以外的所有变更数据库状态的指令，并以 append 的形式追加到 AOF 文件中；
>   >
>   > + RDB-AOF 混合持久化：
>   >
>   >   > RDB 做镜像持久化，AOF 做增量持久化；
>   >   >
>   >   > + 缺点：兼容性差；
>   >
>   > |      |                             RDB                              |                   AOF                    |
>   > | :--: | :----------------------------------------------------------: | :--------------------------------------: |
>   > | 优点 |                 全量数据快照，文件小，恢复快                 | 可读性高，适合保存增量数据，数据不易丢失 |
>   > | 缺点 | 无法保存最近一次快照之后的数据，数据量大的情况下，会由于 I/O 严重影响性能 |          文件体积大，恢复时间长          |
>   > |      |                                                              |                                          |

## 二、集群

### 1. Redis 集群搭建的三种方式：

####1.主从复制：

> + 主从复制原理：