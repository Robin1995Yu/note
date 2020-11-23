## BIO、NIO和AIO区别
- BIO
  - 最传统的IO模式
  - 在收到消息或者没有新的消息的场景下会阻塞
  - 每个IO都需要一个单独的线程维护
- NIO
  - 相比于BIO 在没有新的消息的时候是不会阻塞的
  - 是面向缓冲区的IO模式
  - 一个线程可以维护多个IO
说一下Netty 的各大组件
Netty 线程模型和 Reactor 模式
什么是 Netty 的零拷贝？
NIO 的底层实现。
netty的心跳处理在弱网下怎么办
Netty 高性能表现在哪些方面？
Netty 和 Tomcat 有什么区别？
Netty 发送消息有几种方式？
默认情况 Netty 起多少线程？何时启动？
Netty 支持哪些心跳类型设置？
Java 中怎么创建 ByteBuffer
Java 中的内存映射缓存区是什么？
简单讲讲tomcat结构，以及其类加载器流程，线程模型等
tomcat如何调优，涉及哪些参数
IO多路复用机制
Netty 的应用场景有哪些？
有几种I/O 网络模型？
说说Netty的执行流程？
select、poll、epoll的机制及其区别？