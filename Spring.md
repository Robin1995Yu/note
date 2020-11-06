# Spring 相关
## BeanFactory和 ApplicationContext有什么区别？
- BeanFactory和ApplicationContext是Spring的两大核心接口，都可以当做Spring的容器。其中ApplicationContext是BeanFactory的子接口。
- BeanFactory
  - Spring最底层的接口
  - Bean定义
  - 读取Bean配置的文档
  - 管理Bean加载
  - 实例化Bean
  - 控制Bean的生命周期
  - 维护Bean之间的依赖关系
- ApplicationContext
  - 继承了BeanFactory
  - 继承MessageSorce支持国际化
  - 同一资源文件的访问方式
  - 提供监听器中注册bean事件
  - 同时加载多个配置文件
  - 
## Spring IOC 的理解，其初始化过程
- IOC 控制反转
  - 将初始化好的对象交由容器管理 而不是在对象内直接控制
  - 什么是反转？
    - 在传统的JavaSE中 我们直接在对象内通过new的方式来创建对象 程序主动去依赖对象
    - 反转后由容器控制对象
- 初始化过程
  - 读取Resource
    - 通过ResourceLoader将资源读取成Resource
  - 读取BeanDefinition
    - 通过BeanDefinitionReader将Resource读取成BeanDefinition
  - 注册BeanDefinition
    - 将BeanDefinition注册到BeanDefinitionRegistry
  - 将BeanDefinition实例化为Bean
    - 通过BeanFactory将BeanDefinition实例化
## Spring Bean 的生命周期
1.  执行BeanFactoryPostProcessor.postProcessBeanFactory
    - 操作BeanFactory
2.  执行InstatiantionAwareBeanPostProcessor.postProcessBeforeInstatiation
    - 操作Bean的Class对象和beanName
3.  执行Bean的构造器
4.  执行InstantiationAwareBeanPostProcessor.postProcessPropertyValues
    - 操作Bean对象和beanName
5.  注入Bean的属性
6.  调用BeanNameAware.setBeanName
    - 操作beanName
7.  调用BeanFactoryAware.setBeanFactory
    - 操作BeanFactory
8.  执行BeanPostProcessor.postProcessBeforeInitialization
    - 操作Bean和beanName
9.  调用InitializingBean.afterPropertiesSet方法
    - 在容器初始化好所有Bean后调用
10. 执行Bean的init-method
11. 执行BeanPostProcessor.postProcessAfterInitalization
    - 操作Bean对象和beanName
12. 执行InstantiationAwareBeanPostProcessor.postProcessAfterInitialization
    - 操作Bean对象和beanName
13. 执行业务逻辑
14. 调用DisposableBean.destory
15. 调用Bean的destory-method
## Spring MVC 的工作原理？
- 一次请求的过程
  1. 请求到达DispatcherServlet
  2. DispatcherServlet请求HandlerMapping
  3. HandlerMapping根据配置找到最终执行的Handler
  4. 返回最终的Handler给DispatcherServlet
  5. DispacherServlet请求HandlerAdatper执行Handler
  6. HandlerAdatper根据不同类型的请求执行对应的Handler
  7. Handler返回ModelAndView给HandlerAdatper
  8. HandlerAdapter返回ModelAndView给DispatcherServlet
  9. DispatcherServlet将ModelAndView交给ViewResolver
  10. ViewResolver将ModelAndView处理成View返回给DispatcherServlet
  11. Dispatcher将model数据渲染到View中
  12. 返回给用户
- 主要组件
  - DispatcherServlet 前端控制器
  - HandlerMapping 处理器映射器
  - HandlAdapter 处理器适配器
  - Handler 处理器 需要自己开发
  - ViewResolver 视图解析器
  - View 视图 接口支持不同类型的视图 这里需要自己写JSP
## Spring 循环注入的原理？
- 三种循环注入的情况
  - 构造器循环依赖
  - 单例模式下setter循环依赖
  - 非单例模式循环依赖
- 以上三种中只有第二种可以通过三级缓存来处理
- 单例对象的初始化过程
  1. 调用构造函数实例化对象
  2. 填充属性
  3. 调用init-method
- 在第二步出现的循环注入可以通过三级缓存解决
- 三级缓存
  1. 一级 singletonObjects 完成初始化的对象
  2. 二级 earlySingletonObjects 完成实例化但是尚未初始化的提前曝光的对象
  3. 三级 singletonFactories 进入实例化阶段的单例对象工程
- 如果A依赖于B B依赖于A
  - 尝试获取A
  - 获取无结果 A实例化（从三级获取） 放入二级 由于依赖于B 尝试获取B
  - 获取无结果 B实例化（从三级获取） 放入二级 由于依赖于A 尝试获取A
  - 当前只获取到了A的时候或对象 则将之注入到B中 B初始化完成 放入一级 并从二级移除
  - 将B注入到A A初始化完成 放入一级 并从二级移除
## Spring 中用到了那些设计模式？
- 工厂模式
  - BeanFactory
    - 延迟加载 只有在使用的时候才会加载
  - ApplicationContext
    - 在启动的时候将所有的bean加载完成
    - ClassPathXmlApplication 将上下文作为资源路径
    - FileSystemXmlApplication 从文件系统中的XML作为资源
    - XmlWebApplicationContext 从web系统中的XML作为资源
- 单例模式
  - 好处
    - 减少new的开销
    - 减少GC的开销
  - Spring中的bean默认都是单例的
  - 通过缓存实现 如果缓存没有 创建并放入缓存 如果有 直接得到缓存中的数据
- 代理模式
  - 主要在AOP中使用
  - 代理的实现
    - 如果实现接口 使用JDK proxy 
    - 如果没有 使用Cglib
    - 也可以使用AspectJ
      - 通过操作字节码来实现AOP
      - 如果切面很多 那么使用这个 速度比Spring AOP要快 但是也要复杂
- 模版模式
- 观察者模式
- 适配器模式
- 装饰者模式
## Spring AOP的理解，各个术语，他们是怎么相互工作的？
- 概念
  - 切面（Aspect） 用@Aspect注解 或者\<aop:aspect\>标签
  - 连接点（Join Point） 可以采取发送通知的地方
  - 通知（Advice）在某个连接点时采取的操作
  - 切入点（Pointcut） 表示一组连接点与通知 切入点表达式 默认使用Aspect切入点表达式
  - 
## Spring框架中的单例bean是线程安全的吗?
Spring @ Resource和Autowired有什么区别？
Spring 的不同事务传播行为有哪些，有什么作用？
Spring Bean 的加载过程是怎样的？
请举例说明@Qualifier注解
Spring 是如何管理事务的，事务管理机制？
使用Spring框架的好处是什么？
Spring由哪些模块组成？
ApplicationContext通常的实现是什么？
什么是Spring的依赖注入？
你怎样定义类的作用域？
Spring框架中的单例bean是线程安全的吗？
你可以在Spring中注入一个null 和一个空字符串吗？
你能说下 Spring Boot 与 Spring 的区别吗
SpringBoot 的自动配置是怎么做的？
@RequestMapping 的作用是什么？
spring boot 有哪些方式可以实现热部署？
说说Ioc容器的加载过程
为什么 Spring 中的 bean 默认为单例？
说说Spring中的@Configuration
FileSystemResource 和ClassPathResource 有何区别？
什么是 Swagger？你用 Spring Boot 实现了它吗？
spring的controller是单例还是多例，怎么保证并发的安全。
说一下Spring的核心模块
如何向 Spring Bean 中注入一个 Java.util.Properties
如何给Spring 容器提供配置元数据?
如何在Spring中如何注入一个java集合，实现过吗？
什么是基于Java的Spring注解配置? 举几个例子？
怎样开启注解装配？
Spring支持哪些事务管理类型
在Spring AOP 中，关注点和横切关注的区别是什么？
spring 中有多少种IOC 容器？
描述一下 DispatcherServlet 的工作流程
介绍一下 WebApplicationContext吧
Spring Boot 的配置文件有哪几种格式？它们有什么区别？
Spring Boot 需要独立的容器运行吗？
Spring Boot 自动配置原理是什么？
RequestMapping 和 GetMapping 的不同之处在哪里？
如何使用Spring Boot实现异常处理？
Spring Boot 中如何解决跨域问题 ?
Spring Boot 如何实现热部署 ?
Spring Boot打成的 jar 和普通的jar有什么区别呢?
bootstrap.properties 和 application.properties 有何区别 ?
springboot启动机制