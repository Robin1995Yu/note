# ZooKeeper
## Zookeeper的用途，选举的原理是什么。
Zookeeper是一个分布式的开源程序协调系统

Zookeeper的核心是原子广播 保证了各个节点之间的同步 为了实现这个功能 使用了ZAB协议
- 恢复模式
  - 选主
  - 在启动或者leader奔溃时启动
- 广播模式
  - 同步
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