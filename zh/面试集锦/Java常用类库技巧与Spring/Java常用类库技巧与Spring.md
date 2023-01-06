#  Java常用类库与技巧

## Error和Exception的区别

**Java异常体系**

![](assert\2019-08-28_17-16-01.png)

- 从概念角度解析Java的异常处理机制

> Error :程序无法处理的系统错误,编译器不做检查 
>
> Exception :程序可以处理的异常,捕获后可能恢复
>
> 总结:前者是程序无法处理的错误,后者是可以处理的异常

- 编译器角度

> RuntimeException :不可预知的,程序应当自行避免
>
> 非RuntimeException :可预知的,从编译器校验的异常

- 从责任角度看:

> 1.Error属于JVM需要负担的责任
>
> 2.RuntimeException是程序应该负担的责任
>
> 3.Checked Exception可检查异常是Java编译器应该负担的责任

## 常见Error以及Exception

 - RuntimeException
> NullPointerException-空指针引用异常
> ClassCastException-类型强制转换异常
> llegalArgumentException-传递非法参数异常
> IndexOutOfBoundsException-下标越界异常
> NumberFormatException-数字格式异常

- 非RuntimeException

> 1.ClassNotFoundException-找不到指定class的异常
>
> 2.IOException-IO操作异常
>
> 3.FileNotFoundException-IO操作异常子类

- Erro

> 1.NoClassDefFoundError-找不到class定义的异常
>
> ![](assert\2019-08-28_17-28-08.png)
>
> 2.StackOverflowError-深递归导致栈被耗尽而抛出的异常
>
> 3.0utOfMemoryError-内存溢出异常

## Java的异常处理机制

- 抛出异常:创建异常对象,交由运行时系统处理 
- 捕获异常：寻找合适的异常处理器处理异常，否则终止运行

![](assert\2019-08-29_10-13-05.png)

> jdk1.7的新特性try-resource:自动关闭流

## Java异常的处理原则

- 具体明确：抛出的异常应能通过异常类名和message准确说明异常的类型和产生异常的原因
- 提早抛出：应尽可能早的发现并抛出异常，便于精确定位问题
- 延迟捕获：异常的捕获和处理应尽可能延迟，让掌握更多信息的作用域来处理异常

## try-catch的性能

**Java异常处理消耗性能的地方**

>try-catch块影响JVM的优化
>异常对象实例需要保存栈快照等信息，开销较大

## Java集合框架

- 数组和链表的区别
- 链表的操作，如反转，链表环路检测，双向链表，循环链表相关操作
队列，栈的应用
- 二叉树的遍历方式及其递归和非递归的实现
- 红黑树的旋转(左旋和右旋)

![](assert\2019-08-29_10-37-28.png)

### 集合之List和Set

![](assert\2019-08-29_10-58-09.png)

### 集合之Map

![](assert\2019-08-29_11-16-30.png)

#### HashMap、HashTable、ConccurentHashMap的区别

##### HashMap

> HashMap（Java8以前）：数组+链表
>
> 极端情况（哈希冲突频繁导致链表过长）下性能恶化:从O(1)变成O(n)
>
> HashMap（Java8及以后）：数组+链表+红黑树
> 性能从O（n）提高到O（logn）

![](assert\2019-08-29_11-20-42.png)

> HashMap底层原理 ：
>
> ​       数据结构：数组+链表+红黑树
>
> ①通过哈希函数(散列哈数)计算哈希值（散列值），然后将元素存储在Node数组（哈希表也叫散列表）中
> 
>②哈希值冲突时会链成一个链表(Node有一个next指针指向下一个Node的引用),当add操作使某个桶的阈值TREEIFY_THRESHOLD>=8并且数组容量（MIN_TREEIFY_CAPACITY>=64）大于等于64时转成红黑树TreeNode(Node子类的子类),当remove操作使某个桶的阈值UNTREEIFY_THRESHOLD<=6时红黑树转成链表
>
>

 **HashMap:put方法的逻辑**
> 1、如果HashMap未被初始化过，则初始化
>
> 2、对Key求Hash值，然后再计算下标
>
> 3、如果没有碰撞，直接放入桶中
>
> 4、如果碰撞了，以链表的方式链接到后面
>
> 5、如果链表长度超过阈值(TREEIFY_THRESHOLD=8)，就把链表转成红黑树
>
> 6、如果链表长度低于6(UNTREEIFY_THRESHOLD=6)，就把红黑树转回链表
>
> 7、如果节点已经存在就替换旧值
>
> 8、如果桶满了（容量16*负载因子0.75），就需要resize（扩容2倍后重排）

![](assert\2019-08-29_14-54-45.png)
![](assert\2019-08-29_11-44-38.png)
![](assert\2019-08-29_11-40-43.png)
![](assert\2019-08-29_14-54-57.png)

**HashMap：扩容的问题**

>多线程环境下，调整大小会存在条件竞争，容易造成死锁
>rehashing是一个比较耗时的过程

**HashMap知识点回顾**

>成员变量：数据结构，树化阈值
>构造函数：延迟创建
>put和get的流程
>哈希算法，扩容，性能

##### Hashtable

> Hashtable线程安全，锁住整个对象，数组+链表

##### ConccurentHashMap

> 早期的ConcurrentHashMap：通过分段锁Segment来实现

![](assert\2019-08-29_15-15-56.png)

> 当前的ConcurrentHashMap:CAS+synchronized使锁更细化

![](assert\2019-08-29_15-19-23.png)

 **ConcurrentHashMap:put方法的逻辑**
> 1.判断Node数组是否初始化，没有则进行初始化操作
>
> 2.通过hash定位数组的索引坐标，是否有Node节点，如果没有则使用CAS进行添加（链表的头节点），添加失败则进入下次循环。
>
> 3.检查到内部正在扩容，就帮助它一块扩容。
>
> 4.如果f != null，则使用synchronized锁住元素（链表/红黑二叉树的头元素）
> 	4.1如果是Node（链表结构）则执行链表的添加操作。
> 	4.2如果是TreeNode（树型结构）则执行树添加操作。
>
> 5.判断链表长度已经达到临界值8，当然这个8是默认值，大家也可以去做调整，当节点数超过这个值就需要把链表转换为树结构。

**ConcurrentHashMap总结：比起Segment，锁拆得更细**

>首先使用无锁操作CAS插入头节点，失败则循环重试
>若头节点已存在，则尝试获取头节点的同步锁，再进行操作

**ConcurrentHashMap：别的需要注意的点**

>size()方法和mappingCount(）方法的异同，两者计算是否准确？
>
>推荐使用mappingCount(返回的是long),因为size(返回的是int)有大小限制（限制是int的最大值）,map中的元素的可能超过这个值
>
>多线程环境下如何进行扩容？

##### 三者的区别

> HashMap线程不安全，数组+链表+红黑树
>Hashtable线程安全，锁住整个对象，数组+链表
>ConcurrentHashMap线程安全，CAS+同步锁，数组+链表+红黑树
>HashMap的key、value均可为nul，而其他的两个类不支持

## J.U.C知识点梳理

> java.util.concurrent：提供了并发编程的解决方案

- CAS是java.util.concurrent.atomic包的基础
- AQS是java.util.concurrent.locks包以及一些常用类比如Semaphore，ReentrantLock等类的基础

### J.U.C包的分类

> 线程执行器executor
>锁locks
>原子变量类atomic
>并发工具类tools
>并发集合collections

![](assert\2019-08-29_15-45-52.png)

### 并发工具类

>闭锁CountDownLatch
>循环栅栏CyclicBarrier
>信号量Semaphore
>交换器Exchanger

**CountDownLatch：让主线程等待一组事件发生后继续执行**

- 事件指的是CountDownLatch里的countDown（）方法

![](assert\2019-08-29_15-52-34.png)

**CyclicBarrier：阻塞当前线程，等待其他线程**

- 等待其它线程，且会阻塞自己当前线程，所有线程必须同时到达栅栏位置后，才能继续执行
- 所有线程到达栅栏处，可以触发执行另外一个预先设置的线程

![](assert\2019-08-29_15-53-57.png)

**Semaphore：控制某个资源可被同时访问的线程个数**

![](assert\2019-08-29_16-05-12.png)

**Exchanger：两个线程到达同步点后，相互交换数据**

![](assert\2019-08-29_16-06-50.png)

**BlockingQueue**

​	主要用于生产者-消费者模式，在多线程场景时生产者线程在队列尾部添加元素，而消费者线程则在队列头部消费元素，通过这种方式能够达到将任务的生产和消费进行隔离的目的

**BlockingQueue实现类**

> 1、ArrayBlockingQueue：一个由数组结构组成的有界阻塞队列
> 2、LinkedBlockingQueue：一个由链表结构组成的有界/无界阻塞队列
> 3、PriorityBlockingQueue：一个支持优先级排序的无界阻塞队列
> 4、DealyQueue：一个使用优先级队列实现的无界阻塞队列
> 5、SynchronousQueue：一个不存储元素的阻塞队列
> 6、LinkedTransferQueue：一个由链表结构组成的无界阻塞队列
> 7、LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列

## Java的IO机制

### BIO、NIO、AIO

#### **Block-IO:InputStream和OutputStream，Reader和Writer**

![](assert\2019-08-29_16-58-49.png)

> 参考资料:
>
> [①IO流之字节流的使用](https://github.com/674872075/Bigdata/blob/master/zh/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/20-IO%E6%B5%81%E4%B9%8B%E5%AD%97%E8%8A%82%E6%B5%81%E7%9A%84%E4%BD%BF%E7%94%A8.md)
>
> [②IO流之字符流的使用](https://github.com/674872075/Bigdata/blob/master/zh/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/21-IO%E6%B5%81%E4%B9%8B%E5%AD%97%E7%AC%A6%E6%B5%81%E7%9A%84%E4%BD%BF%E7%94%A8.md)

#### **NonBlock-IO：构建多路复用的、同步非阻塞的IO操作**

![](assert\2019-08-29_17-02-42.png)

- NIO的核心
    >Channels
    >Buffers
    >Selectors

![](assert\2019-08-29_17-04-53.png)

​	 **NIO-Channels**

- FileChannel

     >transferTo：把FileChannel中的数据拷贝到另外一个Channel transferFrom：把另外一个	Channel中的数据拷贝到FileChannel避免了两次用户态和内核态间的上下文切换，即”零拷贝”，  效率较高

- DatagramChannel

- SocketChannel

- ServerSocketChannel

**NIO-Buffers**

- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer
- MappedByteBuffer

**NIO-Selector** 

![](assert\2019-08-29_17-14-34.png)

**IO多路复用：调用系统级别的select\poll\epoll**

![](assert\2019-08-29_17-32-03.png)

##### select、poll、epoll的区别

> 支持一个进程所能打开的最大连接数

![](assert\2019-08-29_17-34-01.png)

> FD剧增后带来的IO效率问题

![](assert\2019-08-29_17-36-51.png)

> 消息传递方式

![](assert\2019-08-29_17-38-05.png)

#### Asynchronous IO：基于事件和回调机制(jdk7)

![](assert\2019-08-29_17-39-25.png)

- AIO如何进一步加工处理结果

>基于回调：实现CompletionHandler接口，调用时触发回调函数
>返回Future：通过isDone(）查看是否准备好，通过get(）等待返回数据

#### BIO、NIO、AIO对比

![](assert\2019-08-29_17-48-47.png)

## Spring考点梳理

### 你了解Spring IOC么

![](assert\2019-08-29_20-05-14.png)

依赖注入的方式
>Setter
>Interface
>Constructor
>Annotation

#### 依赖倒置原则、IOC、DI、IOC容器的关系

![](assert\2019-08-29_20-06-02.png)

#### Spring IOC支持的功能

>依赖注入
>依赖检查
>自动装配
>支持集合
>指定初始化方法和销毁方法
>支持回调方法

#### Spring IOC容器的核心接口

> BeanFactory
>ApplicationContext

#### BeanFactory:Spring框架最核心的接口

> 提供IOC的配置机制
>包含Bean的各种定义，便于实例化Bean
>建立Bean之间的依赖关系
>Bean生命周期的控制

#### BeanFactory体系结构

![](assert\2019-08-29_20-17-16.png)

#### BeanFactory与ApplicationContext的比较

>BeanFactory是Spring框架的基础设施面向Spring
>
>ApplicationContext面向使用Spring框架的开发者

#### BeanFactory与FactoryBean的比较

BeanFactory是一个工厂，spring中的工厂，它能够去生产我们的spring bean,并且能够去获取我们的对象

BeanFactory是spring简单工厂模式的接口类，spring IOC特性核心类，提供从工厂类中获取bean的各种方法，是所有bean的容器

> FactoryBean仍然是一个bean，但不同于普通bean，它的实现类(加 前缀 &)最终也需要注册到BeanFactory中。它也是一种简单工厂模式的接口类，但是生产的是单一类型的对象，与BeanFactory生产多种类型对象不同。
>
> FactoryBean是一个接口，实现了这个接口的类，在注册到spring BeanFactory后，并不像其它类注册后暴露的是自己，它暴露的是FactoryBean的真实对象

#### ApplicationContext的功能（继承多个接口）

> BeanFactory：能够管理、装配Bean
>ResourcePatternResolver：能够加载资源文件
>MessageSource：能够实现国际化等功能
>ApplicationEventPublisher：能够发布事件，将事件广播给监听器，实现监听机制

![](assert\2019-08-29_20-46-59.png)

![](assert\2019-08-29_21-40-06.png)

#### getBean方法的代码逻辑

>转换beanName
>从缓存中加载实例
>实例化Bean
>检测parentBeanFactory
>初始化依赖的Bean
>创建Bean

#### Spring Bean的作用域

>singleton:Spring的默认作用域，容器里拥有唯一的Bean实例
>prototype：针对每个getBean请求，容器都会创建一个Bean实例
>request：会为每个Http请求创建一个Bean实例
>session：会为每个session创建一个Bean实例
>globalSession：会为每个全局Http Session创建一个Bean实例，该作用域仅对Portlet有效

#### Spring Bean的生命周期

- 创建过程

![](assert\2019-08-29_21-59-10.png)

**@PostConstruct执行在InitializingBean之前**

- 销毁过程
  
  >若方法加了@PreDestroy，会调用该方法
  >
  >若实现了DisposableBean接口，则会调用destroy方法
  >若配置了destry-method属性，则会调用其配置的销毁方法
  >
  >@PreDestroy ->DisposableBean( destroy() ) ->destry-method

##### Spring生命周期地图

![SpringBean生命周期--创建过程和销毁过程](assert\2023-01-06_15-07-10.png)

### 你了解Spring AOP么

#### 关注点分离：不同的问题交给不同的部分去解决

>面向切面编程AOP正是此种技术的体现
>通用化功能代码的实现，对应的就是所谓的切面（Aspect）
>业务功能代码和切面代码分开后，架构将变得高内聚低耦合
>确保功能的完整性：切面最终需要被合并到业务中（Weave）

#### AOP的三种织入方式

>编译时织入：需要特殊的Java编译器，如AspectJ
>类加载时织入：需要特殊的Java编译器，如AspectJ和AspectWerkz
>运行时织入：Spring采用的方式，通过动态代理的方式，实现简单

#### AOP的主要名词概念

>Aspect：通用功能的代码实现
>Target：被织入Aspect的对象
>Join Point：可以作为切入点的方法，所有方法都可以作为切入点
>Pointcut:Aspect: 实际被应用在的Join Point，支持正则
>Advice：类里的方法以及这个方法如何织入到目标方法的方式
>Weaving:Aop的实现过程

> **Joinpoint（连接点）**：
> 所谓连接点是指那些被拦截到的点。在spring中，这些点指的是方法，因为spring只支持方法类型的连接点。
> **Pointcut（切入点）**：
> 所谓切入点是指我们要对哪些Joinpoint进行拦截的定义。
>
> **Advice（通知/增强）**：
> 所谓通知是指拦截到Joinpoint之后所要做的事情就是通知。通知的类型：前置通知，后置通知，异常通知，最终通知，环绕通知。
> **Introduction（引介）**：
> 引介是一种特殊的通知在不修改类代码的前提下，Introduction可以在运行期为类动态地添加一些方法或Field。
> **Target（目标对象）**：
> 代理的目标对象。
> **Weaving（织入）**：
> 是指把增强应用到目标对象来创建新的代理对象的过程。
> spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入。
> **Proxy（代理）**： 
> 一个类被AOP织入增强后，就产生一个结果代理类。
> **Aspect（切面）**：
> 是切入点和通知（引介）的结合

#### Advice的种类

>前置通知（Before）
>后置通知（AfterReturning）
>异常通知（AfterThrowing）
>最终通知（After）
>环绕通知（Around）

#### AOP的实现：JdkProxy和Cglib

>由AopProxyFactory根据AdvisedSupport对象的配置来决定
>默认策略如果目标类是接口，则用JDKProxy来实现，否则用后者
>JDKProxy的核心：InvocationHandler接口和Proxy类
>Cglib：以继承的方式动态生成目标类的代理

#### AOP的实现：JdkProxy和Cglib

>JDKProxy：通过Java的内部反射机制实现
>Cglib：借助ASM实现
>反射机制在生成类的过程中比较高效
>ASM在生成类之后的执行过程中比较高效

**代理模式：接口+真实实现类+代理类**

#### Spring里的代理模式的实现

>真实实现类的逻辑包含在了getBean方法里
>getBean方法返回的实际上是Proxy的实例
>Proxy实例是Spring 采用JDK Proxy或CGLIB动态生成的

### Spring IOC三级缓存解决循环依赖

```java
/** DefaultSingletonBeanRegistry **/
    
/** Cache of singleton objects: bean name to bean instance. 一级缓存*/
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

/** Cache of early singleton objects: bean name to bean instance. 二级缓存*/
private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);

/** Cache of singleton factories: bean name to ObjectFactory. 三级缓存*/
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);


@Override
	@Nullable
	public Object getSingleton(String beanName) {
		return getSingleton(beanName, true);
	}

	/**
	 * Return the (raw) singleton object registered under the given name.
	 * <p>Checks already instantiated singletons and also allows for an early
	 * reference to a currently created singleton (resolving a circular reference).
	 * @param beanName the name of the bean to look for
	 * @param allowEarlyReference whether early references should be created or not
	 * @return the registered singleton object, or {@code null} if none found
	 */
	@Nullable
	protected Object getSingleton(String beanName, boolean allowEarlyReference) {
		// Quick check for existing instance without full singleton lock
		Object singletonObject = this.singletonObjects.get(beanName);
		if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			singletonObject = this.earlySingletonObjects.get(beanName);
			if (singletonObject == null && allowEarlyReference) {
				synchronized (this.singletonObjects) {
					// Consistent creation of early reference within full singleton lock
					singletonObject = this.singletonObjects.get(beanName);
					if (singletonObject == null) {
						singletonObject = this.earlySingletonObjects.get(beanName);
						if (singletonObject == null) {
							ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
							if (singletonFactory != null) {
								singletonObject = singletonFactory.getObject();
								this.earlySingletonObjects.put(beanName, singletonObject);
								this.singletonFactories.remove(beanName);
							}
						}
					}
				}
			}
		}
		return singletonObject;
	}
```

spring中使用了3个map来作为三级缓存，每一级对应一个map

| 第几级缓存 | 对应的map                                        | 说明                                                         |
| ---------- | ------------------------------------------------ | ------------------------------------------------------------ |
| 第1级      | Map singletonObjects                             | 用来存放已经完全创建好的单例bean  beanName->bean实例         |
| 第2级      | Map earlySingletonObjects                        | 用来存放早期的bean  beanName->bean实例                       |
| 第3级      | Map<String, ObjectFactory<?>> singletonFactories | 用来存放单例bean的ObjectFactory  beanName->ObjectFactory实例 |

这3个map的源码位于`org.springframework.beans.factory.support.DefaultSingletonBeanRegistry`类中。

#### 三级缓存是什么?

​	Spring IOC解决循环依赖需要使用到三级缓存，一级缓存(singletonObjects)存储的是成品对象、二级缓存(earlySingletonObjects)存储的是半成品对象(未完全填充属性值、未完成Spring Bean的初始化操作)、三级缓存(singletonFactories)存储的是代理对象，分阶段存储对象内容，用于解决循环依赖问题

##### 为什么不使用一级缓存?

​	如果只使用一级缓存，则流程无法拆分，代码复杂度会增加，同时半成品对象可能有空指针异常，如果将成品对象与半成品对象分开，则处理起来更加简单、美观、易扩展

##### 为什么不使用二级缓存?

​	Spring的两大特性不仅包括IOC还包括AOP,即基于字节码增强后的方法，三级缓存最主要解决的循环依赖就是对AOP的处理，如果把AOP代理对象的创建提前，则使用二级缓存也可以解决。但是，这就违背了Spring创建对象的原则-------Spring首先将所有的普通Bean对象初始化完成，再处理代理对象的初始化。所以为了设计上的合理性和可扩展性，创建了三级缓存，用于存储不同时期的对象



#### 附加资料

[循环依赖不用三级缓存可以么?](http://www.itsoku.com/course/5/138)

1、循环依赖如果是构造器的方式，bean无法创建成功，这个前提是bean都是单例的，bean如果是多例的，大家自己可以分析分析。

2、spring是通过singletonsCurrentlyInCreation这个列表来发现循环依赖的，这个列表会记录创建中的bean，当发现bean在这个列表中存在了，说明有循环依赖，并且这个循环依赖是无法继续走下去的，如果继续走下去，会进入死循环，此时spring会抛出异常让系统终止。

3、当某个bean进入到2级缓存的时候，说明这个bean的早期对象被其他bean注入了，也就是说，这个bean还是半成品，还未完全创建好的时候，已经被别人拿去使用了，所以必须要有3级缓存，2级缓存中存放的是早期的被别人使用的对象，如果没有2级缓存，是无法判断这个对象在创建的过程中，是否被别人拿去使用了。

3级缓存是为了解决一个非常重要的问题：早期被别人拿去使用的bean和最终成型的bean是否是一个bean，如果不是同一个，则会产生异常，所以以后面试的时候被问到为什么需要用到3级缓存的时候，你只需要这么回答就可以了：三级缓存是为了判断循环依赖的时候，早期暴露出去已经被别人使用的bean和最终的bean是否是同一个bean，如果不是同一个则弹出异常，如果早期的对象没有被其他bean使用，而后期被修改了，不会产生异常，如果没有三级缓存，是无法判断是否有循环依赖，且早期的bean被循环依赖中的bean使用了。。

spring容器默认是不允许早期暴露给别人的bean和最终的bean不一致的，但是这个配置可以修改，而修改之后存在很大的分享，所以不要去改，通过下面这个变量控制

```java
org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#allowRawInjectionDespiteWrappingprivate boolean allowRawInjectionDespiteWrapping = false;
```



### Dubbo知识总结

> 注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小

> 监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示

> 注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外

> 注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者

> 注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表

> 注册中心和监控中心都是可选的，服务消费者可以直连服务提供者

> 服务提供者无状态，任意一台宕掉后，不影响使用

> 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

#### dubbo中的注册中心zookeeper如何感知服务下线

zookeeper提供了“心跳检测”功能，它会定时向各个服务提供者发送一个请求（实际上建立的是一个 socket 长连接），如果长期没有响应，服务中心就认为该服务提供者已经“挂了”，并将其剔除。

服务消费方会监听zookeeper相应的路径，一旦路径上的数据有任何的变化，zookeeper就会将新的服务列表发送给消费方，消费方在获取到数据后，刷新本地缓存的列表。