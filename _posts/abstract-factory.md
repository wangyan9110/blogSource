title: '设计模式学习--Abstarct Factory'
date: 2014-06-23 17:18:08
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---

###**What**

> Abstarct Factory：提供一个创建一系列相关或相互依赖的接口，而无需指定他们具体类。<!--more-->

###**Why**
Abstarct Factory是创建型设计模式的一种，主要在创建对象时解耦，避免对象的直接依赖，方便替换与定制。常见的比如：一个功能有两种不同的风格，需要根据配置来切换不同的风格时，或者在一个需要适用于多个数据库切换的程序中，都会使用Abstact Factory。
适用于Abstarct Factory的场景：
1、系统的展现或者功能需要可配置时。
2、系统模块的创建，需要独立于系统模块时。
3、系统需要动态定制时。
###**How**
假设如下场景，在编写数据库访问层时，需要支持两种数据库的切换，比如可以支持sqlserver和mysql的切换。以这个简单的例子，说明Abstarct Factory的实现
首先我们定义我们的dao接口：
```java
public interface UserDao {

    void insert(User user);

    void delete(String id);

    User find(String id);
}
```
这个接口需要三个简单的方法，插入，删除，查询
定义用于创建Dao实例的工厂接口
```java
public interface DaoFactory {

    UserDao create();
    
}
```
用于访问sqlserver的UserDao的实现
```java
public class SqlServerUserDaoImpl implements UserDao {

    @Override
    public void insert(User user) {
        System.out.println("sqlserver insert user");
    }

    @Override
    public void delete(String id) {
        System.out.println("sqlserver delete user");
    }

    @Override
    public User find(String id) {
        System.out.println("sqlserver find user");
        return null;
    }
}
```
用于访问mysql的UserDao的实现
```java
public class MysqlUserDaoImpl implements UserDao {

    @Override
    public void insert(User user) {
        System.out.println("mysql insert user");
    }

    @Override
    public void delete(String id) {
        System.out.println("mysql delete user");
    }

    @Override
    public User find(String id) {
        System.out.println("mysql find user");
        return null;
    }
}
```
用于创建sqlserver userDao的工厂
```java
public class SqlserverDaoFactoryImpl implements DaoFactory {

    @Override
    public UserDao create() {
        return new SqlServerUserDaoImpl();
    }
}
```
用于创建mysql userDao的工厂
```java
public class MysqlDaoFactoryImpl implements DaoFactory {

    @Override
    public UserDao create() {
        return new MysqlUserDaoImpl();
    }
}
```
客户端调用方法
```java
public class App {

    public static void main( String[] args ){

        DaoFactory daoFactory=new MysqlDaoFactoryImpl();
        UserDao userDao=daoFactory.create();
        userDao.insert(null);

        DaoFactory daoFactory1=new SqlserverDaoFactoryImpl();
        UserDao userDao1=daoFactory1.create();
        userDao1.delete("");
    }
}
```
以上实例的类图如下：
![abstarct factory](http://yywang.qiniudn.com/abstract-factory.png)
###**Discuss**
在以上的例子中，还可以延伸到把数据库的选择写在配置文件中，然后在系统启动时根据配置通过反射加载不同的程序，这个在以前使用c#做一个系统时用到过，在java的web开发中，一般使用spring框架，它提供了IOC技术，通过配置bean来做数据源的初始化。
在spring的源代码中，也有Abstract Factory的使用，比如BeanFactory就是一个例子，当然它的设计要比本文中的例子，复杂的多。
###**ChangeLog**
在学习时发现本节示例不够完善，所以进行了完善，增加在ChangeLog中，两者的区别和联系在[设计模式学习--Factory Method](http://yywang.info/2014/06/28/factory-method/)的Discuss章节中。
示例代码基于以上示例修改，如果理解了示例中的代码，修改为如下结构的代码比较简单就不在帖出，也可以到去[我的GitHub](https://github.com/wangyan9110/Designpatterns)下载。
![Abstarct Factory](http://yywang.qiniudn.com/abstarct-factory-new.png)