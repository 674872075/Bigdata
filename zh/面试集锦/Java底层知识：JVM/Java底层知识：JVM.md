

# Java底层知识：JVM

## 谈谈你对Java的理解

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_10-26-27.png)

> 语言特性：泛型、反射等

## Java一次编译，到处运行如何实现

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_10-26-27.png)

## ![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_10-58-20.png)JVM如何加载.class文件

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_11-03-57.png)

JVM 中类的装载是由类加载器（ClassLoader）和它的子类来实现的，Java 中的类加载器是一个重要的Java 运行时系统组件，它负责在运行时查找和装入类文件中的类。

由于Java 的跨平台性，经过编译的Java 源程序并不是一个可执行程序，而是一个或多个类文件。当Java 程序需要使用某个类时,JVM 会确保这个类已经被加载、连接（验证、准备和解析）和初始化。类的**加载(通过ClassLoader加载class文件字节码，生成字节码对象)**是指通过ClassLoader把类的.class 文件中的数据读入到内存中，通常是**创建一个字节数组读入.class 文件**，然后产生与所加载类对应的Class 对象。加载完成后，Class 对象还不完整，所以此时的类还不可用。当类被加载后就进入连接阶段，这一阶段包括**验证(词法分析、语法分析、语义分析)、准备（为静态变量分配内存并设置默认的初始值）和解析（JVM将常量池中的符号引用替换为直接引用）**三个步骤(连接)。最后JVM 对类进行**初始化**(执行类变量赋值和静态代码块)，包括：如果类存在直接的父类并且这个类还没有被初始化，那么就先初始化父类；如果类中存在初始化语句，就依次执行这些初始化语句。类的加载是由类加载器完成的，类加载器包括：**根加载器（BootStrap）、扩展加载器（Extension）、系统加载器（System）和用户自定义类加载器（java.lang.ClassLoader 的子类）。**

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_11-08-53.png)

## 谈谈ClassLoader 

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_11-45-36.png)

从Java 2（JDK 1.2）开始，类加载过程采取了**双亲委派机制**（PDM）。PDM 更好的保证了Java 平台的安全性，在该机制中，JVM 自带的Bootstrap 是根加载器，其他的加载器都有且仅有一个父类加载器。类的加载首先检查是否已加载该类,已加载直接返回该类，没有就请求父类加载器加载，加载过就返回没加载过就向上请求直到请求到最顶层的类加载器还是没加载过就尝试在自己的搜索范围内扫描加载，父类加载器无能为力时才由其子类加载器在自己的搜索范围扫描加载。JVM 不会向Java 程序提供对Bootstrap 的引用。下面是关于几个类加载器的说明：
  **A:类加载器的概述**
  	负责将.class文件加载到内存中，并为之生成对应的Class对象。虽然我们不需要关心类加载机制，但是了解这个	机制我们就能更好的理解程序的运行。

  **B:类加载器的分类**

- Bootstrap ClassLoader 根类加载器

- Extension ClassLoader 扩展类加载器

- Sysetm ClassLoader 系统类加载

  **C:类加载器的作用**
   **Bootstrap ClassLoader 根类加载器**
    也被称为引导类加载器，负责Java核心类的加载
    比如System,String等。在JDK中JRE的lib目录下rt.jar、resources.jar、charsets.jar等文件中
  
    **C++编写**，加载核心库java.*
  
    **Extension ClassLoader 扩展类加载器**（ExtClassLoader）间接继承自ClassLoader
    负责JRE的扩展目录中jar包的加载。
    在JDK中JRE的lib目录下ext目录
  
    **Java编写**，加载扩展库javax.*
  
    **Sysetm ClassLoader 系统类加载器**(AppClassLoader) 间接继承自ClassLoader
    负责在JVM启动时加载来自java命令的class文件，以及classpath环境变量所指定的jar包和类路径
  
    **Java编写**，加载程序所在目录
  
    **自定义ClassLoader** 
  
    需要继承ClassLoader（根类加载器）
  
    Java编写，定制化加载
  
   ClassLoader是用户自定义加载器的默认父加载器
  
## 双亲委派机制

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_15-54-14.png)

双亲委派模型工作过程是：如果一个类加载器收到类加载的请求如果它没加载过这个类，它不会自己去尝试加载这个类，而是把这个请求委派给父类加载器完成（父类加载器如果加载过直接返回没加载过就委派给父类）。每个类加载器都是如此，只有当父加载器在自己的搜索范围内找不到指定的类时（即`ClassNotFoundException`），子加载器才会尝试自己去加载。

## loadClass和Class.forName的区别

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_17-30-36.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_17-35-08.png)

## 为什么要使用双亲委派机制去加载类

**避免多份同样字节码的加载**

为什么需要双亲委派模型呢？假设没有双亲委派模型，试想一个场景：

> 黑客自定义一个`java.lang.String`类，该`String`类具有系统的`String`类一样的功能，只是在某个函数稍作修改。比如`equals`函数，这个函数经常使用，如果在这这个函数中，黑客加入一些“病毒代码”。并且通过自定义类加载器加入到`JVM`中。此时，如果没有双亲委派模型，那么`JVM`就可能误以为黑客自定义的`java.lang.String`类是系统的`String`类，导致“病毒代码”被执行。

而有了双亲委派模型，黑客自定义的`java.lang.String`类永远都不会被加载进内存。因为首先是最顶端的类加载器加载系统的`java.lang.String`类，最终自定义的类加载器无法加载`java.lang.String`类。

或许你会想，我在自定义的类加载器里面强制加载自定义的`java.lang.String`类，不去通过调用父加载器不就好了吗?确实，这样是可行。但是，在`JVM`中，判断一个对象是否是某个类型时，如果该对象的实际类型与待比较的类型的类加载器不同，那么会返回false。

举个简单例子：

> `ClassLoader1`、`ClassLoader2`都加载`java.lang.String`类，对应Class1、Class2对象。那么`Class1`对象不属于`ClassLoad2`对象加载的`java.lang.String`类型。


## 谈谈反射

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_11-17-36.png)

> - **getDeclaredMethod**   获取的是类自身声明的所有方法，包含public、protected、private、default方法，**需要注意的是，这些方法一定是在当前类中声明的，从父类中继承的不算，实现接口的方法由于有声明所以包括在内。**
>
> - getMethod   获取的是类的所有共有方法，这就包括自身的所有public方法，和从父类继承的、从接口实现的所有public方法。**需要注意的是，这些方法可以是在当前类中没有声明的但一定是public的**
>
> - getMethods()   获取的是所有public方法，包括：类自身声明的public方法、父类中的public方法、从接口实现的接口方法。
>
>   **getDeclaredMethods()**  获取的是类中所有方法，也就是源文件中有哪些方法，就会获取到哪些，包括：类自身的方法、**重写**的父类的方法、**实现**的接口方法。

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_11-42-41.png)

## 类从编译到执行的过程

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_11-44-08.png)

## 类的加载方式

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_16-36-02.png)

## JVM内存模型--JDK8

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_17-45-16.png)

 ![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_21-29-38.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_21-30-59.png)

## MetaSpace(元数据空间)和PermGen(永久代)的区别

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_21-48-21.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_21-47-35.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_22-22-08.png)

# 堆和栈

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_22-25-01.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_22-27-23.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_22-29-58.png)

## 堆和栈的区别

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_22-32-51.png)

## 不同JDK版本之间的intern()方法的区别---JDK6 VS JDK6+

![](F:\BigDataNote\Bigdata\zh\面试集锦\Java底层知识：JVM\assert\2019-08-26_22-38-36.png)

