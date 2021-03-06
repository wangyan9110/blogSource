title: 'JVM垃圾收集器'
date: 2014-10-22 23:14:04
categories:
	- java
	- jvm
tags:
	- java
	- jvm
---

###**对象已死？**

##### 引用计数算法

* 概念：给对象添加一个引用计数器，每当有地方引用它时，计数器就加1，当引用失效时，计数器就减，任何时刻计数器为0的对象就是不可能再被使用的。
* 优点：简单、高效
* 缺点：循环引用无法回收，如objA.instance=objB,ObjB.ins=objA<!--more-->

##### 可达性分析算法

* 概念：从一个“GC Root”的对象作为起点，从这些节点开始向下搜索，搜索所走过的路径的引用链，当一个对象到GC Roots没有任何引用链相连时，则证明这个对象被判“死缓”
* Java,C#等语言都是通过这种方式确定对象不再使用。
* 在虚拟机中，可达性分析是一个敏感的时间点，在可达性分析中，必须保证在一个能确保的一致性，这种一致性需要把所有对象冻结在一个时间点，这时需要“Stop the Word”，在枚举GC Root时，即便目前最先进的虚拟机也需要“Stop the word”，只是在尽量的减少而已。

##### 死还是不死
被判“死缓”还不一定死，还要看它在缓期间的情况，它还需要至少两次的标记过程，第一次标记看是否有必要执行finalize()方法。在对象没有覆盖finalize()或者虚拟机调用过finalize()方法，则不要执行，如果确定需要执行，则加入一个叫F-Queue的队列之中。这是第一次标记。虚拟机会建立一个Finalizer线程区执行它，如果在finalize()中把自己关联到GC Root上则可以“改判”，否则它将被移出“即将回收”集合，基本上华佗也救不了它了。
如果在finalize()中把自己关联到GC Root上，就可以逃脱死亡的命运，但是在实际的编码中，不推荐使用。

###**垃圾回收算法**

##### 标记-清除算法

* 算法：首先标记需要清除的所有对象，在标记完成后统一回收所有被标记的对象。所有垃圾回收算法的基础算法。
* 缺点：标记清楚效率较低，而空间碎片化，难以存储较大对象。
其他的算法主要围绕这两个缺点解决问题。

##### 复制算法

* 算法：把内存平均分成两块，先用其中一块，当回收时把可用的对象复制到另一块，然后清理该部分内存。
* 优点：实现简单、高效，不需要考虑内存碎片问题
* 缺点：内存利用率低
* 应用：目前主要使用收集新生代对象。新生代对象“朝生梦死”，内存不一定按照1:1划分，可以把可用对象部分内存小一些。

##### 标记-整理算法

* 算法：让存活的对象向一端移动，然后直接清理掉存活以外的对象。
* 应用：主要用于收集老年代。复制算法对于存活对象比较多的情况，代价比较大，内存利用率不高。

##### 分代收集算法

* 算法：根据存活期划分不同内存块，一般分为老年代和新生代，然后分别使用合适的算法收集。新生代一般使用复制算法，老年代使用标记-整理算法或者标记-清除算法。
* 应用：当前的商业垃圾收集器都在使用。

###**内存分配和回收策略**

内存分配主要是堆内存分配，在垃圾回收中也主要是堆内存中的对象回收，对象一般主要分配在堆内存的新生代区上。

* 对象优先分配在新生代区上
* 大对象分配到老年代区上，对于短命的大对象尽量减少使用。
* 新生代区上的对象升级到老年代区上，一般一次GC，如果没有被回收的话，对象的年龄+1，到15岁的时候可进入老年代区，可以通过-XX:TenuringThreshold设置
* 动态年龄判断，假设都根据3中的算法判断，假设如果对象有大量使用了相同年龄的对象，但都没达到15岁，那么会造成新生代的内存大小占用较大，对于相同年龄对象超过内存一半时，大于等于该年龄的对象会进入老年区。
* 空间分配担保，就像生活中的贷款，银行贷款给你也是一种风险投资，所以需要有人担保，如果你没钱，银行可以找他要，如果老年区的连续内存空间小于新生代区内存大小，垃圾回收器就需要进行一次“冒险”收集，在新生代我们使用复制算法收集，一般是不等分配，如果存活对象比较多，另一半内存无法存放全部的对象，这时就需要老年代区有足够的空间来存放剩余的对象，老年区要做“担保”