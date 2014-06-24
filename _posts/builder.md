title: '设计模式学习--Builder'
date: 2014-06-24 20:19:01
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---

###**What**

> Builder：将一个复杂的对象的构建和表示分离，使得同样的构建过程可以创建不同的表示。<!--more-->

###**Why**
Builder也是创建型模式的一种，它是一步一步的向导式的创建一个复杂的对象，Builder接口定义创建复杂对象的零部件，Director根据客户端端传入的builder按照一定的步骤创建完成复杂对象的创建。
Builder适用于比较复杂的对象的创建，该对象的创建有比较稳定的步骤或者比较稳定的“零件”，但是“零件”（步骤）内部的构建是复杂多变的。
设计模式书中Builder适用于如下情况：
1、当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时。
2、当构造过程必须允许被构造的对象有不同的表示时。

###**How**
假设如下场景，需要构造一个汽车类，汽车有轮子，方向盘，发动机，不同品牌的汽车零件算法不同。以此为示例，使用builder模式实现。
汽车builder接口
```java
public abstract class CarBuilder {

    protected Car car=null;

    public abstract void buildWheel();

    public abstract void buildEngine();

    public abstract void buildSteeringWheel();

    public Car getResult(){
        return car;
    }
}
```
Jeep汽车builder实现
```java
public class JeepCarBuilder extends CarBuilder {

    @Override
    public void buildWheel() {
        System.out.println("construct jeep car wheel");
    }

    @Override
    public void buildEngine() {
        System.out.println("construct jeep car engine");
    }

    @Override
    public void buildSteeringWheel() {
        System.out.println("construct jeep car steering wheel");
    }
}
```
Chery汽车builder实现
```java
public class CheryCarBuilder extends CarBuilder {

    @Override
    public void buildWheel() {
        System.out.println("construct chery car wheel");
    }

    @Override
    public void buildEngine() {
        System.out.println("construct chery car engine");
    }

    @Override
    public void buildSteeringWheel() {
        System.out.println("construct chery steering wheel");
    }
}
```
Director实现
```java
public class CarDirector {

    private CarBuilder builder;

    public CarDirector(CarBuilder builder){
        this.builder=builder;
    }

    public Car construct(){
        builder.buildSteeringWheel();
        builder.buildEngine();
        builder.buildWheel();
        return builder.getResult();
    }
}
```
Client调用
```java
public class App {

    public static void main( String[] args ){

        CarBuilder builder=new JeepCarBuilder();
        CarDirector director=new CarDirector(builder);
        director.construct();

        CarBuilder builder1=new CheryCarBuilder();
        CarDirector director1=new CarDirector(builder1);
        director1.construct();
    }
}
```
本示例类图如下：
![builder](http://yywang.qiniudn.com/builder.png)
###**Discuss**
Builder模式的好处是使建造代码与表示代码分离，如果需要增加系列产品，只需要增加相应的builder接口实现即可，如果需要改变产品的表示，也只需修改builder接口的实现即可。
在jdk中,StringBuilder是一个简易版的builder模式，其中StringBuilder充当了builder以及construct的角色，Client充当了Director。
类图如下（来自happyhippy's Blog）：
![stringbudiler](http://images.cnblogs.com/cnblogs_com/happyhippy/WindowsLiveWriter/Builder_B391/image_4.png)
###**Reference**
1、[Builder模式的误区：将复杂对象的构建进行封装，就是Builder模式了吗？](http://www.cnblogs.com/happyhippy/archive/2010/09/01/1814287.html)
