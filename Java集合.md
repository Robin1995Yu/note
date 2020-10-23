# Java 集合

## Arraylist与LinkedList区别
    线性表和双链表
## Collections.sort和Arrays.
    TimSort
## HashMap原理，java8做了什么改变
## List 和 Set，Map 的区别
## poll()方法和 remove()方法的区别？

## HashMap的死循环问题
    多线程的时候会形成环
    1.8解决 通过红黑树
    
    HashMap 的扩容过程
    HashSet是如何保证不重复的
## HashMap 是线程安全的吗，为什么不是线程安全的？死循环问题？
    不是 在扩容的过程中可能会产生线程不安全 产生环路链表 造成死循环
## LinkedHashMap的应用，底层，原理
    在HashMap的基础上给结点添加before和after指针 形成链表
    
## 哪些集合类是线程安全的？哪些不安全？
## ArrayList 和 Vector 的区别是什么？
## Collection与Collections的区别是什么？
## 如何决定使用 HashMap 还是TreeMap？
## 如何实现数组和 List之间的转换？
## 迭代器 Iterator 是什么？怎么用，有什么特点？
## Iterator 和 ListIterator 有什么区别？
## 怎么确保一个集合不能被修改？
## 快速失败(fail-fast)和安全失败(fail-safe)的区别是什么？
## 什么是Java优先级队列(Priority Queue)？
## JAVA8的ConcurrentHashMap为什么放弃了分段锁，有什么问题吗，如果你来设计，你如何设计。
## 阻塞队列的实现，ArrayBlockingQueue的底层实现？
## Java 中的 LinkedList是单向链表还是双向链表？
## 说一说ArrayList 的扩容机制吧
## HashMap 的长度为什么是2的幂次方，以及其他常量定义的含义~
## ConcurrenHashMap 原理？1.8 中为什么要用红黑树？
## ArrayList的默认大小
## 为何Collection不从Cloneable和Serializable接口继承？
## Enumeration和Iterator接口的区别？
## 我们如何对一组对象进行排序？
## 当一个集合被作为参数传递给一个函数时，如何才可以确保函数不能修改它？
## 说一下 HashSet 的实现原理？
## Array 和 ArrayList 有何区别？
## 在 Queue中poll()和 remove()有什么区别？
## ArrayList 如何删除重复的元素或者指定的元素；
## 讲讲红黑树的特点？
## Java集合类框架的最佳实践有哪些？
## Enumeration接口和Iterator 接口的区别有哪些？
## HashSet和TreeSet有什么区别？
## ArrayList 和 HashMap 的默认大小是多数？