title: 'JDK那些有用的工具'
date: 2014-11-08 23:41:31
categories:
	- java
tags:
	- java
---

在jdk中，无论是刚开始学习java的学生还是在职场叱咤多年的java程序员，肯定都知道bin目录里的java.exe以及javac.exe这两个命令行工具。但是有没有发现在bin目录除了这两个命令还有不少其他的命令。它们有什么作用呢？今天主要介绍这里面的一些工具。

#####jps虚拟机进程状态工具
* 功能：可以列出正在运行的虚拟机进程，并显示虚拟机执行主类名称以及这些进程的本地虚拟机唯一ID.
* 命令格式：jps [options] [hostid]
* 参数：-l 输出主类全名，如果进程执行的是jar包，输出jar路径  -v 输出虚拟机进程启动时jvm参数

![jps](http://yywang.qiniudn.com/tools-jps.png)<!--more-->

#####jstat虚拟机统计信息监控工具
* 功能：用于监视虚拟机各种运行状态信息的命令行工具。它可以显示本地或者远程虚拟机进程中的类装载、内存、垃圾收集、JIT编译等运行数据。它是运行期间定位虚拟机性能问题的首选工具。
* 命令格式：jstat [option vmid [interval[s|ms] [count]]]
* 参数
  -class 监视装载、卸载数量、总空间以及类装载所耗费的时间。
  -gc 监视java堆状况，包括Eden区、两个survivor区、老年代、永久代等容量、已用空间、以及gc时间合计等信息
  -gcutil 主要关注已使用和总空间的百分比。
  -gccapacity 主要关注各个区域使用的最大、最小空间。
  -gccause 能够输出上次gc产生的原因。
  另外查看新生代的相关信息使用 -gcnew* 查看老生代信息使用-gcold*

![jstat](http://yywang.qiniudn.com/tools-jstat.png)
以上红框部分，gcutil输出信息解释
新生代：s0表示survivor0区、s1表示survivor1区、E表示Enden区占28.86%
老生代以及永久代码：O表示老生代占50.52% 永久代占90.34%
YGC（Young GC）共发生17次，YGCT 耗时1.102秒 Full GC共5次，Full GC耗时0.876s,GC总耗时：1.978s
#####jmap：java内存映射工具
* 功能：可以生成堆的转储快照。
* 命令格式：jmap [option] vmid
* 参数：-dump 生成java堆转存快照，格式如：jmap -dump:format=b,file=test 7236
        -heap 显示java堆详细信息，如哪种回收器、参数配置、分代情况等。

![jmap](http://yywang.qiniudn.com/tools-jmap.png)

#####jhat：虚拟机堆转存储快照分析工具
* 功能：和jmap搭配使用，分析虚拟机堆转存储快照分析工具，内置了微型http服务器，生成的分析结果后使用浏览器查看。功能比较简陋，尽量使用其他替代品。

#####jstack：java堆栈跟踪工具
* 功能：用于生成虚拟机当前时刻的线程快照。主要用来定位线程长时间停顿的原因，比如线程死锁、死循环、请求外部资源导致长时间等待等常见原因
* 命令格式：jstack [option] vmid
* 参数：
  -F 当正常输出的请求不被响应时，强制输出线程堆栈。
  -l 除堆栈外，显示关于锁的附加信息
  -m 如果调用本地方法的话，可以显示c/c++的堆栈。

#####JConsole虚拟机监控工具
在cmd中输入jconsole 就可以启动JConsole工具
![jconsole](http://yywang.qiniudn.com/JConsole.png)
有概要、内存、线程、类、vm概要这几个tab,
概要tab：主要显示堆内存使用量、线程、类加载数、CPU利用率等。
内存tab：可以显示各个分区的占用情况，以及详细情况。
线程tab：详细显示线程数情况。
类tab: 详细显示已加载的类。
#####更强大的VisualVM
在上面都是用命令形式查看，jvm在运行期间的各项参数，上面的所有命令查询的信息都可以通过VisualVM来查看。还支持链接远程的JVM。


详细的使用方法，在以后的学习中继续总结。

排查线上性能问题，我们就需要以上的工具，今天主要是简单了解一些其功能，在出现问题时，能够想到使用这些工具来分析，详细使用方法，后面继续学习。