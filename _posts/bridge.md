title: '设计模式学习--Bridge'
date: 2014-07-29 23:01:15
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---
###**What**

> Brigde：将抽象部分与它的实现部分分离，使他们都可以独立的变化。<!--more-->

###**Why**
Brigde模式主要解决继承带来的耦合问题，在面向对象编程中，继承主要解决具有共同特征且存在父子关系的类的复用问题。但是有时候继承也会因为继承类比较多，带来的难以维护扩展问题，比如：我要做一个手机软件比如Reader，如果使用继承的方式的话，每个品牌的手机都继承Reader，比如XIAOMIReader,IReader等，在来一个手机游戏比如：1024,每个品牌的手机都继承1024，这样使用继承的方式就会类的"爆炸"，难以维护。
以下情况适用于Bridge:
1、你不希望在抽象和它的实现之间有一个固定的绑定关系时，例如这种情况可能是因为，在程序运行时刻实现部分可以被选中或者被切换。
2、类的抽象以及它的实现都可以通过生成子类的方法加以扩充。（以上手机软件的例子就适用于此类情况）
3、对一个抽象的实现部分的修改应对客户不产生影响
4、正如上面的手机软件的例子，业务的扩增下，会产生很多的类，这样可以把对象拆分成两个部分，（手机对象，软件对象）
5、你想在多个对象间共享实现，但同时要求客户并不知道这一点。
###**How**
假设如下场景：设计一个饮料预定系统，可乐有如下种类：大杯不加冰，小杯加冰，小杯不加冰，大杯加冰四种，实现一个预定可乐的功能。
这个场景和上面手机软件的例子类似，如果使用继承的方式，现在会有四个子类，但是如果需求增加中杯，加糖，不加糖，这样类就会爆增到12个子类，所以这时候对象就需要拆分，我们把杯子和加冰不加冰行为拆分成两个对象。
一个基础的可乐行为类
```java
public abstract class CokeImpl {

    public abstract void makeCokeImpl();
}
```
原汁原味的可乐
```java
public class NoneCokeImpl extends CokeImpl {

    @Override
    public void makeCokeImpl() {
        System.out.print("原汁原味的可乐！");
    }
}
```
加了冰的可乐
```java
public class IceCokeImpl extends CokeImpl {

    @Override
    public void makeCokeImpl() {
        System.out.print("加了冰的可乐！");
    }
}
```
可乐基础类
```java
public abstract class Coke {
    protected CokeImpl[] cokeImpls;

    public Coke(CokeImpl... cokeimpls){
        this.cokeImpls=cokeimpls;
    }

    public abstract void makeCoke();
}
```
大杯可乐
```java
public class BigCoke extends Coke {

    public BigCoke(CokeImpl... cokeImpls){
        super(cokeImpls);
    }

    @Override
    public void makeCoke() {
        System.out.println("来一大杯可乐");
        for(CokeImpl cokeImpl :cokeImpls){
            cokeImpl.makeCokeImpl();
        }
    }
}
```
小杯可乐
```java
public class SmallCoke extends Coke{

    public SmallCoke(CokeImpl... cokeImpls){
        super(cokeImpls);
    }
    @Override
    public void makeCoke() {
        System.out.println("来一小杯可乐");
        for(CokeImpl cokeImpl:cokeImpls){
            cokeImpl.makeCokeImpl();
        }
    }
}
```
客户端调用
```java
        CokeImpl IceCoke=new IceCokeImpl();
        Coke coke=new BigCoke(IceCoke);
        coke.makeCoke();
```
以上代码类图如下：
![bridge](http://yywang.qiniudn.com/bridge.png)
###**Discuss**
以上示例如果实现新增的两个需求，比如加糖，只需要增加一个加糖的CokeImpl
```java
public class SugarCokeImpl extends CokeImpl{
    @Override
    public void makeCokeImpl() {
        System.out.println("加了糖的可乐！");
    }
}
```
现在客户需要一大杯加冰加糖的可乐，实现代码如下：
```java
Coke bigCoke=new BigCoke(new SugarCokeImpl(),new IceCokeImpl());
bigCoke.makeCoke();
```
从此可以看出可乐的量（大杯，小杯）和它的行为（加糖，加冰）分离给我们带来的扩展性的好处，在支持不同的需求，可以非常灵活的实现。当然还是那句话，具体业务具体分析，模式不是定理。