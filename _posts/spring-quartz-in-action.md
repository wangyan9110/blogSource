title: 'Quartz任务调度实践'
date: 2014-07-05 15:57:55
categories:
	- java
	- spring
tags:
	- quartz
	- spring
	- java
	- in action
---

最近在写一个任务调度程序，需要每隔几秒查询数据库，并取出数据做一些处理操作。使用到了Quartz任务调度框架。
####**基本概念
Quartz包含几个重要的对象，分别为任务（Job）,触发器（Trigger），调度器（Scheduler）
- Job:一个接口只有一个方法void execute(),我们需要执行的任务就需要实现这个接口，在execute中实现我们要做的事情。
- JobDetail:在Quartz执行每次执行Job时，都需要创建一个Job实例，所以它直接接受一个实现类以便运行时实例化，还需要一个描述信息，JobDetail就是做这个事情。创建JobDetail方法如下：<!--more-->
  JobBuilder builder=JobBuilder.newJob(SimpleJob.class);
  JobDetail jobDetail=builder.build();
  通过JobBuilder可以设置一些描述，比如builder.withIdentity(name,group)等等
- Trigger:是一个抽象类，描述触发执行的时间，它主要有SimpleTrigger和CronTrigger这两个子类，当仅需触发一次或者以固定时间间隔周期执行，SimpleTrigger是最适合的选择；而CronTrigger则可以通过Cron表达式定义出各种复杂时间规则的调度方案：如每早晨9:00执行，周一、周三、周五下午5:00执行等；
- Scheduler:可以认为是quartz的调度器，我们把JobDetail和Trigger注册到Scheduler，由它调度运行。

####**一个简单的例子**
直接使用quartz实现一个简单的例子。
1.创建一个SimpleJob类，实现Job接口
```java
public class SimpleJob implements Job{
    @Override
    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        System.out.println("执行了一个Job"+jobExecutionContext.getJobDetail().getDescription());
    }
}
```
2.使用SimpleJob创建一个JobDetail实例
```java
        //创建一个JobDetail实例
        JobBuilder builder=JobBuilder.newJob(SimpleJob.class);
        builder.withIdentity("test","test");
        builder.withDescription("my test");
        JobDetail jobDetail=builder.build();
```
3.创建一个Trigger，在这里使用SimpleTrigger创建，从现在开始每隔1s执行一次。
```java
        //创建一个调度规则，1s中运行一次，现在开始
        Trigger trigger = TriggerBuilder.newTrigger().withSchedule(
                SimpleScheduleBuilder.simpleSchedule()
                        .withIntervalInSeconds(1)
                        .repeatForever()).
                startNow().build();
```
4.创建一个Scheduler，并且把以上创建的Trigger和jobDetail注册进去，并且开始执行
```java
        try {
            //获取一个调度器
            SchedulerFactory schedulerFactory = new StdSchedulerFactory();
            Scheduler scheduler = schedulerFactory.getScheduler();
            //注册jobDetail，trigger到调度器
            scheduler.scheduleJob(jobDetail,trigger);
            //开始执行
            scheduler.start();
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
```

####**基于Spring的例子**
1.实现两个Job
```java
public class MyJob {

    public void process(){
        System.out.println("处理Job.."+Thread.currentThread().getId());
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void process1(){
        System.out.println("处理Job1.."+Thread.currentThread().getId());
    }
}
```
配置spring
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="myJob" class="org.yywang.spring.MyJob"></bean>

    <bean id="myJobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
        <property name="targetObject">
            <ref bean="myJob"></ref>
        </property>
        <property name="targetMethod">
            <value>process</value>
        </property>
        <!-- 是否允许任务并发执行。当值为false时，表示必须等到前一个线程处理完毕后才再启一个新的线程 -->
        <property name="concurrent">
            <value>true</value>
        </property>

    </bean>

    <bean id="myJobDetail1" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
        <property name="targetObject">
            <ref bean="myJob"></ref>
        </property>
        <property name="targetMethod">
            <value>process1</value>
        </property>
        <!-- 是否允许任务并发执行。当值为false时，表示必须等到前一个线程处理完毕后才再启一个新的线程 -->
        <property name="concurrent">
            <value>true</value>
        </property>

    </bean>

    <bean id="myTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
        <property name="jobDetail">
            <ref bean="myJobDetail"></ref>
        </property>
        <property name="cronExpression">
            <value>0/2 * * * * ?</value>
        </property>
    </bean>

    <bean id="myTrigger1" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
        <property name="jobDetail">
            <ref bean="myJobDetail1"></ref>
        </property>
        <property name="cronExpression">
            <value>0/5 * * * * ?</value>
        </property>
    </bean>

    <bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="triggers">
            <list>
               <ref bean="myTrigger"></ref>
                <ref bean="myTrigger1"></ref>
            </list>
        </property>
        <property name="autoStartup">
            <value>true</value>
        </property>
        <property name="configLocation" value="classpath:quartz.properties"/>
    </bean>
</beans>
```
quartz.properties
```java
#============================================================================
# 配置 Main Scheduler Properties
#============================================================================
org.quartz.scheduler.instanceName = DefaultQuartzScheduler
org.quartz.scheduler.rmi.export = false
org.quartz.scheduler.rmi.proxy = false
org.quartz.scheduler.wrapJobExecutionInUserTransaction = false

#============================================================================
# 配置执行线程池
#============================================================================
org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 5
org.quartz.threadPool.threadPriority = 5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread = true

#============================================================================
# 配置 JobStore
#============================================================================
org.quartz.jobStore.misfireThreshold = 60000

#内存中JobStore, 服务器重启时执行记录会丢失
org.quartz.jobStore.class = org.quartz.simpl.RAMJobStore

#数据库中JobStore
#org.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
#org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.MSSQLDelegate
#org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.oracle.OracleDelegate
```
客户端初始化spring即可
```java
 new ClassPathXmlApplicationContext("classpath:spring_*.xml");
```
####**Cron表达式**
参考如下：
http://liuzidong.iteye.com/blog/1119773
http://www.iteye.com/topic/582119
http://blog.itpub.net/183473/viewspace-434672

####**Other以上需要的Maven配置**
```xml
    <properties>
        <org.springframework.version>3.2.3.RELEASE</org.springframework.version>
    </properties>
    
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.1.7</version>
        </dependency>
                <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.quartz-scheduler</groupId>
            <artifactId>quartz</artifactId>
            <version>2.1.7</version>
        </dependency>
```
####**More**
http://www.ibm.com/developerworks/cn/java/j-quartz/index.html
http://www.blogjava.net/baoyaer/articles/155645.html
http://www.blogjava.net/supercrsky/articles/199443.html