title: 'JVM运行时数据区域'
date: 2014-10-18 12:58:36
categories:
	- java
	- jvm
tags:
	- java
	- jvm
---

Java虚拟机在执行java程序的过程中会把所有管理的内存划分为若干个内存区域。这些内存区域有各自的用途，创建和销毁时间也不同，有的随着虚拟机的进程启动而存在，有的依赖用户的线程的启动而建立，结束而销毁。
####内存区域划分
在java虚拟机规范中，java虚拟机管理的内存区域将会划分为如下运行时数据区域：Method Area，VM stack,Native Method Stack,Heap,Program Counter Register。
![Runtime data areas](http://yywang.qiniudn.com/jvmruntimearea.png)
<!--more-->
1. Program Counter Register：它是一块较小的内存空间，它可以看到当前线程所执行的字节码的行号指示器。每个java虚拟机线程都有自己的Program Counter Register，在虚拟机的工作中，就是通过改变这个计数器值来执行下一条字节码指令的。像分支、循环、跳转、异常处理、线程恢复等基础功能都依赖这个功能实现。

2. VM stack：每个线程都有自己的VM stack，生命周期和线程相同，VM stack主要用来存放栈帧，用于存储局部变量表，操作数栈、动态链接、方法出口等信息，每个方法的从调用到执行完成就是一个入栈出栈的过程。局部变量表存放了编译是就可预知的基础数据类型、reference类型，和returnAddress类型。在编译是就可知道局部变量表的大小，在执行时不需要改变大小。
在java虚拟机规范中，VM stack规定了两种异常：

	- StackOverflowError：线程请求的栈深入大于虚拟机所允许的栈深度。(方法循环依赖）
	- OutOfMemoryError：如果虚拟机栈可以动态扩展，如果扩展无法申请足够的内存。（物理内存过小）


3. Native Method Stack：和VM stack类似，区别在于VM stack为虚拟机执行java方法服务，而Native Method Stack为调用本地的方法服务。对本地方法实现的语言和数据结构没有限制。

	- StackOverflowError：如果线程请求分配的栈容量超过本地方法栈允许的最大容量时
	- OutOfMemoryError：如果可以动态扩展，如果扩展无法申请足够的内存

4. Heap：在虚拟机启动时被创建，几乎所有的对象实例都在这里分配，它也是拉机器收集器管理的主要区域，在java虚拟机规定中，Heap可以处在物理内存不连续的空间中，只要逻辑内存联系即可。

	- OutOfMemoryError：实际所需的堆超过了自动内存管理系统提供的最大容量。

5. Method Area：和Heap也是各个线程共享的区域，它用于存储已被虚拟机加载的类的信息、常量、静态变量、即时编译器后的编译代码等。在java虚拟机规范中，方法区不实现垃圾收集。

	- OutOfMemoryError：如果方法区的内存空间不能满足内存分配请求。

####详解VM stack
栈帧：用于存储数据和部分过程结果的数据结构，同时被用来处理链接、方法返回值和异常分派。
  创建：随着方法的调用而创建，随着方法的结束而销毁（无论方法是正常完成还是异常完成）
  存储：VM stack中
  内容：局部变量表，操作数栈，指向当前方法所属的类运行时常量池的引用。

1. 局部变量表：
大小：局部变量表的大小可以在编译期决定。存储在类和接口的二进制中。
内容：基础数据类型、reference类型，和returnAddress类型
局部变量使用索引进行定位访问，第一个局部变量为0，long和double的类型数据占用两个连续的局部变量，其中较小的存放数据值
java虚拟机使用局部变量表来完成方法的调用时的参数传递，当一个方法被调用的时候，它的参数将会传递至0开始的连续的局部变量表的位置上。当一个实例方法被调用的时候，第0个局部变量一定是用来存储调用的实例方法所在的对象的引用。后续其他参数从第1个变量开始。
2. 操作数栈：
每个栈帧包括一个操作数栈。同样也是长度也是由编译期决定。在创建时是一个空栈，由虚拟机的一些指令从局部变量表或者对象实例的字段中复制常量或者变量的值到操作栈中，也提供了一些指令用于取出数据，操作数据把结果重新入栈。
3. 动态链接：每个栈帧内存都包含一个指向运行时常量池的引用支持当前方法的代码实现动态链接。在Class文件里，描述一个方法调用了其他方法或者访问其成员变量都是通过符号引用来表示，动态链接的作用就是把这些所表示的方法转换为实际的方法的直接引用。
4. 方法正常调用完成：方法调用完成时没有任何异常抛出。这是当前栈帧还承担着回复调用者状态的责任，其状态包括调用者的局部操作变量表、操作数栈和被正确增加过来表示执行该方法调用指令的程序计数器等，返回值推入调用者栈帧的操作数栈后正常执行。
5. 方法异常调用完成：方法在执行的过程中，抛出异常。

