###### 内存设置

-Xms 初始堆大小，JVM 启动的时候，给定堆空间大小。
-Xmx 最大堆大小，JVM 运行过程中，如果初始堆空间不足的时候，最大可以扩展到多
少。

-Xmn 设置年轻代大小。整个堆大小=年轻代大小+年老代大小+持久代大小。持久代一
般固定大小为 64m，所以增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，
Sun 官方推荐配置为整个堆的 3/8。
-Xss 设置每个线程的 Java 栈大小。JDK5.0 以后每个线程 Java 栈大小为 1M，以前每
个线程堆栈大小为 256K。根据应用的线程所需内存大小进行调整。在相同物理内存下，减
小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限
生成，经验值在 3000~5000 左右。

-XX:NewSize=n 设置年轻代大小
-XX:NewRatio=n 设置年轻代和年老代的比值。如:为 3，表示年轻代与年老代比值为 1：3，年轻代占整个年轻代+年老代和的 1/4
-XX:SurvivorRatio=n 年轻代中 Eden 区与两个 Survivor 区的比值。注意 Survivor 区有两个。
如：3，表示 Eden：Survivor=3：2，一个 Survivor 区占整个年轻代的 1/5
-XX:MaxPermSize=n:设置持久代大小
-XX:MaxTenuringThreshold 设置垃圾最大年龄。如果设置为 0 的话，则年轻代对象不经
过 Survivor 区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设
置为一个较大值，则年轻代对象会在 Survivor 区进行多次复制，这样可以增加对象再年轻代
的存活时间，增加在年轻代即被回收的概率。

###### 内存设置经验分享

JVM 中最大堆大小有三方面限制：相关操作系统的数据模型（32-bt 还是 64-bit）限制；
系统的可用虚拟内存限制；系统的可用物理内存限制。32 位系统 下，一般限制在 1.5G~2G；
64 为操作系统对内存无限制。
Tomcat 配置方式： 编写 catalina.bat|catalina.sh，增加 JAVA_OPTS 参数设置。windows
和 linux 配置方式不同。windows - set "JAVA_OPTS=%JAVA_OPTS% 自定义参数"；linux -
JAVA_OPTS="$JAVA_OPTS 自定义参数"
常见设置：
-Xmx3550m -Xms3550m -Xmn2g -Xss128k 适合开发过程的测试应用。要求物理内存大于
4G。
-Xmx3550m -Xms3550m -Xss128k -XX:NewRatio=4 -XX:SurvivorRatio=4 
-XX:MaxPermSize=160m -XX:MaxTenuringThreshold=0 适合高并发本地测试使用。且大数据对
象相对较多（如 IO 流）
环境： 16G 物理内存，高并发服务，重量级对象中等（线程池，连接池等），常用对象
比例为 40%（运行过程中产生的对象 40%是生命周期较长的）
-Xmx10G -Xms10G -Xss1M -XX:NewRatio=3 -XX:SurvivorRatio=4 -XX:MaxPermSize=2048m 
-XX:MaxTenuringThreshold=5

收集器设置
收集器配置的时候，次收集器和全收集器必须匹配。具体匹配规则参考 3.1.3
-XX:+UseSerialGC:设置串行收集器，年轻带收集器， 次收集器
-XX:+UseParallelGC:设置并行收集器
-XX:+UseParNewGC:设置年轻代为并行收集。可与 CMS 收集同时使用。JDK5.0 以上，JVM
会根据系统配置自行设置，所以无需再设置此值。
-XX:+UseParallelOldGC:设置并行年老代收集器，JDK6.0 支持对年老代并行收集。
-XX:+UseConcMarkSweepGC:设置年老代并发收集器，测试中配置这个以后，-XX:NewRatio
的配置失效，原因不明。所以，此时年轻代大小最好用-Xmn 设置。
-XX:+UseG1GC:设置 G1 收集器

垃圾回收统计信息
类似日志的配置信息。会有控制台相关信息输出。 商业项目上线的时候，不允许使用。
一定使用 loggc
-XX:+PrintGC
-XX:+Printetails
-XX:+PrintGCTimeStamps
-Xloggc:filename

并行收集器设置
-XX:ParallelGCThreads=n:设置并行收集器收集时最大线程数使用的 CPU 数。并行收集线
程数。
-XX:MaxGCPauseMillis=n:设置并行收集最大暂停时间，单位毫秒。可以减少 STW 时间。
-XX:GCTimeRatio=n:设置垃圾回收时间占程序运行时间的百分比。公式为 1/(1+n)并发收
集器设置
-XX:+CMSIncrementalMode:设置为增量模式。适用于单 CPU 情况。
-XX:+UseAdaptiveSizePolicy：设置此选项后，并行收集器会自动选择年轻代区大小和相应
的 Survivor 区比例，以达到目标系统规定的最低相应时间或者收集频率等，此值建议使用并
行收集器时，一直打开。
-XX:CMSFullGCsBeforeCompaction=n：由于并发收集器不对内存空间进行压缩、整理，所
以运行一段时间以后会产生“碎片”，使得运行效率降低。此值设置运行多少次 GC 以后对内
存空间进行压缩、整理。
-XX:+UseCMSCompactAtFullCollection：打开对年老代的压缩。可能会影响性能，但是可
以消除碎片

收集器设置经验分享
关于收集器的选择 JVM 给了三种选择：串行收集器、并行收集器、并发收集器，但是
串行收集器只适用于小数据量的情况，所以这里的选择主要针对并行收集器和并发收集器。
默认情况下，JDK5.0 以前都是使用串行收集器，如果想使用其他收集器需要在启动时加入相
应参数。JDK5.0 以后，JVM 会根据当前系统配置进行判断。
常见配置：
并行收集器主要以到达一定的吞吐量为目标，适用于科学计算和后台处理等。
-Xmx3800m -Xms3800m -Xmn2g -Xss128k -XX:+UseParallelGC -XX:ParallelGCThreads=20
使用 ParallelGC 作为并行收集器， GC 线程为 20（CPU 核心数>=20 时），内存问题根据
硬件配置具体提供。建议使用物理内存的 80%左右作为 JVM 内存容量。
-Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:+UseParallelGC -XX:ParallelGCThreads=20
-XX:+UseParallelOldGC
指定老年代收集器，在JDK5.0之后的版本，ParallelGC对应的全收集器就是ParallelOldGC。
可以忽略
-Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:+UseParallelGC -XX:MaxGCPauseMillis=100
指定 GC 时最大暂停时间。单位是毫秒。每次 GC 最长使用 100 毫秒。可以尽可能提高
工作线程的执行资源。
-Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:+UseParallelGC -XX:MaxGCPauseMillis=100
-XX:+UseAdaptiveSizePolicy
UseAdaptiveSizePolicy 是提高年轻代 GC 效率的配置。次收集器执行效率。
并发收集器主要是保证系统的响应时间，减少垃圾收集时的停顿时间。适用于应用服务
器、电信领域、互联网领域等。
-Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:ParallelGCThreads=20
-XX:+UseConcMarkSweepGC -XX:+UseParNewGC
指定年轻代收集器为 ParNew，年老代收集器 ConcurrentMarkSweep，并发 GC 线程数为
20（CPU 核心>=20），并发 GC 的线程数建议使用（CPU 核心数+3）/4 或 CPU 核心数【不推
荐使用】。
-Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:+UseConcMarkSweepGC
-XX:CMSFullGCsBeforeCompaction=5 -XX:+UseCMSCompactAtFullCollection
CMSFullGCsBeforeCompaction=5 执行 5 次 GC 后，运行一次内存的整理。
UseCMSCompactAtFullCollection 执行老年代内存整理。可以避免内存碎片，提高 GC 过
程中的效率，减少停顿时间。

简单总结
年轻代大小选择
响应时间优先的应用：尽可能设大，直到接近系统的最低响应时间限制（根据实际情
况选择）。在此种情况下，年轻代收集发生的频率也是最小的。同时，减少到达年老代的对
象。
吞吐量优先的应用：尽可能的设置大，可能到达 Gbit 的程度。因为对响应时间没有要
求，垃圾收集可以并行进行，一般适合 8CPU 以上的应用。
年老代大小选择
响应时间优先的应用：年老代使用并发收集器，所以其大小需要小心设置，一般要考
虑并发会话率和会话持续时间等一些参数。如果堆设置小了，可以会造成内存碎片、高回
收频率以及应用暂停而使用传统的标记清除方式；如果堆大了，则需要较长的收集时间。
最优化的方案，一般需要参考以下数据获得：
并发垃圾收集信息
持久代并发收集次数
传统 GC 信息
花在年轻代和年老代回收上的时间比例
减少年轻代和年老代花费的时间，一般会提高应用的效率
吞吐量优先的应用：一般吞吐量优先的应用都有一个很大的年轻代和一个较小的年老
代。原因是，这样可以尽可能回收掉大部分短期对象，减少中期的对象，而年老代存放长期
存活对象。
较小堆引起的碎片问题，因为年老代的并发收集器使用标记、清除算法，所以不会对堆
进行压缩。当收集器回收时，他会把相邻的空间进行合并，这样可以分配给较大的对象。但
是，当堆空间较小时，运行一段时间以后，就会出现“碎片”，如果并发收集器找不到足够的
空间，那么并发收集器将会停止，然后使用传统的标记、整理方式进行回收。如果出现“碎
片”，可能需要进行如下配置：
-XX:+UseCMSCompactAtFullCollection：使用并发收集器时，开启对年老代的压缩。
-XX:CMSFullGCsBeforeCompaction=0：上面配置开启的情况下，这里设置多少次 Full GC
后，对年老代进行压缩