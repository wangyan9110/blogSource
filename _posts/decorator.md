title: '设计模式学习--Decorator'
date: 2014-10-17 23:50:21
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---

####What
> Decorator：动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator模式相比生成子类更加灵活。
####Why
Decorator模式适用于可以动态的给对象增删职责，比如qq秀我们可以选择自己形象，并动态的添加衣服以及装饰，让自己的形象感觉高大上起来。Decorator适用于如下情况：
1.在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
2.处理那些可以撤销职责的场景。
3.当不能采用子类的方法进行扩充时。<!--more-->
####How
假设如下场景，最近有个app比较火，FaceQ可以用来创建自己的卡通形象，如果实现这样的功能就可以使用Decorator模式。
基础的装扮类
```java
public class Face {

    private String name;

    public Face(){

    }

    public Face(String name){
        this.name=name;
    }

    public void show(){
        System.out.println("开始装扮Face:" + name);
    }

}
```
脸型装扮类
```java
public class FaceFeature extends Face{

    private Face face;

    public FaceFeature(){

    }

    public void decorator(Face face){
        this.face=face;
    }

    @Override
    public void show() {
        face.show();
    }
 }
```
圆型脸
```java
public class RoundFaceFeature extends FaceFeature{

    @Override
    public void show() {
        System.out.println("添加圆脸");
        super.show();
    }
}
```
方型脸
```java
public class SquareFaceFeature extends FaceFeature {
    @Override
    public void show() {
        System.out.println("添加方脸");
        super.show();
    }
 }

```
嘴型装扮类
```java
public class Mouth extends Face {

    private Face face;

    public Mouth(){

    }

    public void decorator(Face face){
        this.face=face;
    }

    @Override
    public void show() {
        face.show();
    }
 }
```
微笑嘴型
```java
public class SmileMouth extends Mouth{

    @Override
    public void show() {
        System.out.println("添加微笑");
        super.show();
    }
}
```
闭嘴嘴型
```java
public class ClosedMouth extends Mouth {

    @Override
    public void show() {
        System.out.println("添加闭嘴。");
        super.show();
    }
 }

```
调用以上装扮类
```java
    public static void main(String[] args){
        Face face=new Face("test");
        RoundFaceFeature roundFaceFeature=new RoundFaceFeature();
        SmileMouth smileMouth=new SmileMouth();
        roundFaceFeature.decorator(face);
        smileMouth.decorator(roundFaceFeature);
        smileMouth.show();
    }
```
以上代码的UML图如下：
![Decorator](http://yywang.qiniudn.com/decorator.png)
####Discuss
在以上示例中Face类既充当了职责接口也是具体的装扮对象，FaceFeature以及Mouth为两个装扮类。在java的jdk中也有一个比较典型的Decorator模式使用的地方，在java的io类中，在OutputStream，InputStream，Reader，Writer等都用到了Decorator模式，有时间可以仔细分析一下。
