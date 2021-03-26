# Java垃圾回收机制

## 对象被判定为垃圾的标准

> 没有被其他对象引用
>
> 参考：
>
> [JVM优化](https://github.com/674872075/Bigdata/blob/master/zh/JVM%E4%BC%98%E5%8C%96/JVM%E7%AC%AC%E4%BA%8C%E5%A4%A9/JVM%E4%BC%98%E5%8C%96%E7%AC%AC%E4%BA%8C%E5%A4%A9%E7%AC%94%E8%AE%B0.md#22%E6%A0%87%E8%AE%B0%E6%B8%85%E9%99%A4%E6%B3%95)

## 垃圾回收算法

- 引用计数算法
- 可达性分析算法

### 引用计数算法

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-18-23.png)

**优点:**

​		**执行效率高,程序执行受影响较小**

**缺点:**

​		**无法检测出循环引用的情况,导致内存泄露**

### 可达性分析算法

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-23-23.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-25-07.png)

#### 标记--清除算法

- 标记:从根集合进行扫描,对存活的对象进行标记

- 清除:对堆内存从头到尾进行线性遍历,回收不可达对象内存

>  容易造成内存碎片化

**优缺点**

可以看到，标记清除算法解决了引用计数算法中的循环引用的问题，没有从root节点引用的对象都会被回收。

同样，标记清除算法也是有缺点的：

- 效率较低，标记和清除两个动作都需要遍历所有的对象，并且在GC时，需要停止应用程序，对于交互性要求比较高的应用而言这个体验是非常差的。
- 通过标记清除算法清理出来的内存，碎片化较为严重，因为被回收的对象可能存在于内存的各个角落，所以清理出来的内存是不连贯的。

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-38-56.png)

#### 复制算法（常用于年轻代）

- 分为对象面和空闲面
- 对象在对象面上创建
- 存活的对象被从对象面复制到空闲面
- 将对象面所有对象内存清除

优点：

-  解决碎片化问题
-  顺序分配内存,简单高效
- 适用于对象存活率低的场景

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-42-58.png)

##### 年轻代GC(8:1:1)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-00-55.png)

1. 在GC开始的时候，对象只会存在于Eden区和名为“From”的Survivor区，Survivor区“To”是空的。
2. 紧接着进行GC，Eden区中所有存活的对象都会被复制到“To”，而在“From”区中，仍存活的对象会根据他们的年龄值来决定去向。年龄达到一定值(年龄阈值，可以通过-XX:MaxTenuringThreshold来设置)的对象会被移动到年老代中，没有达到阈值的对象会被复制到“To”区域。
3. Survivor中放不下的对象会晋升到老年代
4. 新生的大对象进入老年代(超过-XX:+PretenuerSizeThreshold)
5. 经过这次GC后，Eden区和From区已经被清空。这个时候，“From”和“To”会交换他们的角色，也就是新的“To”就是上次GC前的“From”，新的“From”就是上次GC前的“To”。不管怎样，都会保证名为To的Survivor区域是空的。
6. GC会一直重复这样的过程，直到“To”区被填满，“To”区被填满之后，会将所有对象移动到年老代中。

**优缺点**

优点：

- 在垃圾对象多的情况下，效率较高
- 清理后，内存无碎片

缺点：

- 在垃圾对象少的情况下，不适用，如：老年代内存
- 分配的2块内存空间，在同一个时刻，只能使用一半，内存使用率较低

#### 标记压缩算法（常用于老年代）

标记压缩算法是在标记清除算法的基础之上，做了优化改进的算法。和标记清除算法一样，也是从根节点开始，对对象的引用进行标记，在清理阶段，并不是简单的清理未标记的对象，而是将存活的对象压缩到内存的一端，然后清理边界以外的垃圾，从而解决了碎片化的问题

**优缺点**

优缺点同标记清除算法，解决了标记清除算法的碎片化的问题，同时，标记压缩算法多了一步，对象移动内存位置的步骤，其效率也有有一定的影响。

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-46-17.png)

#### 分代收集算法

- 按照对象生命周期的不同划分区域以采用不同的垃圾回收算法
- 根据回收对象的特点进行选择，在jvm中，**年轻代适合使用复制算法**，**老年代适合使用标记清除或标记压缩算法**

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_09-54-19.png)

##### 分代收集算法GC的分类

- Minor GC

  > 发生在年轻代中，复制算法

  **GC触发条件：Eden区满了触发Minor GC，这时会把Eden区存活的对象复制到Survivor区，当对象在Survivor区熬过一定次数(年龄阈值[复制的次数]，可以通过-XX:MaxTenuringThreshold来设置,默认15)的Minor GC之后，就会晋升到老年代（当然并不是所有的对象都是这样晋升的到老年代的），当老年代满了，就会报OutofMemory异常。**

- Full GC和Major GC(两者等价)

  > 发生在老年代和年轻代，老年代使用标记--清除算法或标记--压缩算法,老年代GC通常也伴随着年轻代GC
  >
  > Full GC比Minor GC慢，执行效率低

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-10-32.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-20-57.png)

> System.gc()不会立马触发Full GC ，只是提醒垃圾收集器回收垃圾，垃圾收集器之后会在一个不确定的时间收集垃圾

### Stop-the-World

- JVM由于要执行GC而停止了应用程序的执行
- **任何一种GC算法中都会发生**
- 多数GC优化通过减少Stop-the-world发生的时间来提高程序性能

### Safepoint

- 分析过程中对象引用关系不会发生变化的点
- 产生Safepoint的地方:方法调用;循环跳转;异常跳转等
- 安全点数量得适中

### 常见垃圾收集器

#### 年轻代垃圾收集器

##### Serial收集器(-XX:+UseSerialGC,复制算法)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-41-19.png)

##### ParNew收集器(-XX:+UseParNewGC,复制算法)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-43-26.png)

##### Parallel Scavenge(-XX:+UseParallelGC ,复制算法)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-46-01.png)

> -XX:UseAdaptiveSizePolicy
>
> - 自适应GC模式，垃圾回收器将自动调整年轻代、老年代等参数，达到吞吐量、堆大小、停顿时间之间的平衡。
> - 一般用于，手动调整参数比较困难的场景，让收集器自动进行调整

#### 老年代垃圾收集器

##### Serial Old收集器(-XX:+UseSerialOldGC,标记-整理算法)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-53-11.png)

##### Parallel Old收集器(-XX : +UseParalleloldGC,标记-整理算法)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-56-07.png)

##### CMS收集器(-XX:+UseConcMarkSweepGC,标记-清除算法)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_10-59-40.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-00-26.png)

##### G1收集器(-XX:+UseG1GC,复制+标记-整理算法)Garbage First

- 并行和并发

- 分代收集

- 空间整合

- 可预测的停顿

  > G1垃圾收集器是在jdk1.7中正式使用的全新的垃圾收集器，oracle官方计划在jdk9中将G1变成默认的垃圾收集器，以替代CMS。
>
  > G1的设计原则就是简化JVM性能调优，开发人员只需要简单的三步即可完成调优：
>
  > 1. 第一步，开启G1垃圾收集器
  > 2. 第二步，设置堆的最大内存
  > 3. 第三步，设置最大的停顿时间
>
  > G1中提供了三种模式垃圾回收模式，Young GC、Mixed GC 和 Full GC，在不同的条件下被触发。
>

**原理**

G1垃圾收集器相对比其他收集器而言，最大的区别在于它取消了年轻代、老年代的物理划分，取而代之的是将堆划分为若干个区域（Region），这些区域中包含了有逻辑上的年轻代、老年代区域。

这样做的好处就是，我们再也不用单独的空间对每个代进行设置了，不用担心每个代内存是否足够。

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-12-15.png)

在G1划分的区域中，年轻代的垃圾收集依然采用暂停所有应用线程的方式，将存活对象拷贝到老年代或者Survivor空间，G1收集器通过将对象从一个区域复制到另外一个区域，完成了清理工作。

这就意味着，在正常的处理过程中，G1完成了堆的压缩（至少是部分堆的压缩），这样也就不会有cms内存碎片问题的存在了。

在G1中，有一种特殊的区域，叫Humongous区域。

  - 如果一个对象占用的空间超过了分区容量50%以上，G1收集器就认为这是一个巨型对象。
  - 这些巨型对象，默认直接会被分配在老年代，但是如果它是一个短期存在的巨型对象，就会对垃圾收集器造成负面影响。
- 为了解决这个问题，G1划分了一个Humongous区，它用来专门存放巨型对象。如果一个H区装不下一个巨型对象，那么G1会寻找连续的H分区来存储。为了能找到连续的H区，有时候不得不启动Full GC。
  

**Young GC**

Young GC主要是对Eden区进行GC，它在Eden空间耗尽时会被触发。

  - Eden空间的数据移动到Survivor空间中，如果Survivor空间不够，Eden空间的部分数据会直接晋升到年老代空间。
  - Survivor区的数据移动到新的Survivor区中，也有部分数据晋升到老年代空间中。
- 最终Eden空间的数据为空，GC停止工作，应用线程继续执行。
  

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-14-20.png)



**Remembered Set（已记忆集合）**

在GC年轻代的对象时，我们如何找到年轻代中对象的根对象呢？

根对象可能是在年轻代中，也可以在老年代中，那么老年代中的所有对象都是根么？

如果全量扫描老年代，那么这样扫描下来会耗费大量的时间。

于是，G1引进了RSet的概念。它的全称是Remembered Set，其作用是跟踪指向某个堆内的对象引用。

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-14-52.png)

每个Region初始化时，会初始化一个RSet，该集合用来记录并跟踪其它Region指向该Region中对象的引用，每个Region默认按照512Kb划分成多个Card，所以RSet需要记录的东西应该是 xx Region的 xx Card。

**Mixed GC**

当越来越多的对象晋升到老年代old region时，为了避免堆内存被耗尽，虚拟机会触发一个混合的垃圾收集器，即Mixed GC，该算法并不是一个Old GC，除了回收整个Young Region，还会回收一部分的Old Region，这里需要注意：是一部分老年代，而不是全部老年代，可以选择哪些old region进行收集，从而可以对垃圾回收的耗时时间进行控制。也要注意的是Mixed GC 并不是 Full GC。

MixedGC什么时候触发？ 由参数 -XX:InitiatingHeapOccupancyPercent=n 决定。默认：45%，该参数的意思是：当老年代大小占整个堆大小百分比达到该阀值时触发。

它的GC步骤分2步：

  1. 全局并发标记（global concurrent marking）
2. 拷贝存活对象（evacuation）

**全局并发标记**

全局并发标记，执行过程分为五个步骤：

  - 初始标记（initial mark，STW）
    - 标记从根节点直接可达的对象，这个阶段会执行一次年轻代GC，会产生全局停顿。
  - 根区域扫描（root region scan）
    - G1 GC 在初始标记的存活区扫描对老年代的引用，并标记被引用的对象。
    - 该阶段与应用程序（非 STW）同时运行，并且只有完成该阶段后，才能开始下一次 STW 年轻代垃圾回收。
  - 并发标记（Concurrent Marking）
    - G1 GC 在整个堆中查找可访问的（存活的）对象。该阶段与应用程序同时运行，可以被 STW 年轻代垃圾回收中断。
  - 重新标记（Remark，STW）
    - 该阶段是 STW 回收，因为程序在运行，针对上一次的标记进行修正。
  - 清除垃圾（Cleanup，STW）
  - 清点和重置标记状态，该阶段会STW，这个阶段并不会实际上去做垃圾的收集，等待evacuation阶段来回收。

**拷贝存活对象**

Evacuation阶段是全暂停的。该阶段把一部分Region里的活对象拷贝到另一部分Region中，从而实现垃圾的回收清理。

**G1收集器相关参数**

- -XX:+UseG1GC
  
  - 使用 G1 垃圾收集器
  
- -XX:MaxGCPauseMillis
  
  - 设置期望达到的最大GC停顿时间指标（JVM会尽力实现，但不保证达到），默认值是 200 毫秒。
  
- -XX:G1HeapRegionSize=n
  
    - 设置的 G1 区域的大小。值是 2 的幂，范围是 1 MB 到 32 MB 之间。目标是根据最小的 Java 堆大小划分出约 2048 个区域。
  - 默认是堆内存的1/2000。
  
- -XX:ParallelGCThreads=n
  
  - 设置 STW 工作线程数的值。将 n 的值设置为逻辑处理器的数量。n 的值与逻辑处理器的数量相同，最多为 8。
  
- -XX:ConcGCThreads=n
  
  - 设置并行标记的线程数。将 n 设置为并行垃圾回收线程数 (ParallelGCThreads) 的 1/4 左右。
  
- -XX:InitiatingHeapOccupancyPercent=n
  
    - 设置触发标记周期的 Java 堆占用率阈值。默认占用率是整个 Java 堆的 45%。

**测试**

      -XX:+UseG1GC -XX:MaxGCPauseMillis=100 -XX:+PrintGCDetails -Xmx256m
      
      #日志
      [GC pause (G1 Evacuation Pause) (young), 0.0044882 secs]
         [Parallel Time: 3.7 ms, GC Workers: 3]
            [GC Worker Start (ms): Min: 14763.7, Avg: 14763.8, Max: 14763.8, Diff: 0.1]
            #扫描根节点
            [Ext Root Scanning (ms): Min: 0.2, Avg: 0.3, Max: 0.3, Diff: 0.1, Sum: 0.8]
            #更新RS区域所消耗的时间
            [Update RS (ms): Min: 1.8, Avg: 1.9, Max: 1.9, Diff: 0.2, Sum: 5.6]
               [Processed Buffers: Min: 1, Avg: 1.7, Max: 3, Diff: 2, Sum: 5]
            [Scan RS (ms): Min: 0.0, Avg: 0.0, Max: 0.0, Diff: 0.0, Sum: 0.0]
            [Code Root Scanning (ms): Min: 0.0, Avg: 0.0, Max: 0.0, Diff: 0.0, Sum: 0.0]
            #对象拷贝
            [Object Copy (ms): Min: 1.1, Avg: 1.2, Max: 1.3, Diff: 0.2, Sum: 3.6]
            [Termination (ms): Min: 0.0, Avg: 0.1, Max: 0.2, Diff: 0.2, Sum: 0.2]
               [Termination Attempts: Min: 1, Avg: 1.0, Max: 1, Diff: 0, Sum: 3]
            [GC Worker Other (ms): Min: 0.0, Avg: 0.0, Max: 0.0, Diff: 0.0, Sum: 0.0]
            [GC Worker Total (ms): Min: 3.4, Avg: 3.4, Max: 3.5, Diff: 0.1, Sum: 10.3]
            [GC Worker End (ms): Min: 14767.2, Avg: 14767.2, Max: 14767.3, Diff: 0.1]
         [Code Root Fixup: 0.0 ms]
         [Code Root Purge: 0.0 ms]
         [Clear CT: 0.0 ms] #清空CardTable
         [Other: 0.7 ms]
            [Choose CSet: 0.0 ms] #选取CSet
            [Ref Proc: 0.5 ms] #弱引用、软引用的处理耗时
            [Ref Enq: 0.0 ms] #弱引用、软引用的入队耗时
            [Redirty Cards: 0.0 ms]
            [Humongous Register: 0.0 ms] #大对象区域注册耗时
            [Humongous Reclaim: 0.0 ms] #大对象区域回收耗时
            [Free CSet: 0.0 ms]
         [Eden: 7168.0K(7168.0K)->0.0B(13.0M) Survivors: 2048.0K->2048.0K Heap: 55.5M(192.0M)->48.5M(192.0M)] #年轻代的大小统计
       [Times: user=0.00 sys=0.00, real=0.00 secs] 
      ```
    
      #### 3.4.6、对于G1垃圾收集器优化建议
    
      - 年轻代大小
        - 避免使用 -Xmn 选项或 -XX:NewRatio 等其他相关选项显式设置年轻代大小。
        - 固定年轻代的大小会覆盖暂停时间目标。
      - 暂停时间目标不要太过严苛
        - G1 GC 的吞吐量目标是 90% 的应用程序时间和 10%的垃圾回收时间。
        - 评估 G1 GC 的吞吐量时，暂停时间目标不要太严苛。目标太过严苛表示您愿意承受更多的垃圾回收开销，而这会直接影响到吞吐量。

#### 垃圾收集器之间的联系

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-07-32.png)

## GC相关的面试题

### Object的finalize0方法的作用是否与C++的析构函数作用相同

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-34-42.png)

### Java中的强引用,软引用,弱引用,虚引用有什么用

#### 强引用

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-39-52.png)

#### 软引用

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-42-19.png)

#### 弱引用

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-45-07.png)

#### 虚引用

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-45-55.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-47-26.png)

#### 类层次结构

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_11-48-29.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\JVM--GC\assert\2019-08-27_15-28-48.png)

