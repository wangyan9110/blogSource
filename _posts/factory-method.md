title: '设计模式学习--Factory Method'
date: 2014-06-28 22:40:42
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---
###**What**

> Factory Method：定义一个创建对象的接口，让子类来决定实例化哪一个类。Factory Method使一个类的实例化延迟到其子类。<!--more-->

###**Why**
Factory Method是一个比较基础的创建型模式，它主要在于由子类决定实例化哪一个类。主要用于框架代码或者工具包中。
适用于如下场景：
1、当一个类不知道它所必须创建的对象的类的时候
2、当一个类希望由子类来指定它所创建对象的时候
3、当类将创建对象的职责委托给多个帮助子类的某一个，并且你希望将哪一个帮助子类是代理者这一信息局部化的时候

###**How**
假设如下场景，写一个语法分析器，需要支持C与java代码的语法分析，需要完成代码语法检查和语法解析工作。以此为例实现工厂模式。
语法分析器接口
```java
public interface Parsing {

    void checkGrammar();

    void parseCode();
}
```
C代码语法分析器实现
```java
public class CParsing implements Parsing {

    @Override
    public void checkGrammar() {
        System.out.println("check c grammar");
    }

    @Override
    public void parseCode() {
        System.out.println("parse c code");
    }
}
```
Java代码语法分析器实现
```java
public class JavaParsing implements Parsing {

    @Override
    public void checkGrammar() {
        System.out.println("check java grammar");
    }

    @Override
    public void parseCode() {
        System.out.println("parse java code");
    }
}
```
解析器工厂接口
```java
public interface ParsingFactory {

    Parsing createParsing();
}
```
C代码解析器工厂实现
```java
public class CParsingFactory implements ParsingFactory {
    
    @Override
    public Parsing createParsing() {
        return new CParsing();
    }
}
```
Java代码解析器工厂实现
```java
public class JavaParsingFactory implements ParsingFactory {

    @Override
    public Parsing createParsing() {
        return new JavaParsing();
    }
}
```
客户端调用
```java
public class App {

    public static void main( String[] args ){
        ParsingFactory factory=new CParsingFactory();
        Parsing parsing=factory.createParsing();
        parsing.checkGrammar();
        parsing.parseCode();

        ParsingFactory factory1=new JavaParsingFactory();
        Parsing parsing1=factory1.createParsing();
        parsing1.checkGrammar();
        parsing1.parseCode();
    }
}
```
以上示例类图如下：
![Factory Method](http://yywang.qiniudn.com/factorymethod.png)

###**Discuss**
Abstarct Factory VS Factory Method
在学习Factory Method就对Factory Method模式和Abstarct Factory很迷惑，经过反复阅读两个章节，基本上总结出了两者的一个联系，Abstarct Factory是一个特殊的Factory Method，但是两者区别在哪呢？
完成Factory Method的示例发现，在Abstract Factory中示例的UML和Factory Method的示例的UML是结构是一样的，于是就思考为什么？
两者的意图如下：
Factory Method：定义**一个创建对象的接口**，**让子类来决定实例化哪一个类**。Factory Method使一个类的**实例化延迟到其子类**。
Abstarct Factory：提供一个创建**一系列相关或相互依赖的接口**，而无需指定他们具体类。
从以上粗体部分就可以体现其区别的地方。
1.Abstarct Factory强调的是创建一系列相关相互依赖的接口，也就是Abstarct这个工厂可以生产一系列的对象，是一个一对多的关系，然后我又重新考虑了一下，Abstarct Factory的示例，并进行了完善（详情见：[设计模式学习--Abstarct Factory的ChangeLog](http://yywang.info/2014/06/23/abstract-factory/)）。它的扩展性侧重横向扩展。比如在Abstarct Factory学习中的例子，我们又来一个RoleDao，我们是在DaoFactory中增加createRoleDao方法。
2.Factory Method强调的是创建一个对象接口，让子类来决定实例化哪一个类，也就是它的工厂和产品是一对一的关系。它的扩展性侧重的是垂直扩展，比如本节的示例，我们需要增加一个支持Sql语言的语法分析器，我需要增加一个SqlParsingFactory类和一个SqlParsing类。
关于Abstarct Factory VS Factory Method的讨论还有如下可以参考：
http://www.cnblogs.com/happyhippy/archive/2010/09/26/1836223.html
http://www.cnblogs.com/procoder/archive/2009/04/24/1442920.html
