title: '设计模式学习--Facade'
date: 2014-10-19 20:38:17
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---
####What
> Facade：为子系统中的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

####Why
其实Facade模式，我们几乎每天都在使用，比如在web开发中，我们经常使用三层架构，每一层通过接口隔离，然后是接口的实现，其实这就是一种简单的Facade模式，在一下情况使用Facade：
1. 当你要为一个复杂子系统提供一个简单接口时。子系统往往因为不断演变变得原来越复杂。大部分模式会产生很多更小的类。这使得子系统更具有重用性，也容易对子系统控制，但是对系统用户调用带来的困难，Facade可以提供一个简单的缺省视图，这个视图对于大部分用户来说已经足够了。
2. 客户程序与抽象类的实现部分之间，存在着很大的依赖性。
3. 当你需要构建一个层次结构的子系统时，使用Facade模式定义子系统中每层的入口点。（三层架构）<!--more-->

####How
假设如下场景：一个发短信的需求，要求客户端填写发送内容以及发送号码，可以支持移动、电信、联通三大运营商的信息。
分析：一般发送短信的功能需要如下步骤，根据短信模板，生成短信全部内容，根据运营商或者开放平台提供的短信接口，构建需要的数据格式，通过socket或者http的方式把数据传过去。很显然用户不需要了解如此复杂的发送程序，它只需要传入号码以及内容就就应该可以发送短信了。我们就可提供一个门面让用户可以方便的调用。
发短信子系统接口
```java
public interface SendMessage {

    /**
     * 获取添加模板之后的内容
     * @param message 信息
     * @return 添加模板之后的内容
     */
    String getMesageWithTempl(String message);

    /**
     * 获取根据数据格式后的内容
     * @return 内容
     */
    String getFormatMessage(String phone,String message);

    /**
     * 发送短信
     * @param message 消息
     */
    void sendMessage(String message);
}
```
发短信子系统实现
```java
public class SendMessageImpl implements SendMessage {

    @Override
    public String getMesageWithTempl(String message) {
        return message+"[来自：yywang.info]";
    }

    @Override
    public String getFormatMessage(String phone, String message) {
        return phone+":"+message;
    }

    @Override
    public void sendMessage(String message) {
        System.out.println(String.format("发送：%s", message));
    }
}
```
发短信门面
```java
public interface SendMessageFacade {

    /**
     * 发送消息
     * @param phone 手机号码
     * @param message 消息
     */
    void sendMessage(String phone,String message);
}
```
发短信门面实现
```java
public class SendMessageFacadeImpl implements SendMessageFacade{

    private SendMessage sendMessage=new SendMessageImpl();

    @Override
    public void sendMessage(String phone, String message) {
        String telMsg=sendMessage.getMesageWithTempl(message);
        String formatMsg=sendMessage.getFormatMessage(phone,telMsg);
        sendMessage.sendMessage(formatMsg);
    }
}
```
客户端调用
```java
public class App {

    public static void main(String[] args){
        SendMessageFacade sendMessageFacade=new SendMessageFacadeImpl();
        sendMessageFacade.sendMessage("123445566","Hello");
    }
}
```
![facade](http://yywang.qiniudn.com/facade.png)
####Discuss
上面我们说过Facade在编程过程中经常用到，在web应用程序开发中，有比较经典的三层架构，软件的分层思想也是Facade模式的一种，但是这种分层思想在实际的应用中又是怎样呢？
在web应用程序开发中，我们一般分为三层：展现层、业务层、数据层;各层各司其职，展现层，一般用于提供用户访问界面，业务层，主要实现业务接口，其实业务接口所做的事情就是把提供业务接口门面，让用户可以方便的调用我们的业务接口，复杂的业务逻辑在业务层实现。数据层，没有业务逻辑，一般用于数据库访问。
这样的三层架构让我们的代码大体职责更加清晰，从以上的介绍中，也可以看出来，其中业务层最难让人理解，其实也是最复杂的一部分，目前做的一个系统，我认为是我没做好的一部分，在设计的过程中，一般重点考虑domain设计，以及service接口设计（其实就是门面接口设计），可以说这些不是真正的设计，应该更加注重业务实现设计，最简单的如何重用利用代码，如何应对业务变化等方面考虑问题。其实业务实现设计，在一些简单的模块完全没必要做，但是在我负责的模块，我越来越感觉业务实现的设计重要性。其中有部分业务接口实现的代码已经超过了2000行，维护起来越来越麻烦。所以在实际的应用中，不一定就是一个接口然后对应一个接口实现类的，就是分层架构，分层的目的一是为了职责更加清新，二是隔离具体实现，让客户端方便的调用，把分层接口看做是一个门面。定义门面接口只是设计的一小部分。