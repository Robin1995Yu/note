# JVM 篇

## 什么情况下会发生栈内存溢出。什么时候发生堆溢出？你是怎么排错的？
- 栈
  - 栈是线程私有的 和其生命周期相同
  - 每个方法执行的时候都会创建栈帧
    - 局部变量表
    - 操作数栈
    - 动态链接
    - 方法出口
    - 等
  - 出现溢出的原因
    - 栈太小
      - 通过-Xss调整大小
    - 递归问题
      - 没用结束的递归和过多无用的递归会造成这个问题
- 堆
  - 用于存放对象的区域
  - 过多的创建对象会造成溢出
  - 通过-Xmx调整堆大小
## JVM怎么判断对象是可回收对象？有哪些方法。
- 引用数算法
  - 已淘汰
  - 引用计数器为0认为是垃圾
  - 无法解决循环引用的问题
- 根可达算法
  - 从root开始的引用链
  - 如果不在引用链 认为是垃圾
  - 常见的root
    - 虚拟机栈中的引用变量
    - 方法区中静态属性
    - 方法区中常量引用堆对象
    - 本地方法栈中堆引用对象
## JVM的内存结构，新生代与老年代的比例，Eden和Survivor比例。
- 新生代 1/3
  - Eden 8/10
    - From Survivor 1/10
    - To Survivor 1/10
  - 为什么要有两个Survivor
    - 在一次MajorGC（对于Eden和一个Survivor）后 将一个Survivor的对象复制到另一个 然后对两个区域清理
- 老年代 2/3
- 清理过程
- 所有对象被创建时被放入新生代的Eden 大部分的对象都不需要长时间存活
- MajorGC是发生在新生代的GC
  - 如果Eden中的对象经历过一次GC 那么复制到Survivor中 并将age+1
  - 清理整个Eden区
  - 每经历一次GC age都要+1
  - 如果age超过一个值（默认15 可以通过-XX:MaxTenuringTreshold设置） 放入老年代
- 如果是一个较大的对象 以创建就会进入老年代
- FullGC 对于老年代的GC 不会和Major这么频繁
- 如果有较大对象但是没有可以容纳它的连续内存 就会提前GC
## 你知道哪几种垃圾收集器，各自的优缺点，重点讲下cms和G1，包括原理，流程，优缺点。
- 新生代的收集器
  - Serial
    - 串行化 停止所有别的线程直到GC结束
    - 依旧是客户端程序的默认GC
  - PraNew
    - Serial的多线程方式
  - Parallel Scavenge
    - 新生代的并行收集器
    - 减少STW的时间
- 老年代的收集器
  - Serial Old
    - Serial的老年版 和parallel scavenge配合使用
    - 作为CMS的后备方案
  - Parallel Old
      - 标记整理算法
  - CMS
    1. 初始标记 串行化
    2. 并发标记 并发
    3. 重新标记 串行化
    4. 并发清除 并发
    - 缺点
      - 无法标记浮动垃圾（线程交替产生的新的垃圾）
      - 清理的过程是并发的 会产生很多内存碎片 导致提前FullGC
- 回收整个Java堆(新生代和老年代)
  - G1收集器
    1. 初始标记 串行化
       - 标记直接可达的对象
    2. 并发标记 并发
       - 标记可达的对象
    3. 最终标记 串行化
       - 标记在并发阶段发生变化的对象
    4. 筛选回收 串行化
       - 通过回收价值和成本的排序 在所期待的时间内回收一部分

## 简单说说你了解的类加载器，可以打破双亲委派么，怎么打破。
- 类加载过程
  - Loading 加载
    - 通过类的完全限定名称查找字节码文件 通过字节码文件创建Class对象
  - Linking 链接
    - Verifcation 验证
      - 当前字节码是否符合JVM的要求 不会危害虚拟机安全
      - 文件格式验证
      - 元数据验证
      - 字节码验证
      - 符号引用验证
      - Preparation 准备
        - 给static变量分配内存并设置初始值
        - 如 public static int a = 5;会将a设置为0 赋值为5是初始化的时候做的事情
        - final变量在编译的时候就分配了
      - Resolution 解析
  - Initialization 初始化
    - 对只分配内存赋初始值的静态对象赋值 执行静态代码块 加载父类
- ClassLoader
  - Bootstrap 启动类加载器
    - 主要加载JVM需要的类
    - C++实现 是JVM的一部分
    - 加载/lib下的文件或-Xbootclasspath参数指定的路径下的jar包
    - 只会加载java，javax，sun开头的类
  - Extension 扩展类加载器
    - sun.misc.Launcher$ExtClassLoader
    - 负责加载/lib/ext或者-Djava.ext.dir参数指定的类
    - 开发者可以直接使用这个加载器
  - System 系统类加载器
    - sun.misc.Launcher$AppClassLoader
    - 负责加载classpath或者-Djava.class.path的类
    - 可以通过ClassLoad.getSystemClassLoad()得到
- 双亲委派机制
    - 除了顶层ClassLoader 别的所有ClassLoader都要有自己的父类加载器
        - 这里的父类不是通过继承 而是通过组合的方式来实现复用
    - ClassLoader收到类加载的请求 会将传递给父加载器 如果父加载器没发加载完成 才会自己加载
    - 目的
        - 类随着ClassLoader的层次而产生了层次和优先级
        - 父类加载过了的类子类就不会再加载一遍
        - 防止外部的类污染核心类库
- ClassLoader类图
    - abstract ClassLoader
        - SecureClassLoader
        - URLClassLoader
            - ExtClassLoader
            - AppClassLoader
- 主要方法
  - ClassLoader
    - loadClass(String className)
      - 在Java2之后不建议重写
      - 通过调用loadClass(String, boolean)
      - 逻辑
        - 从缓存中查找是否有对应的Class对象
          - 存在 直接返回
          - 不存在 下一步
        - 是否存在父加载器
          - 是 调用父加载器的loadClass方法
          - 否 调用bootstrap加载器加载
        - 是否找到
          - 是 返回
          - 否 调用findClass
      - findClass(String className)
        - 在Java2后不建议重写loadClass方法 取而代之的是重写findClass方法
        - 在loadClass方法如果父加载器没有加载 那么就会调用findClass来加载
        - 这样可以保证自定义的ClassLoader也符合双亲委派机制
        - 在ClassLoader中没有实现findClass的逻辑
      - defineClass(byte[] b, int off, int len)
        - 将byte子节流解析成JVM能识别的Class对象
          - 在ClassLoader中已经实现了这个功能
        - 字节流的来源很多 可以是文件 也可以是网络等
        - 一般是配合findClass方法来使用
        - 这个返回的Class并没有被解析
      - resolveClass(Class c)
        - 解析Class c
  - SercureClassLoader
    - 添加了关于源代码的位置与证书，对于class源码访问权限等的验证
  - URLClassLoader
    - 实现了ClassLoader中一些没有实现的方法的实现 如findClass
    - 新增URLClassPath类协助取得字节码流的操作
      - 通过传入构造器的参数URL[]判断路径是jar还是class文件
      - 返回FileLoader或者JarLoader
    - 如果不是什么十分复杂的功能 可以直接继承URLClassLoader
  - ExtClassLoader AppClassLoader
    - 是sun.misc.Lancher的内部类
    - 都继承自URLClassLoader
    - ExtClassLoader
      - 没有重写父类的loadClass
    - AppClassLoader
      - 重写了父类的loadClass 但是最后还是调用了父加载器的loadClass方法
- 类加载器之间的关系
  - 启动类加载器（BootStrap） C++实现 没有父加载器
  - ExtClassLoader 拓展类加载器 Java实现 父加载器为null
  - AppClassLoader 系统类加载器 Java实现 父加载器为ExtClassLoader
  - 自定义类加载器 父加载器为AppClassLoader
- 类与类加载器
  - Class对象相同的条件 名字一样（包括包名）
  - 类加载器一样
  - 不同的类加载器加载出来的Class对象会在不同的独立的命名空间中
    - 前提是重写loadClass方法
    - 由于loadClass的实现中 会先从缓存中查找 所以需要绕过这个才能重新加载
    - 或者直接调用findClass方法来绕过缓存查找
- 显示加载和隐式
  - 显示加载
    - 在代码中通过ClassLoader加载
  - 隐式加载
    - 不直接在代码中调用 而是通过JVM自动加载到内存中
- 自定义类加载器
  - 意义
    - class文件不在ClassPath路径中 默认的类加载器无法找到该类文件
    - 当一个文件是通过网络等方式传输且可能有加密操作时 需要先解密再加载到JVM中
    - 热部署
  - 自定义ClassLoader
    - 继承URLClassLoader就行
  - 热部署
    - 通过不同的ClassLoader的findClass方法来绕过缓存检查
  - 线程上下文类加载器
    - 通过Thread.getContextClassLoader()方法获得
    - 通过Thread.setContextClassLoader()方法设置
    - 默认为父线程的ClassLoader
    - 初始线程的类加载器是AppClassLoader
    - 应用
      - 在DriverManage中需要加载Connection的实现类 但是Bootstrap无法加载位于classpath的类 就委托给上下文类加载器来加载
## JVM内存为什么要分成新生代，老年代，持久代。新生代中为什么要分为Eden和Survivor。
- 新生代
  - Eden 新创建的对象会在这里（如果太大了去老年代） Eden内存不足触发一次GC
  - ServivorTo 保留一次GC中的幸存者
  - ServivorFrom 上一次GC代幸存者 作为这一次GC代被扫描者
  - MinorGC
    - 作用于新生代的GC算法
    1. 把Eden和ServivorFrom中的存活者复制到ServivorTo
    2. 把这些的年龄赋值为1（如果ServivorTo不够了 复制到老年代）
    3. 清空Eden和ServivorFrom
    4. 把ServivorTo复制到ServivorFrom 准备下一次GC
- 老年代
  - MajorGC
    - 在进行之前 一般先进行一次MinorGC
    - 如果从新生代放入老年代没有空间时会提前触发
    - 可能会产生内存碎片 需要进行合并
    - 如果老年代也装不下了 那么就OOM了
    1. 扫描一次老年代 标记存活
    2. 回收没有标记的
- 持久代
  - 永久保存的区域
  - 主要是Class和Mate
  - GC在程序运行时永远不会扫描永久代
  - 如果加载的Class过多会OOM
  - Java8取消了永久代 取而代之的是元数据区（元空间）
- 元数据区
  - 不存在JVM 在内存
  - 加载类不再由JVM内存大小限制
## JVM 出现 fullGC 很频繁，怎么去线上排查问题？
- full GC的原因
  - System.gc();
    - 建议执行full GC 但是不会立即执行
  - 执行jmap -histo:live pid
    - 会立即执行
  - 执行minor GC时的检查
    - 检查新生代的大小是否小于老年代的连续空间大小
      - 是 执行minor GC
      - 否 检查是否开启空间分配担保机制
        - 否 执行full GC
        - 是 检查历次minor GC晋升到老年代的大小是否小于老年代的连续空间
          - 是 执行minor GC 如果失败执行full GC
          - 否 执行full GC
  - 使用大对象 直接进入老年代 而老年代没有足够的连续空间
- 如何观察full GC 开启 -XX:+HeapDumpBeforeFullGC
- dump文件分析
## JVM中一次完整的GC流程是怎样的，对象如何晋升到老年代，说说你知道的几种主要的JVM参数。
1. 检查Eden和ServivorFrom中的对象 如果是根可达的 放入ServivorTo 并年龄+1
2. 清空Eden和ServivorFrom
3. 如果年龄大于15（默认值）放入老年代
4. 如果老年代没有连续空间 出发full GC
![image](https://static001.infoq.cn/resource/image/e6/69/e663bd3043c6b3465edc1e7313671d69.png)
## 垃圾回收算法的实现原理。
## JVM内存模型的相关知识了解多少，比如重排序，内存屏障，happen-before，主内存，工作内存等。
示例代码如下
```
int a = 10;     // 1
int b = 10;     // 2
int c = a + b;  // 3
```
- 重排序
  - 在不影响运行结果的情况下 允许对指令重排
  - as-if-serial原则
    - 在单线程的场景下 无论如何排序 都不会影响最终结果
    - 如果不存在数据依赖 允许重排序
    - 依赖关系
      - 3 依赖于 1
      - 3 依赖于 2
      - 1 2 之间互不依赖
    - 所以以上代码的执行顺序可以为123或213
  - 在单线程下 观测到的结果永远和顺序执行相同 所以程序员不会察觉到重排序
- 内存屏障
  - 完全内存屏障 早于屏障的读取操作的结果提交到内存后再执行晚于屏障的读写操作
  - 内存读屏障 仅保证了读操作
  - 内存写屏障 仅保证了写操作
- happen-before
  - 从Java1.5开始用于阐述多线程之间内存可见性的问题
  - 定义
    - 如果第一个操作happen-before第二个操作 那么第一个操作的结果对于第二个是可见的 第一个操作发生在第二个操作之前
    - 在运行时不一定会严格按照happen-before的顺序 如果重排序之后的结果和happen-before的结果一样 那么这种重排序就是合法的
  - 原则
    - 程序次序规则
    - 锁定规则
    - volatile变量规则
    - 传递规则
    - 线程启动规则
    - 线程中断规则
    - 线程终结规则
    - 对象终结规则
- Java内存模型
  - 目标 定义程序中各个变量的访问规则
  - 主内存
    - 所有的变量都保存在主内存中
  - 工作内存
    - 每条线程私有的 保存着每条线程的变量从主内存中的拷贝副本（对象拷贝指针）
    - 线程只能操作工作内存 不能操作主内存
    - 线程之间的变量值的通讯都要通过主内存
  - 线程之间的通信
    - 由JMM控制
    - 线程之间的共享变量存储在主内存
    - 每个线程都有一个私有的本地内存
    - 内存中的交互协议
      - lock 锁定
        - 作用于主内存 让一个变量标识变为一个线程独占
      - unlock 解锁
        - 作用于主内存 把一个锁定的变量释放
      - load 载入
        - 作用于工作内存 将主内存的变量值放到工作内存中
      - use 使用
        - 作用于工作内存 将工作内存中的数据传递给工作引擎
      - assign 赋值
        - 作用于工作内存 从工作引擎中赋值到工作内存
      - store 存储
        - 作用于工作内存 将工作内存中的数据传递到主内存
      - write 写入
        - 作用于主内存 将store到数据传送到主内存
## 说一下Java对象的创建过程
1. 分配内存 存放实例变量 从父类继承来的变量（被隐藏的也会被分配空间）并为他们附上初始值
2. 实例变量的初始化和静态代码块
   - Java按照顺序来执行初始化 不允许在前面的代码初始化后面的变量
3. 构造函数初始化
## 你们线上应用的JVM参数配置了哪些。
## G1和cms区别。
- cms
  - Concurrent Mark Sweep
  - 以最短STW时间为目标的GC 通过和用户线程并行来实现的
  - 仅作用于老年代的GC
  - 将耗时长的根可达标记通过并发来执行 将只能在STW执行的操作在STW的时候操作
  - 优点 并发收集 低停顿
  - 缺点
    - 对CPU资源非常敏感
    - 无法处理浮动垃圾
    - 标记清除算法的缺点（会产生内存碎片）
  - 主要分为四个过程
    1. 初始标记
       1. STW
       2. 标记根直接可达的对象
    2. 并发标记
       1. 不需要STW
       2. 根可达标记
    3. 重新标记
       1. STW
       2. 修正因为用户线程的执行而变化的对象
    4. 并发清除
  - 补充 安全点
    - 在一段代码中引用关系不会发生变化被成为安全点
    - 抢先式中断
      - GC将所有线程中断 如果发现某个线程不在安全点上 让它执行到安全点为之
      - 几乎没有在用来
    - 主动式中断
      - GC对所有线程标志
      - 线程主动轮询 发现标志就中断（轮询点和安全点都是重合点）
- G1
  - 重新定义了对内存 分块 收集时不必对全堆范围内操作
  - 停顿的时间是可预期的 用户可以指定GC操作在多长时间内完成
  - G1和CMS的区别
    - 都是并发和分代的
    - G1可以最大化释放堆内存
    - 都是低延迟
    - G1的吞吐量大于CMS
    - G1可以压实
    - G1的可预测性强
    - G1新生代和老年代不分离 CMS分离
  - Region G1将堆内存划分成多个大小相等的独立区域
    - 大小在1M到32M之间的一个2的幂
    - 会尽量划分成2048个同等大小的Region
  - G1的可预测停顿时间
    - 有计划的避免整个堆的GC
    - 通过计算模型计算每个Region的回收时间和成本
  - 过程
    1. 初始标记
       - STW
       - 标记GC Root直接可达的对象
    2. 并发标记
       - 并发
       - 标记根可达的对象
    3. 最终标记
       - 对并发标记时用户引用的变化更新
       - 需要停顿线程 但是可以并发执行
    4. 筛选回收
       - 对每个Region的回收成本排序
       - 根据用户期望的GC时间指定回收计划
       - 可以并发完成
## 怎么打出线程栈信息。
## 说一下类加载的执行过程
## JVM垃圾回收机制，何时触发MinorGC等操作呢？
Eden区不足以存放新来的实例
## ZGC 垃圾收集器，了解过吗
- JDK11推出的低延迟GC
  - 停顿时间不超过10ms
  - 停顿时间不会随着堆大小而改变
  - 支持8MB～4TB的堆（未来支持16TB）
- GC之痛
  - STW时间会影响系统的反馈
  - GMS与G1停顿时间的瓶颈
    - CMS的YoungGC，G1，ZGC都基于标记-复制算法
      - 标记阶段 从GC Root开始标记活跃的对象
        - 初始标记 标记GC Root直接可达的对象 STW 但是时间很短
        - 并发标记 通过初始标记找出存活对象 并发 时间较长
        - 再标记 重新标记并发阶段变化的对象 STW
      - 转移阶段 将活跃的复制到新的内存地址中
        - 清点出有存活对象的分区和没有存活对象的分区 不存在复制和清除 STW
        - 为将要复制的对象分配内存（耗时较短） 然后复制所有存活的对象（耗时与对象的复杂度有关 比较耗时）
      - 重定位阶段 将栈中指向这些被转移的对象的旧地址调整到新地址
    - 标记-复制的瓶颈在复制阶段
- ZGC原理
  - 采用标记-复制算法
  - 将部分功能并发完成
  - ZGC的STW阶段
    - 初始标记
      - 标记GC Root直接可达的对象 时间较短
      - 处理时间和堆内存大小无关 只和GC Root数量有关
    - 再标记
      - STW时间最多1ms
      - 如果超过来1ms 那么就再次进入并发标记
    - 初始转移
  - ZGC技术关键
    - 通过着色指针和读屏障实现并发转移
    - 为什么别的GC在转移时要STW
      - 如果转移了只后尚未更新指针 而这时线程访问这个指针 就会发生错误
      - ZGC通过读屏障
        - 如果发现是转移过的对象 读屏障会先将读出来的指针更新到对象的新地址上
    - 着色指针
      - 一种将信息存储在指针中的技术
      - ZGC只支持64位系统
      - ZGC将64位的虚拟地址空间划分为多个子空间
        - 虚拟地址空间
          - 值CPU可以发出的地址数量
          - 例如 邮编有6位十进制 有10^6个虚拟空间 但是不代表着需要这么多实际空间和他匹配
        - 分配方式
          - 0 ~4 TB 对应Java堆
          - 4 ~8 TB M0地址空间
          - 8 ~12TB M1地址空间
          - 12~16TB 预留
          - 16~20TB Remapped空间
        - 在应用程序对象创建时 在堆空间中申请一个虚拟地址 这个虚拟地址不会映射到实际的地址空间 同时在M0 M1 Remapping上也申请一个地址 这三个地址映射到同一个物理地址
        - 三个地址在同一时间只有一个有效
        - 将存活信息放在地址中 和传统的放在对象头中完全不同
      - 读屏障
        - JVM向应用代码插入一小段代码的技术 在应用程序从堆中读取对象引用时会执行这段代码（只有从堆读取对象引用时才会触发）
      - 并发处理演示
        - 初始化
          - 在ZGC初始化之后 整个内存空间设置为Remapped 满足条件后开始GC
        - 并发标记阶段
          - 将活跃的标记为M0
          - 此时每个对象只会有两种状态 M0 Remapped 如果是M0 活跃 如果是Remapped 那么不活跃
        - 并发转移阶段
          - 如果被转移或者被访问过就标记为Remapped
## 对象的访问定位有哪两种方式?
## 说一下 jvm 调优的工具？
## 对象什么时候会进入老年代？
- 在新生代存活过了15轮后（可以在JVM参数里设置）
- 对象过大 无法放入新生代
- -XX:MaxTenuringThreshold参数设置
## 内存泄漏和内存溢出区别？
- 内存泄漏
  - 无法到达且无法被回收的对象占用内存
- 内存溢出
  - 在老年代中GC后还没有足够空间存放对象会发生OOM
## 什么是tomcat类加载机制？
- 是违反双亲委派机制的
## 了解逃逸分析技术吗
Escape Analysis
- 逃逸状态
  - 全局逃逸
    - 一个对象的作用范围逃出了当前方法或线程
    - 发生情况
      - 对象是一个静态变量
      - 对象是一个已经发生逃逸的对象
      - 对象作为返回值
  - 参数逃逸
    - 对象被作为参数传递如方法 这种状态下不会发生全局逃逸
  - 没有逃逸
- 如果判断出来一个对象是不逃逸的 那么就进行以下的优化
- 逃逸分析优化
  - 锁消除
    - 在单线程的情况下就移除该对象的同步锁
    - JVM是默认开启锁消除的（前提是开启逃逸分析）
  - 标量替换
    - 如果判定一个对象是非逃逸的 那么就把它替换成几个标量
    - 不能被进一步分解的就是标量
      - 标量
        - 基础类型
        - 对象的引用
      - 非标量
        - 对象
  - 对象和数组有可能分配在栈中 用完就丢弃 可以减少GC和堆内存堆压力
## 调用System.gc()会发生什么?
  如果设置了禁用 那么什么都不会发生（默认）

  如果是允许 那么就会尽量调用fullGC
## 谈谈Minor GC条件，full GC条件
- Minor GC
  - 当Eden区满了触发
- full GC
  - 调用了System.gc()
  - 老年代空间不足
  - Minor GC之前触发full GC
## Stop The World 了解过吗？
在GC的时候 有些过程是无法和用户线程并发的 这个时候需要将用户线程全都停止 被称为Stop The World（STW）

在标记-复制算法的GC中 一般会在初始标记 最终标记和复制的时候STW（ZGC在复制的时候是并行的）

触发STW的时候最初是GC强制让所有的线程停止 逐一判断线程是否在安全点 如果不在安全点 会唤醒线程让它允许到安全点为止

之后GC会在用户线程上打上中断标记 线程会轮询自己的标记 如果发现有中断标记 那么会主动中断（轮询点和安全点是重合的）
## 谈谈你认识多少种OOM？如何避免OOM?
- OutOfMemeryError: Java Heap space
  - 堆溢出
  - 尝试放入比堆大的对象（虚拟机内存大小设置不合理）
  - 内存泄漏
- OutOfMemeryError: GC overhead limit exceeded
  - 如果GC时间超过总时间的98%且在GC后仅恢复了2%的堆时触发
  - 是一种快速失败 当之后GC达到100时会无法完成用户的应用
- OutOfMemoryError: PermGen space
  - 永久代空间满
  - 可以在JVM参数中允许卸载长时间不用的永久代对象（GC会作用在永久代上）
  - 在Java8之后放弃了永久代 用元数据区替代
- OutOfMemoryError: Matespace
  - 元数据区溢出
  - 在JVM参数中增加元数据区的大小
- OutOfMemoryError: Unable to create new native thread
  - 无法创建更多线程
- OutOfMemoryError: Out of swap space
  - 系统配置的交换空间不足
  - 操作系统中的另一个线程在消耗所有的内存
- OutOfMemoryError: Requested array size exceeds VM limit
  - 数组分配的大小超过虚拟机的限制
  - 最大可以分配Integer.MAX_VALUE - 2长度的数组
- OutOfMemoryError: Kill process or sacrifice child
  - 操作系统级别有一个Out of memory killer
  - 操作系统会在内存不足的时候主动杀死评分最低的线程
## 了解过JVM调优没，基本思路是什么?如何确定它们的大小呢？
- 何时考虑JVM调优
  - 老年代达到设置的最大内存
  - FullGC频繁
  - GC停顿时间过长（1s以上）
  - 出现OOM
  - 应用中有使用本地缓存且占用大量内存空间
  - 系统吞吐量与响应性能不高或下降
- 调优原则
  - 大多数的Java应用不需要JVM调优
  - 大多数GC问题是代码层面导致的
  - 上线之前 先考虑机器的JVM参数配置
  - 减少对象创建
  - 减少使用全局变量和大对象
  - 优先架构和代码调优 JVM调优是不得已的情况
  - 通过GC情况优化代码比优化JVM效果好
- 调优量化目标
  - 堆内存使用率<=70%
  - 老年代<=70%
  - GC平均停顿时间<=1s
  - full GC次数为0或者平均停顿间隔>=24h
- 参数
  - 不稳定参数
    - 以-XX开头的参数 由于它们的变化会大大影响JVM性能 所以被称为不稳定参数
    - 布尔型参数
      - -XX:+ 表示开启
      - -XX:- 表示关闭
    - 数字类型参数
      - -XX:= 给定一个数字类型值 可以跟单位k/K/m/M/g/G都是可以的
    - 字符串型参数
      - -XX:= 给定一个字符串型参数 通常指定文件 路径 一系列命令列表
  - 具体参数
    - -Xmx4g 最大堆内存4g
    - -Xms4g 堆初始化大小4g
    - -Xmn1200m 年轻代的大小
      - 年轻代过大会挤压老年代 官方的建议是年轻代占总堆内存堆3/8
    - -Xss512k 每条线程的堆栈大小
      - JDK5.0后默认是1M 之前是256K
      - 在相同的物理内存下设置的越小可以的线程越多
      - 但是操作系统对线程是有限制的 一般是3000～5000
    - -XX:NewRatio=4 年轻代和老年代的比值 4表示1:4 年轻代占总的1/5
    - -XX:SurvivorRatio=8 Eden和Servivor的比值 8表示1:8（这里是一个Servivor的比值 年轻代有两个Servivor）
    - -XX:MaxTenuringThreshold=15 放入老年代的最大年龄
      - 如果是0 在老年代比较多的系统中比较适用
      - 如果设置的比较大 那么会在Servivor中频繁复制 但是可以增加在年轻代回收的概率
## 淘宝热门商品信息在JVM哪个内存区域
## 字节码的编译过程
- javac 前端编译器
  - 词法 语法分许
    - 词法分析 将字符流转化为标记（Token）集合
      - Token 标记 在语言中一个不可拆分的字符串 如int 变量名等
    - 语法分析 通过词法分析得到的Token集合构造抽象语法树
## Java需要开发人员回收内存垃圾吗？
## Java中垃圾回收有什么目的？什么时候进行垃圾回收？
## System.gc()和Runtime.gc()会做什么事情？
## 主内存与工作内存
## 内存间交互操作
## volatile 禁止内存重排序
## 内存模型三大特性
## 谈谈先行发生原则
## JVM 堆内存溢出后，其他线程是否可继续工作？
## 说一下JVM 常用参数有哪些？
## VM 为什么使用元空间替换了永久代？
## Java堆的结构是什么样子的？什么是堆中的永久代(Perm Gen space)?
## JVM的永久代中会发生垃圾回收么？
## 什么是字节码？采用字节码的最大好处是什么？什么Java是虚拟机？
## MinorGC 的过程
## CPU 占用过高如何分析
## Serial与Parallel GC之间的不同之处？
## WeakHashMap 是怎么工作的？
## 解释 Java 堆空间及 GC？
## 你能保证 GC 执行吗？
## JVM中哪个参数是用来控制线程的栈堆栈小的?