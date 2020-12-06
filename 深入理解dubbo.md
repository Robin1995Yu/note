#深入理解Dubbo
## Dubbo组件
- 组件
  - Provider 提供者
    - 一个集群中的服务提供者
    - 会将自己的服务注册到注册中心 方便消费者找到
  - Consumer 消费者
    - 服务的调用者
    - 通过注册中心得到提供者的信息
    - 通过RPC远程调用服务
  - Registry 注册中心
    - 服务的注册与发现
  - Monitor  监控中心
    - 统计服务的效用次数和调用时间
- 调用关系
  - 提供者在启动时将自己的信息注册到注册中心
  - 消费者在启动时从注册中心订阅自己需要的服务的地址 注册中心异步的将数据返回给消费者 消费者通过预设的平衡算法选择提供者调用
  - 监控中心统计服务的调用次数和调用时间 并每分钟固定一次的将数据发送给注册中心
## Dubbo项目结构
- SDK模块（也有些叫做API）
  - 提供服务的接口
  - 提供服务中的实体类
- Provider模块
  - 需要实现SDK中的接口并暴露
- Comsumer模块
  - 通过远程调用Provider中的实现来实现一些别的功能
## 如何确定一个Dubbo接口
- 服务接口
- 服务分组
- 服务版本
## Dubbo的结构
在Dubbo中 也有类似于互联网的分层结构 每一层依赖于其下一层的实现来完成自己的功能 并为上一层提供服务 每一层都可以使用别的实现 更换实现也不会影响到别的层的功能
![avatar](Dubbo整体架构.png)
- 分层实现
  - Service和Config层
    - 是Dubbo的API层
    - Provider通过ServiceConfig
    - Consumer通过ReferenceConfig
  - 其他均为SPI（Service Provider Interface）层
    - 下面个层都是组件化的 可以被替换
- 具体各层
  - Service
  - Config
  - Proxy
  - Registry
  - Cluster
  - Monitor
  - Protocol
  - Exchange
  - Transport
  - Serialize