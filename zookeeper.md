# ZooKeeper
## Zookeeper的用途，选举的原理是什么。
Zookeeper是一个分布式的开源程序协调系统

Zookeeper的核心是原子广播 保证了各个节点之间的同步 为了实现这个功能 使用了ZAB协议
- 恢复模式
  - 选主
  - 在启动或者leader奔溃时启动
- 广播模式
  - 同步
- ZAB协议
  - 原子广播
  - 用于保证分布式事务的最终一致性
  - 支持奔溃恢复的原子广播协议
  - 实现数据一致性
    - 只有leader负责写请求 然后将数据同步到follower
    - 超过半数节点写入成功事务才会被提交
  - 特性
    - 已经在在leader提交的事务会被所有服务器提交
    - 确保丢弃只在leader上提出而未提交的事务
  - 作用
    - 保证单线程写（leader）然后以事务的形式广播到所有的副本
    - 保证事务的顺序执行 新选举出来的leader只有提交了之前leader的事务才能接受新的事务
    - 当leader出现异常的时候整个集群还是可以正常工作
  - 原理
    - 发现
      - leader会维护一个follower列表 客户端可以和这些follower通信
    - 同步
      - leader需要负责将本身的数据同步给follower 满足cap的可用性和分区容错性
    - 广播
      - leader接受客户端的提议 并将提议请求广播给所有的follower
  - zab的核心
    - 定义了事务请求的处理方式
    - 所有事务都会被全局唯一的服务器处理（leader）
    - leader需要将事务转化为提议 并将之广播给所有的follower
    - 发送之后需要等待follower的反馈 那么就会他送commit请求 要求所有follower提交
  - 协议内容
    - 奔溃恢复
      - 当leader发生断网 奔溃推出 异常重启的时候 zab协议就会进入奔溃恢复阶段
## Zookeeper watch机制原理。
    zookeeper 怎么保证主从节点的状态同步？
    集群中有3台服务器，其中一个节点宕机，这个时候 zookeeper 还可以使用吗？
    zookeeper都有哪些功能？
    什么是paxos算法，什么是zab协议。
    zookeeper 是如何保证事务的顺序一致性的？
    zookeeper 负载均衡和 nginx 负载均衡区别
    Zookeeper 的典型应用场景
    说说四种类型的数据节点Znode
    Zookeeper 的服务器角色（Leader，Follower，Observer）
    Zookeeper 有哪几种几种部署模式？