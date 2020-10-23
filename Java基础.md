# Java 基础
## equals与==的区别
    equals比较值（需要重写）==比较内存中是否是同一对象
## final, finally, finalize 的区别
    final 类不可继承 方法不可重写 成员不可修改 变量不可修改
## 重载和重写的区别
    重载 方法名相同 参数列表不同
    重写 重写父类的方法
## 两个对象的hashCode()相同，则 equals()是否也一定为 true？
    不
## 抽象类和接口有什么区别
    这个我懂的
## BIO、NIO、AIO 有什么区别？
- 同步阻塞 一直等待到完成任务 对应BIO
    - 传统的socket方式 通过accept方法来等待接收 这就意味着一个客户端就需要一个线程来完成这些任务
    - 伪异步BIO 通过线程池来管理一系列的连接 客户端的数量为M 线程池的大小为N M可以远大于N
    - 在并发量在1000以下的情况下可以使用这种方式来完成
- 同步非阻塞 不等待任务完成 但是需要一直去看是否完成 对应NIO
    - NIO的特性/IO和NIO的区别
        - IO是阻塞的 NIO是非阻塞的 Java中的IO都是阻塞的
        - IO是面向流的（Stream） NIO是面向缓冲区的（Buffer）
        - NIO是通过Channel来读写的
        - NIO有Selector IO没有 选择器用于一个线程处理多个通道
            - 通道数据读取：创建一个缓冲区 从通道请求数据读取
            - 通道数据写入：创建一个缓冲区 写入数据 请求通道写入
    - 在Java1.4引入 对应java.nio包 抽象为Channel Selector Buffer
    - Channle 可以使用阻塞和非阻塞两种方式 阻塞方式和传统NIO一样 简单 但是可靠性和并发都不好
- 异步非阻塞 不等待任务完成 任务完成的时候会有通知
## String，StringBuffer，StringBuilder的区别
    String          字符串类 静态类 在修改的时候效率很低
    StringBuffer    同步的 修改字符串的效率高
    StringBuilder   非同步的 修改字符串的效率高
    效率 StringBuilder > StringBuffer > String
## JAVA中的几种基本数据类型是什么，各自占用多少字节呢
    类型    | 大小
    ---     | ---
    byte    | 1
    short   | 2
    int     | 4
    long    | 8
    float   | 4
    double  | 8
    boolean | 2
    char    | 2

## Comparator与Comparable有什么区别？
- Comparator 比较器
    - 用于比较两个对象的值
    - 必须实现compare方法
    - 提供了一些default方法来生成一些默认的比较器
- Compatable 可比较的
    - 用于修饰类 表明被修饰的类是可比较的
    - 必须实现compareTo方法
## String类能被继承吗，为什么。
    不可以 因为是被final修饰的
## 说说Java中多态的实现原理
- 编译时多态（静态多态）
    - 重载 Override是编译时多态 在编译时就确定了
- 运行时多态（动态多态）
    - 大多数情况下都是运行时多态 编译时不确定到底是调用哪个方法 一直延迟到运行时才确定 所以多态方法也称为延迟方法
- 如果要使用多态 要在定义类型的时候使用父类（父接口）来定义变量 参数传入也要使用这些
- 多态的实现
    - 在执行字节码的时候 会将类型信息放入到方法区中 为了优化调用速度 会在方法区中的类型信息上加上指针 指向该类型的方法表
    - 方法表
        - 方法表指都是一个类的方法列表 从前到后存储Object的方法 父类的方法 自己的方法（私有方法和静态方法不会出现在这里）
        - 重写的方法会覆盖掉父类的方法
        - 由于使用了Object-父类-子类的方式排列方法 所以方法的偏移量是相同的
        - 当使用接口方法时 就只能一个个找了 所以接口的延迟方法是比类的延迟方法要慢的
## Java泛型和类型擦除
    Java的泛型是伪泛型 是在编译器级别实现的 字节码中是不存在泛型的
    在编译的时候去除泛型 称之为类型擦除

```
ArrayList<String> arrayList1 = new ArrayList<String>();
arrayList1.add("abc");
ArrayList<Integer> arrayList2 = new ArrayList<Integer>();
arrayList2.add(123);
System.out.println(arrayList1.getClass() == arrayList2.getClass());
```
    以上代码返回的是true 说明类型String和Integer都被擦除了
```
ArrayList<Integer> arrayList3=new ArrayList<Integer>();
arrayList3.add(1);
arrayList3.getClass().getMethod("add", Object.class).invoke(arrayList3, "asd");
for (int i = 0; i < arrayList3.size(); i++) {
	System.out.println(arrayList3.get(i));
}
```
    使用反射的方式获得add方法时可以添加String类型 说明类型已经擦除了

- 原始类型
    - 擦除了泛型后的类型 如TestClass<T>的原始类型是TestClass
- 限定类型
    - TestClass<T extends Interface1 & Interface2>的限定类型是Interface1
        - 会在必要的时候添加强制转换Interface2
        - 为了提高效率 将标签接口（没有方法的接口）放在后面减少强转次数
        - 如果没有限定类型 那么用Object


## int和Integer 有什么区别，还有Integer缓存的实现
    int是基础类型 Integer是包装类型
## 说说反射的用途及实现原理，Java获取反射的三种方法
- 反射提供以下功能
    - 在运行时判断一个对象所属的类
    - 运行时构造任意一个对象
    - 运行时判断一个类所包含的成员以及方法（可以调用私有方法）
    - 运行时调用任意一个对象的方法
- 主要用途
    - 开发框架
- 反射功能的实现
    - 获取Class对象
        - Class.forName("Test");
        - Test.getClass();
        - new Test().getClass();
        - 由于在一个JVM实例中一个类只有一个Class对象 所以以上三种方式获取的是同一个Class
    - 判断是否为某个类的实例
    - 创建实例
        - 区别于传统的new 先需要通过Class实例获取构造器来获取对象
        - Class<Test> clazz = Test.getName(); // 获取Class实例
        - Constructor con = clazz.getConstructor(形参.class); // 获取构造器实例
        - Test test = con.newInstance(参数); // 获取实例
    - 获取方法
    - 获取构造器
    - 获取成员变量的信息
        - Field f = clazz.getField("属性名");
        - f.set(ins, value); // 设置属性
    - 调用方法
    - 利用反射创建数组
## 面向对象的特征
- 三大特性
    - 封装
        - 将现实事物封装成类 只让可信的类或对象操作 对不可信的类或对象隐藏
    - 继承
        - 实现继承 可以得到父类的属性和方法而无需编写额外代码
        - 接口继承 仅使用属性和方法的名称且必须实现功能
        - 可视继承 子类可以使用父类的功能来实现新的功能
    - 多态
        - 允许子类赋值给父类的指针 调用方法实现子类自己的功能
        - 覆盖 子类重新定义父类方法
- 五大原则
    - 单一职责原则SRP(Single Responsibility Principle)
        - 类的功能单一 只实现自己的功能
    - 开放封闭原则OCP(Open－Close Principle)
        - 扩展性方面应该是开放的
        - 改进性能方面是封闭的
    - 替换原则(the Liskov Substitution Principle LSP)
        - 子类应该可以出现在父类应该出现的场合替代父类的功能
    - 依赖原则(the Dependency Inversion Principle DIP)
        - 如果B依赖于A的功能 需要B定义接口 A实现接口
        - 解除AB之间的直接依赖
        - 上下层的变动不会影响到对方
    - 接口分离原则(the Interface Segregation Principle ISP)
        - 模块间通过接口隔离开来 而不应该直接是类而耦合
## &和&&的区别
- & 位运算符
    - 0 & 0 = 0
    - 0 & 1 = 0
    - 1 & 0 = 0
    - 1 & 1 = 1
- && 逻辑运算符
    - false && false = false
    - false && true  = false
    - true  && false = false
    - true  && true  = true
## Java中IO流分为几种?
- 输入流
    - 将数据读入到内存的流
    - InputStream
    - Reader
    - 主要方法
        - int read()
            - 读取单个位/字符 返回字节/字符（字节和字符都可以转换为int）
            - 这里读取的返回值是转化为int的无符号byte或char
            - 如果返回-1表示没有数据了
        - int read(byte[]/char[] buf)
            - 读取buf.length个字节/字符 返回实际读取的字符数
        - int read(byte[]/char[] buf, int off, int len)
            - 读取len个字节/字符 从off开始存储数据 返回实际读取的字节/字符数量
- 输出流
    - 将内存数据输出
    - OutputStream
    - Writer
    - 主要方法
        - void write(int c)
            - 将一个无符号的byte/char（转换为int）写入流
        - void write(byte[]/char[] buf)
            - 将buf写入流
        - void write(byte[]/char[] buf, int off, int len)
            - 从buf的off开始写入len字节/字符到流
- 字节流和字符流的区别
    - 操作单元不同
        - 字节流操作8位的字节
        - 字符流操作16位的字符
- 结点流和处理流
    - 结点流 构造参数 IO物理结点
    - 包装流 构造参数 已存在的流
        - 包装流隐藏了底层结点流的差异提供了额外的功能和更方便的操作
        - 程序直接操作包装流 底层还是结点流
        - 关闭的时候只要关闭包装流 那么它对应的结点流也会关闭
        - 常见包装流
            - 缓冲流 Buffered*
                - 加入缓存 加快速度
                - 先将数据读入 放入缓存 等待读取
                - 外部操作还是和通常操作一样 默认缓冲区的大小为8192
            - 转换流
                - InputStreamReader/OutputStreamWriter
                - 属于字符流
                - 将字节流转换为对应的字符流
                - FileReader和FileWriter是它们的子类
            - 内存流
                - ByteArrayInputStream/ByteArrayOutputStream
                - CharArrayReader/CharArrayWriter
                - StringReader/StringWriter
                - 这些流是在内存中的所以不存在关闭一说（close方法在它们的实现是一个空函数 如果调用了之后还是可以使用这些流的）
            - 合并流
                - SequenceInputStreamextends
                - 将两个输入流合并 一个读完了读下一个
            - 对象流
                - ObjectInputStream/ObjectOutputStream
                - 对象的输入输出流
                - readObject(Object)/writeObject(Object)
                - 输入/输出的对象要实现Serializable接口
                - 如果有不想被序列化的字段 在字段上加上transient关键字
                - 用途
                    - 网络传输 将对象序列化后可以在网络上传输
                    - 服务器钝化 如果某些对象好久没有活动了 将之序列化后放入文件系统后待之后使用
                    - 深拷贝 可以通过内存流作为中转实现深拷贝
            - 随机访问流
                - RandomAccessFile
                    - File f, String mode
                    - String path, String mode
                        - mode
                            - r     只读 调用write会报IOException
                            - rw    读写 如果文件不存在 尝试创建
                            - rws   每次write修改后都会同步到磁盘中
                            - rwd   在close时才会将数据同步到磁盘中
                            - 关于为什么不用枚举 因为这个类出现在枚举之前 历史遗留问题了
                    - long getFilePointer() 获取当前偏移量
                    - void seek(long) 设置偏移量
                    - 多线程下载 断点下载都可以用这个
## 讲讲类的实例化顺序，比如父类静态数据，构造函数，子类静态数据，构造函数。
1. 父类静态代码块
2. 子类静态代码块
3. 父类成员变量初始化
4. 父类代码块
5. 父类构造函数
6. 子类成员变量初始化
7. 子类静态代码块
8. 子类构造函数
## Java创建对象有几种方式
1. 继承Thread类 重写run方法
2. 实现Runnable接口 重写run方法 将之作为参数创建Thread实例
3. 实现Callable接口
## 如何将GB2312编码的字符串转换为ISO-8859-1编码的字符串呢？
## 守护线程是什么？用什么方法实现守护线程
- 如何开启守护线程
    - 创建Thread 调用setDaemon(true)将线程变为守护线程
    - 开启线程
- 注意点
    - 在需要在开启线程之前将之设置为守护线程 否则会抛出IllegalThreadStateException
    - 守护线程创建的线程都是守护线程
    - 守护线程不能访问固有资源（读写IO或逻辑） 因为它可能在任何时候中断
    - 不能使用线程池 如果守护线程放入了线程池 它会变为用户线程

## notify()和 notifyAll()有什么区别？
- 都是Object的方法 在类作为锁的时候使用
- 都可以唤醒wait中的线程
- notify唤醒一个wait中的线程（具体是哪个就看重装系统调度的实现了）
- notifyAll唤醒所有wait中的线程
- 唤醒后如果没有竞争到锁也无法马上执行

## Java语言是如何处理异常的，关键字throws、throw、try、catch、finally怎么使用？
- throws  抛出声明 在方法上使用 声明这个方法会抛出的异常
- throw   抛出异常 一般使用throw new Exception();来使用
- try     检测异常的代码块 后面必须跟着catch或者finally或两者
- catch   捕捉异常 捕捉对应的异常和处理
- finally 不论上面的模块是否发生异常 都会执行的代码

## 谈谈Java的异常层次结构
- Throwable（interface）
    - Error
        1. 可以恢复但是十分困难 比如操作系统底层的问题 几乎不可能指望修改程序来解决
        2. 同RuntimeException一样 没有用throws和try-catch检查也可以编译通过
    - Exception
        - CheckedException
            1. 被检查的异常
            2. 包括Exception和其非RuntimeException都是
            3. 必须使用throws和try-catch处理才能编译通过
        - RuntimeException
            1. 运行时异常
            2. 编译器不会检查 即没有throws和try-catch也可以编译通过
            3. 如果使用throws和try-catch也是可以的
            4. 如果产生 那么需要通过修改代码来修复

## 静态内部类与非静态内部类有什么区别
- 内部类可以访问类的所有成员 包括外部类的私有成员
- 非静态内部类
    - 必须要依赖于外部类的实例创建
    - 不能定义static元素
- 静态内部类
    - 如果一个内部类是静态的 那么就升级为顶级类
    - 静态内部类有所有顶级类的特性
- 内部接口
    - 内部接口不论是否声明静态 都是静态的

## String s与new String与有什么区别
- new String必定会创建一个新的字符串对象
- String s看情况
    - String s = "s";
        - 如果堆内存中有这个String 那么不会重新创建
    - String a = "a";
    - String b = "b";
    - String c = "ab";
    - String s = a + b;
        - s != c
    - final String a = "a";
    - final String b = "b";
    - String c = "ab";
    - String s = a + b;
        - s == c

## 反射中，Class.forName和ClassLoader的区别
- class加载到JVM的过程
1. 装载 loading 找到class对应的文件
2. 连接 linking将字节码文件读入到JVM
3. 初始化initializing 对class做相应的操作
- Java中加载class到JVM中的两种方式
1. Class.forName("className")
    - 实际是使用Class.forName(className, true, ClassLoader.getCallerClassLoader())
    1. className 需要加载的类名
    2. true 是否需要初始化
    3. classLoader 需要使用的ClassLoader
2. ClassLoad.loadClass("className")
    - 实际是调用ClassLoad.loadClass(className, false)
    1. className 需要加载的类名
    2. 这个类是否需要去连接
- 区别
    - Class.forName("className")
        - 已经初始化完成
    - ClassLoad.loadClass("className")
        - 得到class但是还为连接
    - 一般情况下 这两个是都可以使用的
    - 如果依赖于Class是否被初始化 那么要用Class.forName("className")

## JDK动态代理与cglib实现的区别
- JDK动态代理
    - 被代理的类必须实现接口
    - 代理实现方式
        1. 实现InvocationHandler接口
        2. 添加成员变量Object 用于存储被代理的对象
        3. 重写invoke方法
        4. 通过Proxy.newProxyInstance()方法获取代理对象（是接口的实现类）
            - ClassLoader 被代理的对象的类加载器（target.getClass().getClassLoader()）
            - Class[] 被代理类的接口列表（target.getClass().getInterface()）
            - InvocationHandler 自己实现的InvocationHandler

## error和exception的区别，CheckedException，RuntimeException的区别。
- Throwable（interface）
    - Error
        1. 可以恢复但是十分困难 比如操作系统底层的问题 几乎不可能指望修改程序来解决
        2. 同RuntimeException一样 没有用throws和try-catch检查也可以编译通过
    - Exception
        - CheckedException
            1. 被检查的异常
            2. 包括Exception和其非RuntimeException都是
            3. 必须使用throws和try-catch处理才能编译通过
        - RuntimeException
            1. 运行时异常
            2. 编译器不会检查 即没有throws和try-catch也可以编译通过
            3. 如果使用throws和try-catch也是可以的
            4. 如果产生 那么需要通过修改代码来修复

## 深拷贝和浅拷贝区别
- 浅拷贝
    - 通过Object.clone()方法来实现
    - 被拷贝的对象的类必须实现Cloneable接口
    - 拷贝了一个新的对象
    - 对象中的引用还是原来的引用
- 深拷贝
    - 与浅拷贝不同 类中的引用也会被一起拷贝
    - 实现方式
        - 通过重写clone方法实现
            - 缺点 需要一层层的重写clone 代码量很大 也很傻逼
        - 通过序列化和反序列化
            - 所有的被引用的对象（包括引用的饮用都需要实现Serializable接口）
            - 用ObjectOutputStream包装ByteArrayOutputStream
            - 将对象放到输出流
            - 用ObjectInputStream包装ByteArrayInputStream
                - 参数为ByteArrayOutputStream.toByteArray()
            - 调用ObjectInputStream.readObject得到拷贝后的对象

## JDK 和 JRE 有什么区别？
- JRE Java Runtime Environment
    - Java运行时环境
    - 主要有两个文件夹
        - bin 运行时二进制文件 可以认为就是JVM
        - lib JVM运行时需要调用到的类库
- JDK Java Development Kit
    - Java开发工具包
    - 主要四个文件夹
        - bin 开发时工具 包括javac等编译相关
        - include java和JVM交互的头文件
        - lib 类库
        - jre java运行环境
- 包含关系：JDK包含JRE包含JVM

## String 类的常用方法都有那些呢？

## 谈谈自定义注解的场景及实现
- 如何自定义注解
    - 注解相关的内容在java.lang.annotation包中
    - 反射的基本类Class Method Field Constructor的父类AnnotatedElement中提供了一系列可以获得其本身的注解的方法可以获取标记在其之上的注解
    - 常用元注解
        - @Treget     标记修饰的对象范围 参数是枚举类ElementPolicy的数组
        - @Retention  标记注解的保留时间长短 参数是枚举类RetentionPolcy
            - SOURCE   在源码中有效 编译后被忽略
            - CLASS    会编译到class文件中 在运行时忽略
            - RUNNTIME 在运行时有效
            - 只有需要通过反射得到 那么必须设置为RUNNTIME
        - @Inherited  标记该注解具有继承性 被修饰的类的子类也会有这个注解
        - @Repeatable 允许一个元素上有多个注解
    - 注解属性类型
        - 基本数据类型
        - String
        - 枚举类型
        - 注解类型
        - Class类型
        - 以上类型的数组

## 说说你熟悉的设计模式有哪些？
- 单例模式
    - 全局只有一个实例
    - 实现方式
        - 双重检查模式
            1. 私有化构造器
            2. 添加自身的私有静态成员（volatile修饰）
            3. 双重检查
                1. 如果成员为空 获取锁
                2. 获取锁后判断是否为空
                3. 为空 赋值 退出临界区
                4. 返回单例
        - 静态内部类
            - 创建内部类 将本身的对象作为内部类的静态成员
            - 这个也是延迟加载
        - 懒汉
            - 在第一次访问的时候创建
            - 没有做双重检查 线程不安全
        - 饿汉
            - 同样利用了类加载器
            - 但是没有实现静态内部类的延迟加载的方式
        - 枚举
            - 在Effective Java中推荐的方式
            - 可以在序列化和反射中保证唯一性
            - 由于可读性等原因 还没有被广泛推广
- 工厂模式（简单工厂和抽象工厂）
    - 简单工厂
        - 又被称为静态工厂
        - 是工厂方法的特例
        - 只有一个工厂 但是要负责所有的产品子类的生成 通过工厂方法的参数来区分是哪个具体产品
    - 工厂方法
        - 类层次
            - 一个抽象产品类 可以派生出多个不同的产品
            - 一个抽象工厂类 可以派生出多个不同的工厂
        - 每个工厂只能生产一个具体馋哦
        - 如果有一个新的产品子类 那么就创建一个新的工厂
    - 抽象工厂
        - 类层次
            - 多个抽象产品类 各自可以派生出多个不同的产品
            - 一个抽象工厂类 可以派生出多个不同的工厂
        - 这种模式产生的背景
            - 在UI交互中 Button和Text组件在Unix和Windows中要构建不同的实现
            - 如果通过工厂方法 那么就是一个工厂生成Button 一个生成Text
            - 通过使用场景来分类 那么可以变为Unix工厂和Windows工厂 各自生成自己的产品 这种方式更加适合这种场景
- 观察者模式
    - 一个对象修改时需要通知依赖于它的对象
    - 优点
        - 观察者和被观察者之间是抽象耦合的
    - 缺点
        - 如果有很多观察者的话 通知时间会很久
        - 观察者和被观察者之间有循环依赖的话会造成系统奔溃
        - 只能让被观察者知道发生变换 但是无法通知发生了什么变化
    - 注意点
        - Java有自己实现的观察者模式
        - 避免循环依赖
        - 如果是同步的 一个观察者报错会导致系统卡壳 故使用异步
    - 重点 被观察者要维护一个观察者的队列 需要有添加观察者 执行所有观察者等行为
- 外观模式
- 模版方法模式
- 状态模式
- 修饰器模式

## 抽象工厂和工厂方法模式的区别？

## 什么是值传递和引用传递？

## 可以在static环境中访问非static变量吗？
    不可以 static环境中没有this的引用

## Java支持多继承么,为什么？
    不支持

## 用最有效率的方法计算2乘以8？
    2 << 3

## 构造器是否可被重写？
    各个类名不同 故构造器名也不同 所以构造器不能被继承 就更不能被重写

## char型变量中能不能存贮一个中文汉字，为什么？
    可以 Java中默认使用UTF8的编码 一个char占两个子节

## 如何实现对象克隆？
    调用Object的clone方法实现浅克隆
    重写clone方法可以实现深克隆
    通过序列化的方式可以实现深克隆

## object中定义了哪些方法？

## hashCode的作用是什么？
    用于快速查找和元素对比

## for-each与常规for循环的效率对比

## 写出几种单例模式实现，懒汉模式和饿汉模式区别

## 请列出 5 个运行时异常。

## 2个不相等的对象有可能具有相同的 hashcode吗？
    可能

## 访问修饰符public,private,protected,以及default的区别？
- public 公共的 任何地方都可以访问
- private 私有的 只有类内部的方法和内部类可以访问
- protected

## 谈谈final在java中的作用？
- 类 不可继承
- 方法 不可重写
- 变量 常量 不可修改
## java中的Math.round(-1.5) 等于多少呢？
    -1

## String属于基础的数据类型吗？
    不属于 是对象

## 如何将字符串反转呢？
    StringBuilder.reverse()
## 描述动态代理的几种实现方式，它们分别有什么优缺点
    JDK动态代理
    cgLib
## 在自己的代码中，如果创建一个java.lang.String类，这个类是否可以被类加载器加载？为什么。
    不可以  双亲委派机制
## 谈谈你对java.lang.Object对象中hashCode和equals方法的理解。在什么场景下需要重新实现这两个方法。
    
## 在jdk1.5中，引入了泛型，泛型的存在是用来解决什么问题。
    在编译时检查安全性
    
## 什么是序列化，怎么序列化，反序列呢？
## java8的新特性。
## 匿名内部类是什么？如何访问在其外面定义的变量呢？
## break和continue有什么区别？
## String s = "Hello";s = s + " world!";这两行代码执行后，原始的 String 对象中的内容是否会改变？
    不会 String是不可变类
## 怎样将GB2312编码的字符串转换为ISO-8859-1编码的字符串？
## try-catch-finally-return执行顺序
    Java 7新的 try-with-resources语句，平时有使用吗
    简述一下面向对象的”六原则一法则”。
    switch是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？
    数组有没有length()方法？String有没有length()方法？
    是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用？
## String s = new String("jay");创建了几个字符串对象？
    两个
## 匿名内部类是否可以继承其它类？是否可以实现接口？
    都可以
## 我们能将int强制转换为 byte类型的变量吗？如果该值大于byte 类型的范围，将会出现什么现象？
    将四子节的int的前三个子节给切掉 只保留最后一个子节
## float f=3.4;正确吗？
    应该为 float f = 3.4f;
## 你能写出一个正则表达式来判断一个字符串是否是一个数字吗？

## Reader和InputStream区别？
    一个字符输入流 一个子节输入流
    
## 列举出JAVA中6个比较常用的包

## JDK 7有哪些新特性

## 同步和异步有什么区别？

- 同步 一直等待调用结果 是阻塞的
- 异步 调用后立即返回 不等待结果 非阻塞

## 实际开发中，Java一般使用什么数据类型来代表价格？

## 64 位 JVM 中，int 的长度是多数？
    4子节32位
    
## 字节流与字符流的区别

## Java 事件机制包括哪三个部分？分别介绍下。

## 为什么等待和通知是在 Object 类而不是 Thread 中声明的？

    对象是可以作为锁的 等待和通知是锁的任务 而不是线程的

## 每个对象都可上锁，这是在 Object类而不是 Thread 类中声明，为什么呢？

## 为什么char 数组比Java中的 String 更适合存储密码？

    因为String是不可变的
    在修改后修改前的数据在下一次GC线程清理之前会一直在内存中 造成安全隐患 char数组可以显式的被擦除

## 如何使用双重检查锁定在 Java 中创建线程安全的单例？
    
## 如果你的Serializable类包含一个不可序列化的成员，会发生什么？你是如何解决的？

    该类型无法被序列化 解决办法就是都加上序列化接口
    
## 什么是serialVersionUID ？如果你不定义这个, 会发生什么？

    一个long类型的静态私有常量 序列化时是否版本兼容
    如果类Persion是可序列化的 在其修改之后 如果是向下兼容的 则不修改 如果不是 则修改
    
## Java 中，Maven 和 ant，gradle 有什么区别？
## 常见的序列化协议有哪些
- JSON
- XML
- Thrift
- Avro
## @Transactional注解在什么情况下会失效，为什么。
- 修饰非public方法 如果不是public方法 不会检查Transactional的属性
    - 由于这样不会报错 最容易犯
- 属性propagation设置
- 只有方法被别的类调用时才会有事务
- 异常被catch 只有错误被抛出了才会被回滚
    - 被调用的抛出错误 认为需要回滚
    - 调用的把错误catch了 认为不需要回滚
- 用了不支持事务的数据库引擎（如MySql的myISAM）
## Java 中，DOM 和SAX 解析器有什么不同？
- DOM
    - Document Object Model
    - 将XML文件表示成树然后解析
    - 速度快
    - 需要更多的内存
- SAX
    - Simple API for XML Parsing
    - 基于事件的XML解析器
    - 支持只读取一部分的XML
- 区别
    - DOM加载整个文件 SAX加载一部分
    - DOM速度快
    - SAX适合解析大型的XML
## 数组在内存中如何分配
1. 声明数组 在栈中创建数组的指针
2. 创建数组 在堆中创建指针的数组
3. 指针指向对应的元素
- 如果是基础类型 那么数组中不是指针 而是数据
## 什么是 Busy spin？我们为什么要使用它？
- 在不释放CPU的情况下等待的技术
- 经常使用在避免丢失CPU缓存的情况下
## Java 中怎么获取一份线程 dump 文件？
- jstack pid命令打印
- thread.getStackTrace()方法
## 父类的静态方法能否被子类重写
可以被继承 但是无法重写
## 什么是不可变对象
所有成员变量都是final的 如String Integer等
## 如何正确的退出多层嵌套循环？
```
out: for (;;) {
    for (;;) {
        break out;
    }
}
```
## SimpleDateFormat是线程安全的吗?你一般怎么格式化
    线程不安全的
    由于内部引用了一个Calendar用于存储相关的日期信息
解决办法
- 把它作为局部变量
    - 缺点 方法结束又要作为垃圾挥手
- 加锁
    - 效率太低
- 第三方库
- 为每个线程创建一个
## 抽象类必须要有抽象方法吗？
    不需要
## 怎么实现动态代理？有哪些应用
    JDK和CgLib
- 日志打印
- AoP
## 什么是内部类？内部类的作用
    定义在类内部的类
## 泛型中extends和super的区别
- T extends Test
    - T必须为Test的子类
- T super Test
    - T必须为Test的父类
## 内部类有几种，在项目中的有哪些应用
## utf-8编码中的中文占几个字节；int型几个字节？
## 说说你对Java注解的理解
## Java 中 java.util.Date 与 java.sql.Date 有什么区别？
    继承关系 java.sql.Date extends java.util.Date
- java.sql.Date
    - 只包含日期 没有时间
    - 主要用于数据库交互
- java.util.Date
    - 包含日期时间
    - 内部除了sql环境使用这个
## 说一下隐式类型转换
    从小的类型转换为大的类型
    byte -> short char -> int -> long -> float -> double
## 抽象类能使用final修饰吗
    不能 抽象类不能实例化 必须被继承
    final表示不能被继承
    二者矛盾
## 给我一个符合开闭原则的设计模式的例子
## Files的常用方法都有哪些
## Java 中，Serializable与Externalizable 的区别？
## Java锁有哪些种类，它们都有哪些区别
## 抽象的方法是否可同时是静态的）,是否可同时是本地方法），是否可同时被 synchronized 修饰？
    都不能
## 一个”.java”源文件中是否可以包含多个类（不是内部类）？有什么限制？
    可以 但是只有一个和文件名同名的公共类
## 说说代理的实现原理
- 静态代理
    - 程序员自己实现
- JDK动态代理
    - 生成一个实现了接口的类的
## 了解哪设计模式，举例说说在jdk源码哪些用到了你说的设计模式
- 单例模式
- Runtime
## 什么是B/S架构？什么是C/S架构
- B/S 浏览器服务器
- C/S 客户端服务器
## Java有那些开发平台呢？
## Java内部类为什么可以访问到外部类呢？
    可以
## Java支持的数据类型有哪些？什么是自动拆装箱呢？
    valueOf和xxxValue完成自动拆箱装修
- 弊端
    - 在循环中可能会创建很多不必要的对象
    - Integer泛型的List中的remove(int)和remove(Object)会有歧义
    - 在这里不会自动拆装箱 但是需要注意传入的类型
## 创建线程有几种不同的方式
## hashCode()和equals()方法的重要性体现在什么地方？
    HashMap中会先比较hashCode是否相等
    如果相等才会调用equals来判断
## 如何通过反射获取和设置对象私有字段的值？
    调用可以获得私有字段的方法
    设置访问为true
    调用set或get获取
## 如何通过反射调用对象的方法？
## 简述一下面向对象的"六原则一法则"
- 单一职责原则
    - 类应该小而精 只负责一块任务
- 开闭原则
    - 对扩展开放
    - 对性能优化封闭
- 依赖倒转原则（面向接口编程）
    - 声明方法的参数以及返回值是尽量使用抽象类型
    - 在任何地方都可以用任何实现来替代
- 里式替换原则
    - 父类可以出现的地方都可以替换成子类
    - 可以用来测试继承是否合理
    - 继承一定是对父类能力的扩展而不是减少
- 接口隔离原则
    - 接口应该只表示一种能力
    - 接口应该尽量细分 有多个能力就实现多个接口
- 合成聚合复用原则
    - 优先使用Has-A的方式来使用而不是继承
    - 例如在Java API中 Properties继承了Hashtable 应该是将Hashtable作为成员
- 迪米特法则（最少知识原则）
    - 类应该知道最少的关于别的类的细节
    - 低耦合
## Java 对象不使用时为什么要赋值为 null？
    方便GC
## 什么时候用断言（assert）？
## AJAX请求为什么不安全？
## 一个Java字符串中到底能有多少个字符?
    int的最大值
## StringBuilder为什么线程不安全？
## 深克隆和浅克隆
## 聊一聊设计模式的基本原则
## Java 能否自定义一个类叫 java.lang.System？
## 异常处理完成以后，Exception对象会发生什么变化？
    估计是GC吧
## 什么是RMI？
## 解释下Serialization和Deserialization。
## 环境变量Path和ClassPath的作用是什么？如何设置这两个环境变量？
## 字符型常量和字符串常量的区别
## 构造器Constructor是否可被override
    不可