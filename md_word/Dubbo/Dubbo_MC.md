# Dubbo

## 1、Dubbo 架构

### 1. 业务分层：

> ![image-20200313205103804](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200313205103804.png)
>
> + 业务层：
>
>   > + Service：业务层。业务层代码的实现，应用开发；
>
> + RPC 层：
>
>   > + Congfig：配置层。以ServiceConfig和ReferenceConfig为中心，可以直接new配置类，也可以通过spring解析配置生成配置类。
>   > + Proxy：代理层。生产者 (Prodiver) 和消费者 (Consumer) 都会生成 Proxy，用来调用远程接口。生成服务的客户端Stub和服务器端Skeleton，以ServiceProxy为中心，扩展接口为ProxyFactory。；
>   > + Registry：注册层，负责框架的服务注册和发现，扩展接口为RegistryFactory、Registry和RegistryService；
>   > + Cluster：集群容错层，负责容错策略、负载均衡策略以及路由策略，封装多个提供者的路由及负载均衡，并桥接注册中心，以Invoker为中心，扩展接口为Cluster、Directory、Router和LoadBalance。将多个服务提供方组合为一个服务提供方，实现对服务消费方来透明，只需要与一个服务提供方进行交互。；
>   > + Monitor：监控层，负责监控调用次数和调用时间；
>   > + Protocol：远程调用层，封装具体调用过程；
>
> + Remoting 层：
>
>   > + Exchange：信息交换层，建立 Request-Response 模型，封装请求响应模式；
>   > + Transport：网络传输层，将网络封装成统一接口，并可以在这之上扩展更多网络传输方式；
>   > + Serialize：序列化层，负责网络传输的序列化和反序列化；

## 2.其他问题

### 1. zookeeper做注册中心，如果注册中心挂掉，发布者和订阅者还能通信吗？

> + 启动 Dubbo 时，消费者会将 zookeeper 的生产者地址接口数据缓存到本地，按照本地缓存的地址进行调用；
> + 注册中心对等集群，任一台服务器挂掉，回自动切换到另一台；
> + 注册中心全部宕机，服务提供者和消费者都可以通过本地缓存调用；
> + 服务者无状态，任一台宕机后都不影响使用；
> + 服务者全部宕机，消费者将无法使用，并将进行无限次重连，等待服务恢复；

### 2. Dubbo 如何解决安全机制？

> + Dubbo 通过 Token 令牌防止用户绕过注册中心直连，然后在授权中心上管理授权;
> + Dubbo 可以提供服务的黑白名单，控制服务的调用方；

### 3. 集群容错方案？

> + Failover Cluster：失败自动切换（默认）；
> + Failfast Cluster：快速失败，立即报错；
> + Failsafe Cluster：失败安全，出现异常时直接忽略；
> + Failback Cluster：失败自动恢复，记录失败请求，定时重发；
> + ForKing Cluster：并行调用多个服务器，只要一个成功即返回；
> + Broadcast Cluster：广播逐个调用所有服务提供者，任一个报错则报错；

### 4. 负载均衡策略

> + RandomLoadBalance：随机负载，按权重设置随机概率；
> + RoundRobinLoadBalance：轮询负载均衡，轮询选择一个；
> + LeastActiveLoadBalance：最少活跃调用数，相同调用数的随机。活跃数指调用前后计数差。使慢的 Provider 收到更少请求，因为越慢的 Provider 调用前后技术差越大；
> + ConsistentHashLoadBalance：一致性哈希负载均衡，相同参数的请求总是落在同一台机器上；

**负载均衡的优先级：**

>客户端方法 --> 客户端接口-->服务端方法-->服务端接口

### 5. Dubbo结构图

![image-20200314093056868](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200314093056868.png)

> 组件说明：
>
> > + provider：服务提供方，生产者；
> > + Consumer：服务消费方，消费者；
> > + Registry：注册中心；
> > + Monitor：监控中心；
> > + Container：运行容器；
>
> 调用关系：
>
> > + 服务提供者启动时，向注册中心注册服务；
> > + 服务消费者启动时，向注册中心订阅服务；
> > + 注册中心返回服务提供者地址列表给消费者，如果地址有变更，将基于长连接，推送变更数据给消费者；
> > + 服务消费者，将从服务者列表中基于软负载均衡算法，选一台进行调用，如果失败，将调用另一台；
> > + Monitor 监控消费者和服务者，计算调用次数和调用时间，定时每分钟统计一次数据