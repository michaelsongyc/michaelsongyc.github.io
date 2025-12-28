# 中间件-缓存

常用三个框架 Caffeine, GuavaCache, EhCache

## 前言
在大部分互联网架构中 Cache 己经成为了必可不少的一环- 常用的方案有大家熟知的 NoSQL数据库 (Redis、Memcached) 等,也有大量的进程内缓存 Caffeine、EhCache、Guava Cache等.

## 缓存框架介绍
### 1.Caffeine
Caffeine是一个基于Java8开发的提供了近乎最佳命中率的高性能的缓存库，
缓存和ConcurrentMap有点相似，但还是有所区别- 最根本的区别是ConcurrentMap将会持有所有加入到缓存当中的元素，
直到它们被从缓存当中手动移除- 但是，Caffeine的缓存Cache 通常会被配置成自动驱逐缓存中元素，以限制其内存占用- 
在某些场景下，LoadingCache 和AsyncLoadingCache 因为其自动加载缓存的能力将会变得非常实用，Spring5 开始不再支持 Guava Cache, 改用Caffeine.

### 2.EhCache
EhCache 是一个用Java实现的使用简单，高速，实现线程安全的缓存管理类库，而且从12之后就支持了集群 ehcache提供
了用内存，磁盘文件存储，以及分布式存储方式等多种灵活的cache管理方案- 同时ehcache作为开放源代码项目，采用限
制比较宽松的Apache License V2.0作为授权方式，被广泛地用于Hibernate, Spring， Cocoon等其他开源系统- 

### 3.GuavaCache
guava cache是google开源的一款本地缓存工具库，它的设计灵感来源于ConcurrentHashMap， 使用多个segments方式的
细粒度锁，在保证线程安全的同时，支持高并发场景需求，同时支持多种类型的緩存清理策略，包括基于容量的清理、基于
时间的清理、基于引用的清理等- 

## 缓存框架主要特性
### 1.Caffeine
Caffeine提供了灵活的构造器去创建一个拥有下列特性的缓存：
- 自动加载元素到缓存当中，异步加载的方式也可供选择
- 当达到最大容量的时候可以使用基于就近度和频率的算法进行基于容量的驱逐
- 将根据缓存中的元素上一次访问或者被修改的时间进行基于过期时间的驱逐
- 当向缓存中一个已经过时的元素进行访问的时候将会进行异步刷新
- key将自动被弱引用所封装
- value将自动被弱引用或者软引用所封装
- 驱逐(或移除)缓存中的元素时将会进行通知
- 写入传播到一个外部数据源当中
- 持续计算缓存的访问统计指标
- 为了提高集成度，扩展模块提供了JSR-107 JCache 和Guava适配器- JSR-107规范了基于Java 6的API，在牺牲了功
能和性能的代价下使代码更加规范- Guava的Cache是Caffeine的原型库并且Caffeine提供了适配器以供简单的迁移策略

### 2.EhCache
快速，针对大型高并发系统场景，Ehcache的多线程机制有相应的优化改善
- 简单，很小的jar包，简单配置就可直接使用，单机场景下无需过多的其他服务依赖
- 支持多种的缓存策略，灵活
- 缓存数据有两级：内存和磁盘，与一般的本地内存缓存相比，有了磁盘的存储空间，将可以支持更大量的数据缓存需求
具有缓存和缓存管理器的监听接口，能更简单方便的进行缓存实例的监控管理
- 支持多缓存管理器实例，以及一个实例的多个缓存区域
- Ehcache的超时设置主要是针对整个cache实例设置整体的超时策略，而没有较好的处理针对单独的key的个性的超时设置
- 因此，在使用中要注意过期失效的缓存元素无法被GC回收，时间越长缓存越多，内存占用也就越大，内存泄露的概率也越大

### 3.GuavaCache
Guava Cache的架构设计灵感ConcurrentHashMap，在简单场景中可以通过HashMap实现简单数据缓存，但如果要
实现缓存随时间改变、存储的数据空间可控则缓存工具还是很有必要的- cache存储的是键值对的集合，不同时是还
需要处理缓存过期、动态加载等算法逻辑，需要额外信息实现这些操作，对此根据面向对象的思想，还需要做方法与
数据的关联性封装，主要实现的缓存功能有：自动将节点加载至缓存结构中，当緩存的数据超过最大值时，使用LRU
算法替换；它具备根据节点上一次被访问或写入时间计算缓存过期机制，缓存的key被封装在WeakReference引用
中，缓存的value被封装在 WeakReference 或 SoftReference 引用中; 还可以统计缓存使用过程中的命中率、异常率和命中率等统计数据.

## 缓存框架原理
### 1.Caffeine 主要原理
#### 1.1淘汰算法
##### 1.1.1常见算法
对于 Java 进程内缓存我们可以通过 HashMap 来实现。不过，Java 进程内存是有限的，不可能无限地往里面放缓存对象。
这就需要有合适的算法辅助我们淘汰掉使用价值相对不高的对象，为新进的对象留有空间。常见的缓存淘汰算法有 FIFO、LRU、LFU.

- FIFO(First In First Out): 先进先出
  - 它是优先淘汰掉最先缓存的数据、是最简单的淘汰算法。缺点是如果先缓存的数据使用频率比较高的话，那么该数据就不停
  地进进出出，因此它的缓存命中率比较低。
- LRU(Least Recently Used): RRA.
  - 它是优先淘汰掉最久未访问到的数据。缺点是不能很好地应对偶然的突发流量。比如一个数据在一分钟内的前59秒访问很多
  次，而在最后1秒没有访问，但是有一批冷门数据在最后一秒进入缓存，那么热点数据就会被冲刷掉。
- LFU(Least Frequently Used) :
  - 最近最少频率使用。它是优先淘汰掉最不经常使用的数据，需要维护一个表示使用频率的字段。
  - 主要有两个缺点：
  1. 如果访问频率比较高的话，频率宇段会占据一定的空间；
  2. 无法合理更新新上的热点数据，比如某个歌手的老歌播放历史较三，新出的歌如果和老歌一起排序的话，就永无出头之日;

#### 1.1.2 W-TinyLFU 算法
  Caffeine 使用了 W-TinyLFU 算法，解决了 LRU 和 LFU 上述的缺点，它主要干了两件事：
  1. 采用 Count-Min Sketch 算法降低频率信息带来的内存消耗；
  2. 维护一个PK机制保障新上的热点数据能够缓存。

如下图所示，Count-Min Sketch 算法类似布隆过滤器(Bloom fiter)思想，对子频率统计我们其实不需要一个精确值。存储
数据时，对key进行多次 hash 函数运算后，二维数组不同位置存储频率（Caffeine 实际实现的时候是用一维long 型数组，
每个long 型数字切分成16份，每份4bit，默认15次为最高访问频率，每个key实际 hash 了四次，落在不同 long 型数宇的16
份中某个位置）。读取某个key的访问次数时，会比较所有位置上的频率值，取最小值返口。对于所有key的访问频率之和有
个最大值，当达到最大值时，会进行reset即对各个缓存key的场率除以2。

{{< figure src="/image/tech/java/cache/Count_Min_Sketch.png" title="Count-Min Sketch" >}}

如下图缓存访问频率存储主要分为两大部分，即 LRU 和 Segmented LRU。 新访问的数据会进入第一个 LRU，在 Caffeine
里叫 WindowDeque. 当WindowDeque满时, 会进入Segmented LRU 中的 ProbationDeque, 在后续被访问到时，它会
被提升到 ProtectedDeque。当 ProtectedDeque 满时，会有数据降级到 ProbationDeque。数据需要淘汰的时候，对
probationDeque 中的数据进行淘汰。具体淘汰机制：取ProbationDeque 中的队首和队尾进行 PK，队首数据是最先进入队
列的，称为受害者，队尾的数据称为攻击者，比较两者 频率大小，大胜小汰

### 2.EnCache 主要原理
略

### 3.GuavaCache主要原理

Guava Cache实质是一个在本地缓存KV数据的LocalCache。而LocalCache的数据结构和ConcurrentHashMap一样，
都采用分 segment 来细化管理HashMap中的节点Entry。不同的是LocalCache 中的 ReferenceEntry节点更为复杂。

数据结构简图大致如下：
图?

- 另外视缓存是否需要使用特别的引用策略(非Strong引用).以及设置的超时过期策路不同 (expireAfterAccess,
expire AfterWrite）每个Segment中会维护最多以下5个队列：
  - keyReferenceQueue： 存储了即将被GC回收的缓存数据K引1用列表 （当缓存设置了非强引用策略时该队列生效)
  - valueReferenceQueue：存储了即将被GC回收的缓存数据Value弓1用列表（当缓存设置了非强引用策略时该队列生效）
  - recencyQueue ： 存储最近访问的Entry列表，为了提升访问性能使用的辅助队列。
  - accessQueue：存储了基于最近访问时间排序的segment中所有ReferenceEntry的一个双向链表(当选择基于访问时间失效策略的时候用到该队列)
  - WriteQueve： 存储了基于最近写入时间排序的segment中所有Entry的一个双向链表(当选择基于写入时问失效策略的时候用到该队列）
    
此外segment中还维护了2个变量count(记录当前segment中节点数量)，modcount (记录当 前segment变更次数）

## 缓存基本用法

```java
code
```
某他高级用法用法汇总:
- 通过异步自动加载实体到缓存中
- 基于大小的回收策略
- 基于时间的回收策略
- 自动刷新
- key自动封装虚引用
- value自动封装弱引用或软引1用
- 实体过期或被删除的通知
- 写入外部资源
- 统计累计访问缓存

加载策略
- 手动加载
- 同步加载
- 异步加载


#### 回收策略
Caffeine提供了 3种回收策略：基于大小回收，基于时间回收，基手号用回收

#### 统计缓存使用情况
通过使用Caffeine.recordStats...

## 总结
- 个人推荐使用 Caffeine 算法W-TinyLFU算法它的命中率非常高，内存小，异步化，不阻塞。
- 无论是读写,Caffeine性能都比 Guava要好。
- Caffeine基于java8的高性能，接近最优的缓存库。
- Caffeine提供的内存缓存使用参考 Google guava的API。
- Caffeine 是基于 Google guava 和 ConcurrentLinkedHashMap的设计经验上改进的成果。
• Caffeine 是Spring 5 默认支持的cache，可见Spring对它的看中，Spring抛弃Guava 转向了 Caffeine.
