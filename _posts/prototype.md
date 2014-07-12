title: '设计模式学习--Prototype'
date: 2014-07-12 16:14:53
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---

###**What**

> Prototype：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。<!--more-->

###**Why**
Prototype适用于在一个类的实例有几种不同的状态组合的一种时，建立相应的数目的原型并克隆她们，要比每次使用合适的状态创建它们方便一些，或者为了避免创建一个与产品类层次平行的工厂类层次时，要实例化一的类在运行时动态指定时。
###**How**
假设如下场景：有一个复杂的报表，创建过程非常复杂，需要把报表发给两个领导，其中只有报表少了属性不同，其他属性相同。
首先讨论一下java中的基础知识
java的clone（）方法：
clone方法将对象复制一份并返回给调用者。一般满足如下条件：
1、对于任何对象a,都有a.clone()!=x，克隆对象和源对象是不同的对象
2、对于任何对象a,都有a.clone().getClass()==a.getClass(),克隆对象和源对象类型一样
3、如果对象a的equals()方法定义恰当，那么a.clone().equals(a)成立。
java中对象克隆的实现：
可以利用Object类的clone()方法
1、对象类实现Coneable接口
2、覆盖实现clone()方法
3、调用super.clone(),实现克隆。
以上场景实现代码如下：
```java
public class Product implements Cloneable{

    private String name;

    private int model;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getModel() {
        return model;
    }

    public void setModel(int model) {
        this.model = model;
    }

    @Override
    protected Object clone()  {
        try {
            return super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```
```java
public class Report implements Cloneable{

    private String name;

    private Product product;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Product getProduct() {
        return product;
    }

    public void setProduct(Product product) {
        this.product = product;
    }

    @Override
    protected Object clone()  {
        try {
            return super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```
客户端调用
```java
public class App {
    public static void main( String[] args ){
       Report report=new Report();
        Product product=new Product();
        product.setModel(1);
        product.setName("productName");
        report.setName("某某产品报表");
        report.setProduct(product);
        System.out.println(report.getName()+"|"+report.getProduct().getName());
        Report report1=(Report)report.clone();
        report1.setName("某某产品报表组1");
        report1.getProduct().setName("产品1");
        System.out.println(report1.getName()+"|"+report1.getProduct().getName());
    }
}
```
以上代码类图如下：
![Prototype](http://yywang.qiniudn.com/prototype.png)
###**Discuss**
深拷贝VS浅拷贝
以上的例子我们的运行结果如下：
```xml
某某产品报表|productName
某某产品报表组1|产品1
```
我们在加一行代码，在最后一行打印report的信息
```java
System.out.println(report.getName()+"|"+report.getProduct().getName());
```
我们的运行结果如下：
```xml
某某产品报表|productName
某某产品报表组1|产品1
某某产品报表|产品1
```
我们可以看出，第二次打印的结果出了问题，report中的产品名字已经被改变了。
在Report中，其中product属性，我们引用其他对象，在clone方法中，我们的实现只复制了它的引用，只负责了它的指针，没有复制它所指向的堆空间里的属性，所以在这行代码report1.getProduct().setName("产品1");也改变了report中product属性中的name属性的值，这种拷贝是浅拷贝。
我们修改下report的clone()方法，改为如下：
```java
    @Override
    protected Object clone()  {
        Report report;
        try {
            report=(Report) super.clone();
            report.setProduct((Product)product.clone());
            return report;
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return null;
    }
```
我们再看下运行结果
```xml
某某产品报表|productName
某某产品报表组1|产品1
某某产品报表|productName
```
这次结果正确了，在以上代码中，我们对product属性，也进行了一次clone，所以report1和report的product属性指向了不同的堆空间，所以我们改变product中的属性值，不会相互影响，这个拷贝就是深拷贝。