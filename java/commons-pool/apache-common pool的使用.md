# apache-common pool的使用

## 一、对象的生命周期

commons-pool实现思想非常简单,它主要的作用就是将"对象集合"池化,任何通过pool进行对象存取的操作,都会严格按照"pool配置"(比如池的大小)实时的创建对象/阻塞控制/销毁对象等.它在一定程度上,实现了对象集合的管理以及对象的分发.

- 1) 将创建对象的方式,使用工厂模式;
- 2) 通过"pool配置"来约束对象存取的时机
- 3) 将对象列表保存在队列中(LinkedList)

### 获取对象

```
graph TD
A[获取对象] --> B{对象池是否为空}
B --> |是| C{活跃对象个数是否达到maxActive}
C --> |否| D[创建对象]
C --> |是| E((阻塞-直到有对象归还对象池))
B --> |否| F[活跃对象个数+1]
D --> F
E --> F
F --> G[从pool中移除对象]
```

### 归还对象

```
graph TD
A[归还对象] --> B{检查对象是否有效}
B --> |是| D{对象池中idle的对象个数是否达到maxIdle}
D --> |否| C[活跃对象个数-1]
C --> F[将对象添加到pool]
D --> |是| E[销毁对象]
B --> |否| E[销毁对象]
E --> C
F --> G((唤醒阻塞))
```

## 二.Config详解:

这些属性均可以在org.apache.commons.pool.impl.GenericObjectPool.Config中进行设定。

属性 | 意义
--- | ---
maxActive | 链接池中最大连接数,默认为8.
maxIdle | 链接池中最大空闲的连接数,默认为8.
minIdle | 连接池中最少空闲的连接数,默认为0.
maxWait | 当连接池资源耗尽时，调用者最大阻塞的时间，超时将跑出异常。
| | 单位，毫秒数;默认为-1.表示永不超时.
minEvictableIdleTimeMillis |  连接空闲的最小时间，达到此值后空闲连接将可能会被移除。负值(-1)表示不移除。
softMinEvictableIdleTimeMillis | 连接空闲的最小时间，达到此值后空闲链接将会被移除，且保留“minIdle”个空闲连接数。默认为-1.
numTestsPerEvictionRun | 对于“空闲链接”检测线程而言，每次检测的链接资源的个数。默认为3.
testOnBorrow | 向调用者输出“链接”资源时，是否检测是有有效，如果无效则从连接池中移除，并尝试获取继续获取。
| | 默认为false。建议保持默认值.
testOnReturn | 向连接池“归还”链接时，是否检测“链接”对象的有效性。
| | 默认为false。建议保持默认值.
testWhileIdle | 向调用者输出“链接”对象时，是否检测它的空闲超时；默认为false。
| | 如果“链接”空闲超时，将会被移除。建议保持默认值.
timeBetweenEvictionRunsMillis | “空闲链接”检测线程，检测的周期，毫秒数。如果为负值，表示不运行“检测线程”。默认为-1.
whenExhaustedAction | 当“连接池”中active数量达到阀值时，即“链接”资源耗尽时，连接池需要采取的手段, 
| | 默认为1
| | -> 0 : 抛出异常，
| | -> 1 : 阻塞，直到有可用链接资源
| | -> 2 : 强制创建新的链接资源

## 三.原理解析

### 1) 对象池创建(参考GenericObjectPool):

#### public GenericObjectPool(PoolableObjectFactory factory, GenericObjectPool.Config config) : 

此方法创建一个GenericObjectPool实例,GenericObjectPool类已经实现了和对象池有关的所有核心操作,开发者可以通过继承或者封装的方式来使用它.通过此构造函数,我们能够清晰的看到,一个Pool中需要指定PoolableObjectFactory 实例,以及此对象池的Config信息.PoolableObjectFactory主要用来"创建新对象",比如当对象池中的对象不足时,可以使用 PoolableObjectFactory.makeObject()方法来创建对象,并交付给Pool管理.

此构造函数实例化了一个LinkedList作为"对象池"容器,用来存取"对象".此外还会根据timeBetweenEvictionRunsMillis的值来决定是否启动一个后台线程,此线程用来周期性扫描pool中的对象列表,已检测"对象池中的对象"空闲(idle)的时间是否达到了阀值,如果是,则移除此对象.
    
### 2) 对象工厂PoolableObjectFactory接口:

commons-pool通过使用ObjectFactory(工厂模式)的方式将"对象池中的对象"的创建/检测/销毁等特性解耦出来,这是一个非常良好的设计思想.此接口有一个抽象类BasePoolableObjectFactory,可供开发者继承和实现.

#### Object makeObject()

创建一个新对象;当对象池中的对象个数不足时,将会使用此方法来"输出"一个新的"对象",并交付给对象池管理.

#### void destroyObject(Object obj) : 

销毁对象,如果对象池中检测到某个"对象"idle的时间超时,或者操作者向对象池"归还对象"时检测到"对象"已经无效,那么此时将会导致"对象销毁";"销毁对象"的操作设计相差甚远,但是必须明确:当调用此方法时,"对象"的生命周期必须结束.如果object是线程,那么此时线程必须退出;如果object是socket操作,那么此时socket必须关闭;如果object是文件流操作,那么此时"数据flush"且正常关闭.

#### boolean validateObject(Object obj) : 

检测对象是否"有效";Pool中不能保存无效的"对象",因此"后台检测线程"会周期性的检测Pool中"对象"的有效性,如果对象无效则会导致此对象从Pool中移除,并destroy;此外在调用者从Pool获取一个"对象"时,也会检测"对象"的有效性,确保不能讲"无效"的对象输出给调用者;当调用者使用完毕将"对象归还"到Pool时,仍然会检测对象的有效性.所谓有效性,就是此"对象"的状态是否符合预期,是否可以对调用者直接使用;如果对象是Socket,那么它的有效性就是socket的通道是否畅通/阻塞是否超时等.

#### void activateObject(Object obj) : 

"激活"对象,当Pool中决定移除一个对象交付给调用者时额外的"激活"操作,比如可以在activateObject方法中"重置"参数列表让调用者使用时感觉像一个"新创建"的对象一样;如果object是一个线程,可以在"激活"操作中重置"线程中断标记",或者让线程从阻塞中唤醒等;如果 object是一个socket,那么可以在"激活操作"中刷新通道,或者对socket进行链接重建(假如socket意外关闭)等.

#### void void passivateObject(Object obj) : 

"钝化"对象,当调用者"归还对象"时,Pool将会"钝化对象";钝化的言外之意,就是此"对象"暂且需要"休息"一下.如果object是一个 socket,那么可以passivateObject中清除buffer,将socket阻塞;如果object是一个线程,可以在"钝化"操作中将线程sleep或者将线程中的某个对象wait.需要注意的时,activateObject和passivateObject两个方法需要对应,避免死锁或者"对象"状态的混乱.


### 3) ObjectPool接口与实现

对象池是commons-pool的核心接口,用来维护"对象列表"的存取;其中GenericObjectPool是其实现类,它已经实现了相关的功能.

#### Object borrowObject() : 

从Pool获取一个对象,此操作将导致一个"对象"从Pool移除(脱离Pool管理),调用者可以在获得"对象"引用后即可使用,且需要在使用结束后"归还".

#### void returnObject(Object obj) : 

"归还"对象,当"对象"使用结束后,需要归还到Pool中,才能维持Pool中对象的数量可控,如果不归还到Pool,那么将意味着在Pool之外,将有大量的"对象"存在,那么就使用了"对象池"的意义.如下为伪代码:

#### void invalidateObject(Object obj) : 

销毁对象,直接调用ObjectFactory.destroyObject(obj);.

#### void addObject() : 

开发者可以直接调用addObject方法用于直接创建一个"对象"并添加到Pool中.