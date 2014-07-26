title: '设计模式学习--Adapter'
date: 2014-07-26 22:40:33
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---

####**What**

> Adapter：将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。<!--more-->

####**Why**
Adpater主要用于系统接口兼容性问题，比如有一个现有的组件，它提供了我们需要的部分功能，我们就可以再该组件上封装一层Adapter以方便我们的代码调用。
以下情况适用：
1、你使用一个已存在的类，而它的接口不符合你的需求。
2、你想创建一个可以复用的类，该类可以与其他不相关的类或不可预见的类协同工作。
3、你想使用一些已经存在的子类，但是不可能对每一个都进行子类化以匹配它们的接口。
####**How**
假设如下场景：需要提供一种word下载功能，使用aspose组件生成word，需要设计一种接口，可以适用于以后更换组件，比如apache poi。
不同的组件可能提供的接口不同，调用的方式也不同，所以可以使用adapter模式来统一不同组件调用方式不同给我们带来的困扰，在更换组件时我们的代码可以不改变或者少量改变。
模拟aspose的类
```java
public class Aspose {

    public void asposeCreateDoc(){
         System.out.print("Aspose create a doc");
    }

    public void asposeCreateTable(){
        System.out.print("Aspose create a table");
    }
}
```
word操作接口
```java
public interface WordOperate {

    void createDoc();

    void createTable();
}

```
word操作的适配器
```java
public class WordOperateImpl implements WordOperate {

    private Aspose aspose=new Aspose();

    @Override
    public void createDoc(){
        aspose.asposeCreateDoc();
    }

    @Override
    public void createTable(){
        aspose.asposeCreateTable();
    }
}
```
客户端调用
```java
        WordOperate wordOperate = new WordOperateImpl();
        wordOperate.createDoc();
        wordOperate.createTable();
```
以上代码的UML类图如下：
![adapter](http://yywang.qiniudn.com/adapter.png)
####**Discuss**
在实现以上示例的过程中，从代码的角度上看，就是一个接口然后是一个接口的实现，总感觉这不是adapter模式，经过一些思考，以上假设的场景确实也适合adapter模式实现，在我们平常了开发中不能为了设计模式而使用设计模式，需要根据业务场景去分析，找到合适的实现方法。在学习设计模式的过程中，需要根据这个设计模式适用的情况，举出适用于该设计模式的业务场景，这是个相反的过程。所以每种设计模式是为了解决某种业务问题，不是类图像某个设计模式，就是设计模式。设计模式不是定律。是解决业务问题的常见方法。
adapter模式在java jdk中也有应用比如如下：
java.util.Arrays#asList() 
java.io.InputStreamReader(InputStream) 
java.io.OutputStreamWriter(OutputStream) 