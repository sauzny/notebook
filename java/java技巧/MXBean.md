## MXBean

JAVA 平台MXBean 是一种托管 Bean，它符合 JMX Instrumentation Specification,提供管理接口，用于监视和管理 Java 虚拟机以及 Java 虚拟机在其上运行的操作系统。

| MXBean名字 | 描述 |
|--------|--------|
| ClassLoadingMXBean | 用于 Java 虚拟机的类加载系统的管理接口。 |
| CompilationMXBean | 用于 Java 虚拟机的编译系统的管理接口。  |
| GarbageCollectorMXBean | 用于 Java 虚拟机的垃圾回收的管理接口。  |
| MemoryManagerMXBean | 内存管理器的管理接口。 |
| MemoryMXBean | 虚拟机的内存系统的管理接口。  |
| MemoryPoolMXBean | 内存池的管理接口。 |
| OperatingSystemMXBean | 用于操作系统的管理接口，Java 虚拟机在此操作系统上运行。  |
| RuntimeMXBean | Java虚拟机的运行时系统的管理接口。 |
| ThreadMXBean | Java虚拟机线程系统的管理接口。 |

以上接口所在包 `java.lang.management`，[api传送门](http://tool.oschina.net/apidocs)
