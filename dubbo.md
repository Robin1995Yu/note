## Dubbo的服务请求失败怎么处理
如果超出一定时间 provider没有反馈 说明调用失败

失败会有重试机制 如果超出重试次数 认为调用失败 抛出错误
## dubbo的负载均衡有几种算法?（随机，轮询，最少活跃请求数，一致性hash）
- RandomLoadBalance 随机
  - 根据负载量随机分配
  - 默认的负载均衡算法
- RoundRobinLoadBalance 轮询
  - 根据负载量轮询（轮询后权重自减 如果权重为0 移除出表）
- LeastActiveLoadBalance 最少活跃数
  - 优先将负载分配给当前活跃最小的服务
- ConsistentHashLoadBalance 一致性hash
  - 同样的参数的请求会到同一台机器上
  - 如果有机器退出集群 发往原提供者的请求会平均的分配到其他提供者 不会引起剧烈的变动
## Dubbo 和 Spring Cloud 有什么区别？
- 基础知识点
  - RPC
    - 远程调用过程
    - 协议包括传输协议和编码协议
      - 传输协议
        - gRPC
        - HTTP2.0
        - Dubbo
      - 编码协议
        - 文本
          - XML
          - JSON
        - 二进制
          - ProtoBuf
          - Binpack
- 为何Dubbo效率高于Spring Cloud
  - Dubbo采用单一长连接和NIO 使用的是自定义的TCP和自定义的Hessian2
  - 适合小数据大并发的场景 且消费者远多于生产者 不适合大数据的调用
  - Spring Cloud直接使用HTTP协议
## dubbo都支持什么协议，推荐用哪种？
- dubbo
  - 单一长连接和NIO异步
  - 适合小数据量大并发和消费者远多于生产者的情况
  - 不适合大数据量（除非并发量很低）
- rmi
- hessian
- http
- webservice
- thrift
- memcached
- redis
## 画一画服务注册与发现的流程图
- provider 服务提供与暴露者
  - 在启动时想registry注册自己提供的服务
- consumer 服务消费者
  - 在启动时向服务器注册自己需要的服务
  - 根据软负载均衡算法选择一个provider 如果失败 选择另一个
- registry 服务注册与发现的注册中心
  - 返回provider的列表
  - 在provider发生变化时通知consumer
- monitor 服务的调用次数和调用时间的监视器
  - 以每分钟一次的频率接受consumer和provider上报的调用次数和时间
- contrainer 服务的容器
  - 负责启动和加载provider
## Dubbo默认使用什么注册中心，还有别的选择吗？
- Zookeeper 默认
- Multicast
- Redis
- Simple
## 在 Provider 上可以配置的 Consumer 端的属性有哪些？
- 调用超时 （如果consumer也配置了 以consumer为准）
- 重试次数
  - 以上两个配置尽量配置在provider上
    - provider更加知道服务的情况
- 均衡算法
- 最大并发调用量
## Dubbo启动时如果依赖的服务不可用会怎样？
默认会阻止spring初始化并抛出错误
## Dubbo推荐使用什么序列化框架，你知道的还有哪些？
- hessian2
  - 跨平台 推荐使用
- dubbo
  - 尚未成熟 不推荐使用
- JSON
  - 有FastJson和dubbo实现的简单的序列化
  - 但是性能都很不理想
- java默认的
  - 性能比json还差
## Dubbo默认使用的是什么通信框架，还有别的选择吗？
## 服务上线怎么兼容旧版本？
## Dubbo服务之间的调用是阻塞的吗？
默认是阻塞的 但是可以通过非阻塞的方式 返回的是一个Future
## Dubbo telnet 命令能做什么？
- ls
  - 输出当前所有的服务
  - 输出服务下属的方法
- invoke 调用服务
  - 需要在provider中引入FastJson
- count 统计服务的状态（调用次数等信息）
- status 当前服务的状态 如果全部可以 显示OK
## Dubbo如何一条链接并发多个调用。
## Dubbo 的使用场景有哪些？
- RPC 分布式
## Dubbo 核心功能有哪些？
- Remoting 网络通信
- Cluster 服务架构
  - 多种协议支持
  - 软负载均衡
  - 失败容错
  - 地址路由
  - 动态配置
- Registry 服务注册
  - 服务方可以动态的增加机器
## Dubbo 核心组件有哪些？
- provider 服务提供与暴露者
  - 在启动时想registry注册自己提供的服务
- consumer 服务消费者
  - 在启动时向服务器注册自己需要的服务
  - 根据软负载均衡算法选择一个provider 如果失败 选择另一个
- registry 服务注册与发现的注册中心
  - 返回provider的列表
  - 在provider发生变化时通知consumer
- monitor 服务的调用次数和调用时间的监视器
  - 以每分钟一次的频率接受consumer和provider上报的调用次数和时间
- contrainer 服务的容器
  - 负责启动和加载provider
## Dubbo 服务器注册与发现的流程？
## Dubbo 支持哪些协议，它们的优缺点有哪些？
## Dubbo 的注册中心集群挂掉，发布者和订阅者之间还能通信么？
## Dubbo源码使用了哪些设计模式
## Dubbo集群提供了哪些负载均衡策略？
## Dubbo的集群容错方案有哪些？
## Dubbo 支持哪些序列化方式？
## Dubbo超时重试，Dubbo超时时间设置