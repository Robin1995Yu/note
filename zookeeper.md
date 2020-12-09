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
## Zookeeper的选举
- 分类
  - 通过算法分类
    - LeaderElection
    - FastLeaderElection
      - 标准的Fast Paxos算法
    - FastLeaderElection比LeaderElection的优点是可以快速收敛
  - 通过协议分类
    - UDP
    - TCP
  - 当前默认的是TCP的FastLeaderElection
  - 别的方式在之后的更新中会被移除
- 节点的状态
  - LOOKING
    - 节点不确定Leader状态 会发起Leader选举
  - FOLLOWING
    - Follower状态
  - LEADING
    - Leader状态
  - OBSERVING
    - Observer状态
- 选票数据结构
  - logicClock 一个自增的整数 表示当前节点发起了多少轮选举
  - state 当前节点状态
  - self_id 当前节点的ID（即myid）
  - self_zxid 当前节点保存的最大的zxid（事务id） 即最后一次完成的事务
  - vote_id 投给节点的ID（即myid）
  - vote_zxid 投给节点的最大zxid
- 投票过程
  - 自增logicClock 所有有效的投票都必须在一轮中
  - 初始化选票 在投票之前 节点会将自己的投票箱清空
    - 同一时间选票箱中只会有最新一轮的选票
  - 发送初始化选票 每个节点会将第一票投给自己
  - 接收外部选票
    - 节点会尝试接收外部的选票
    - 如果无法获取 会确认是否和别的节点有连接 如果有 发送选票 如果没有 重新连接
  - 判断选举轮次 在节点收到选票后会比较选票的logicClock和自己的logicClock是否匹配
    - 落后 清空自己的选票箱 将自己的logicClock置为最新的logicClock
    - 领先 忽略这张选票
    - 相等 进行选票PK
  - 选票PK
    - 如果接收到的选票的vote_zxid大于自己选票的vote_zxid 将自己选票的vote_id和vote_zxid改为这张选票 并广播出去
    - 如果收到的选票的vote_zxid等于自己选票的vote_zxid 比较收到选票的vote_id与自己的vote_id 如果收到选票的vote_id大于自己选票 将收到的选票的vote_id和vote_zxid改为这张选票并广播
  - 统计选票
    - 如果有半数节点同意了自己的选票 则结束投票
  - 更新服务器状态
    - 依据投票结果 更新状态
- 几种节点的场景
  - Follower重启
     - 进入LOOKING状态
     - 将投给自己的选票发送出去
     - Leader收到选票后将自己的状态和投给自己的选票发送给当前节点
     - Follower收到选票后将自己的状态和投给Leader的选票发送给当前节点
     - Follower发现集群有Leader 进入FOLLOWING状态
  - Leader重启
    - 进入LOOKING状态
    - 在旧Leader重启的过程中 别的节点已经完成了投票（可参照集群初次启动）
    - 旧Leader知晓集群中已经存在Leader 进入Follower状态（可参照Follower重启）
    - 旧Leader进入FOLLOWING状态
  - 集群初次启动
    - 所有节点都处于LOOKING状态 且所有的zxid都相同
    - 经过几轮投票后 必定会将myid最大的节点作为Leader
    - 投票胜出的节点进入LEADING状态
    - 剩余节点进入FOLLOWING状态
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