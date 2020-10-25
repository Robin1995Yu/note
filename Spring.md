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
    Spring Bean 的生命周期
    Spring MVC 的工作原理？
    Spring 循环注入的原理？
    Spring 中用到了那些设计模式？
    Spring AOP的理解，各个术语，他们是怎么相互工作的？
    Spring框架中的单例bean是线程安全的吗?
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