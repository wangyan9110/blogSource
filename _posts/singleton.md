title: '设计模式学习--Singleton'
date: 2014-07-12 21:38:23
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---
###**What**

> Singleton：保证一个类仅有一个实例，并提供一个访问它的全局访问点。<!--more-->

###**Why**
Singletion是我比较熟悉的设计模式之一，在平常的开发过程中，也曾几次用到，它主要适用于如下场景：
1、当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时。
2、当这个唯一实例应该是通过子类可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。
在系统设计中，在涉及系统资源的管理时，往往会被设计成Singletion模式，比如缓存、日志对象、线程池、对话框等等。
###**How**
假设如下场景：一个简单的线程池，需要实现增加线程以及获取单个线程的功能。显然这个线程池对象是一个Singletion对象。
简单的实现代码如下：
```java
public class ThreadPool {

    private List<Runnable> threads=new ArrayList<Runnable>();

    private static ThreadPool threadPool=null;

    private ThreadPool(){

    }

    public static ThreadPool getInstance(){
        if(threadPool==null){
            threadPool=new ThreadPool();
        }
        return threadPool;
    }

    public void add(Runnable thread){
        System.out.append("add a thread!");
        threads.add(thread);
    }
}
```
客户端调用
```java
        ThreadPool threadPool=ThreadPool.getInstance();
        threadPool.add(new Thread());
```
以上代码类图如下：
![singleton](http://yywang.qiniudn.com/singleton.png)

###**Discuss**
####**线程安全的Singleton实现**
以上代码，实现的是一个学习意义上的版本，在实际生产中，在一些情况下会出现问题，在多线程情况下，如下代码会出现什么问题?
```java
    public static ThreadPool getInstance(){
        if(threadPool==null){
            threadPool=new ThreadPool();
        }
        return threadPool;
    }
```
在多线程条件下，当一个线程执行到new ThreadPool()但是还没返回给threadPool，这时thread=null,另一个线程也会进入if代码片段，这样就创建了两个threadPool，造成这个的原因就是这段代码是线程非安全的。
经过优化形成如下版本：
```java
public static ThreadPool getInstance() {
        
        synchronized (ThreadPool.class) {
            if (threadPool == null) {
                threadPool = new ThreadPool();
            }
        }
        return threadPool;
    }
```
这个版本可以很好的解决上个版本的问题，在一个线程进入了synchronized代码块，另一个线程就会等待。但是仔细想想，如果对象已经创建，线程还是需要等待进入synchronized代码块才会知道threadPool!=null，这样会造成比较严重性能问题，再来一个版本
```java
public static ThreadPool getInstance() {
        if (threadPool == null) {
            synchronized (ThreadPool.class) {
                if (threadPool == null) {
                    threadPool = new ThreadPool();
                }
            }
        }
        return threadPool;
    }
```
ok,这个版本看上去完美了，可以在生产中使用了。这是线程安全的实现。
以上代码还是可以通过一些办法在一个JVM中创建多个ThreadPool实例，想想是什么？对，可以通过反射的方式来，创建n多个实例，java的反射机制可以通过private的构造器创建实例。
####**使用枚举实现Singleton**
Effectvie java的作者Joshua Bloch提出了一个可以绝对防止多次实例化，而且无偿的提供了序列化机制的方法，使用枚举实现Singleton，当然java版本需要在1.5以上，下面是以上示例的使用枚举的实现
```java
public enum ThreadPool {

    Instance;

    private List<Runnable> threads=new ArrayList<Runnable>();

    public void add(Runnable thread){
        System.out.append("add a thread!");
        threads.add(thread);
    }
}
```
客户端调用
```java
 ThreadPool.Instance.add(new Thread());
```
可以看出使用枚举方式，代码比较简洁而且可以绝对防止多次实例化，是一个实现Singleton的非常好的方法。