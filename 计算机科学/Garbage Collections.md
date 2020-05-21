### 分类

|收集器名称|区域|说明|
| :--------  | :-----  | :---- |
|Serial|新生代|单线程，GC时必须停止其它线程直到收集结束；JVM运行在client模式下新生代的默认收集器，简单有效；采用复制算法|
|ParNew|新生代|Serial收集的多线程版，保留Serial的参数控制，算法等，暂停所有用户线程，采用复制算法；JVM运行在server的首先的新生代收集器；只有它能和CMS配合工作|
|Parallel Scavenge|新生代|采用复制算法，并行的多线程收集器，与ParNew不同的是，关注点不是停顿时间，而是可控制的吞吐量，即运行用户代码的时间/（运行用户代码的时间+垃圾收集的时间）。可设置最大GC时间和吞吐量大小等参数，也可以让JVM自适应调整策略|
|G1|新生代/老年代|收集器最前沿版本，JDK 1.7，代替CMS的新产品|
|CMS|新生代|concurrent Mark Sweep，已获取最短回收停顿为目标，大部分的互联网站及服务端采用的方式，标记-清除算法|
|Serial Old（MSC）|老年代|Serial的老年版，单线程收集器，采用标记-整理算法，主要是client模式的JVM使用|
|Parallel Old|老年代|Parallel Scavenge的老年版，多线程，标记整理算法|

### 搭配

| 新生代 | 老年代 | 说明 |
| :--------  | :-----  | :---- |
|Serial|Serial Old（MSC）|新生代采用复制算法，暂停所有用户线程,单线程；老年代采用标记-整理算法，暂停所有用户线程,单线程|
|Serial|CMS|新生代采用复制算法，暂停所有用户线程,单线程；老年代，采用CMS收集器，初始标记（单线程，stopTheWorld），重新标记（并发标记），重新标记（多线程，stopTheWorld），并行清除（并行清理，标记-清理算法）|
|ParNew|Serial Old（MSC）|新生代采用复制算法，stopTheWorld，多线程；老年代MSC同上|
|ParNew|CMS|新生代同上ParNew，老年代同上CMS|
|Parallel|Scavenge|Serial Old（MSC）	Parallel Scavenge收集方式和ParNew一致，重点关注吞吐量，但被MSC限制|
|Parallel|Scavenge|Parallel Old	老年代，多线程，标记-整理算法，吞吐量优先组合|
|G1|G1|Java堆的内存布局有很大的不同，划分成多个大小相等的独立区域Region，新生代和老年代不在物理隔离，能有效避免整个Java堆全区域的GC|

