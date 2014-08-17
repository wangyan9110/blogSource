title: '设计模式学习--Composite'
date: 2014-08-14 23:25:32
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---
###**What**

> Composite：将对象组合成树形结构以表示“部分-整体”的层次结构。Composite使得用户对单个对象和组合对象的使用具有一致性。<!--more-->

###**Why**
Composite模式是一个比较常见的模式，在面向对象设计中，我们经常使用对象的组合来完成大对象的创建或者表示一种层次关系，比如在设计一个公司的组织结构的子系统，每个部门可以抽象为一个业务单元，公司对象就是由很多业务单元组成，再比如我们界面上的编程也无时不刻在使用着组合的设计思想，界面上的每个小区域组成我们整个看起来美观大方的界面。
适用于如下情况：
你想表示对象的部分-整体的层次结构
你希望用户忽略组合对象和单个对象的不同，用户将统一地使用组合结构中的所有对象。
###**How**
假设如下场景，相信我们上学的时候都考过试，都使用过答题卡，怎么使用程序生成答题卡？
我们可以使用绘图的api画，也可以使用html生成，也我们可以使用任何的图形api来绘制出来。在生成答题卡的关键在于建模，建模完成后，我们可以根据我们需求和使用的语言来选择我们使用哪种方式呈现出来。一般一张答题卡由准考证号，选择题，问答题等区域组成。我们可以把每个区域抽象为一个Element，一张答题卡就是由很多个Element组成.
基础的element
```java
public abstract class Element {

    protected int top;

    protected int left;

    protected int width;

    protected int height;

    protected List<Element> children = new ArrayList<Element>();

    protected Element(int top, int left, int width, int height) {
        this.top = top;
        this.left = left;
        this.width = width;
        this.height = height;
    }

    protected abstract void display();

    protected abstract void add(Element element);

    public void show() {
        show(this);
    }

    private void show(Element element) {
        for (Element element1 : element.children) {
            element1.display();
            if (!element1.children.isEmpty()) {
                show(element1);
            }
        }
    }
}
```
学生准考号区域
```java
public class UserCodeElement extends Element {

    public UserCodeElement(int top, int left, int width, int height) {
        super(top, left, width, height);
    }

    @Override
    protected void display() {
            System.out.print("学号区域：left:" + this.left + " top:" + this.top
                    + " width:" + this.width + " height:" + this.height);
    }

    @Override
    protected void add(Element element) {
        children.add(element);
    }
}
```
选择题单个框区域
```java
public class ChooseItemElement extends Element{

    public ChooseItemElement(int top, int left, int width, int height){
        super(top, left, width, height);
    }

    @Override
    protected void display() {
            System.out.print("选择题单项：left:" + this.left + " top:" + this.top
                    + " width:" + this.width + " height:" + this.height);
    }

    @Override
    protected void add(Element element) {

    }
}
```
选择题区域
```java
public class ChooseElement extends Element {

    public ChooseElement(int top, int left, int width, int height){
        super(top, left, width, height);
    }

    @Override
    protected void display() {
            System.out.print("选择题：left:" + this.left + " top:" + this.top
                    + " width:" + this.width + " height:" + this.height);
    }

    @Override
    protected void add(Element element) {
        children.add(element);
    }
}
```
答题卡区域
```java
public class AnswerCardElement extends Element{

    public AnswerCardElement(int top, int left, int width, int height){
        super(top, left, width, height);
    }

    @Override
    protected void display() {

    }

    @Override
    protected void add(Element element) {
        children.add(element);
    }
}
```
客户端调用
```java
public class App {

    public static void main(String[] args) {
        UserCodeElement userCodeElement = new UserCodeElement(10, 10, 20, 20);
        ChooseItemElement chooseItemElement = new ChooseItemElement(100, 100, 20, 20);
        ChooseElement chooseElement = new ChooseElement(100, 100, 100, 100);
        chooseElement.add(chooseItemElement);
        AnswerCardElement answerCardElement = new AnswerCardElement(0, 0, 1000, 1000);
        answerCardElement.add(userCodeElement);
        answerCardElement.add(chooseElement);
        answerCardElement.show();
    }

}
```
以上实现的UML如下：
![composite](http://yywang.qiniudn.com/composite.png)
###**Discuss**
以上示例只是简单的实现了对象的关系，没有考虑更加复杂的需求，如果真正的完成生成答题卡生成的程序还是比较麻烦的，还需要考虑布局等。Composite模式在处理部分与整体的关系的需求中，是一个比较有用的设计模式，最近看到学习设计模式几个境界，第一个阶段就是知道设计模式的一些概念，但是实现起来可能有些困难，第二个阶段就是在有需求时，套用设计模式，第三个阶段，就是深入各种设计模式的思想，能够灵活的运用于程序设计中。目前虽然看过设计模式，还是不能深入的理解其思想，在平常的设计过程中，也常会套用设计模式的误区，学习设计模式还是需要深入理解其思想，多思考，悟出其中的道理。