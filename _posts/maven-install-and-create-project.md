title: maven安装与创建多模块项目
date: 2014-05-31 17:06:40
categories: 
	- Maven
tags: 
	- maven
	- java
---
maven是一个比较流行的项目管理工具，在最近参与的项目中，也使用了maven，本文主要对在项目中的使用做一个总结，主要涉及maven的安装于配置、maven创建多模块项目。

1、maven安装与配置
maven的安装与配置非常简单，具体步骤如下：
　1、首先到[下载maven](http://maven.apache.org/download.cgi)的包，可以选择下载：apache-maven-3.2.1-bin.zip。
　2、解压下载的文件到电脑硬盘的某个目录，比如D:\Program Files\
　3、然后在环境变量中配置MAVEN_HOME=D:\Program Files\apache-maven-3.2.1-bin
　4、在path中添加%MAVEN_HOME%\bin;
<!--more-->
完成以上步骤，在运行 cmd 中 输入 mvn -version出现如下：
![图片](http://yywang.qiniudn.com/011841037813000.png)
安装成功。
如果需要指定本地仓库的位置进入安装目录，config打开setting.xml设置
```xml
<localRepository>D:\Repositories\Maven</localRepository>
```
注意：在使用ecplise时注意配置Windows->Perferences->User Settings 中User Settings选择以上的setting.xml

maven的安装于配置已经完成了，需要了解更多的maven基础资料可以访问如下：
http://maven.apache.org/guides/index.html
http://maven.apache.org/pom.html
2、maven创建多模块项目
在开发web项目时，一般分为domain（领域对象）、persist（持久化）、service（业务）、web（web交互）四个模块。在有的项目中还按照不同的业务进行划分几个模块，每个模块包含这几个模块。
使用maven创建多模块项目步骤如下（以一个blogs为名字的项目为例）：
1、创建blogs-root，用于各个模块集成。
(1)、进入命令行，输入如下：
```xml
mvn archetype:create -DgroupId=org.yywang -DartifactId=blogs-root -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveModel=false
```
在当前目录下，创建了一个blogs-root的目录，进入blogs-root有两个文件，src、pom.xml文件。
删除src文件。
(2)、修改pom文件
修改pom文件中的 <packaging>jar</packaging>为 <packaging>pom</packaging>
修改后的pom文件内容如下：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.yywang</groupId>
  <artifactId>blogs-root</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>

  <name>blogs-root</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
2、创建blogs-domain，领域模型模块
(1)、进入blogs-root，进入命令行，输入
```xml
mvn archetype:create -DgroupId=org.yywang -DartifactId=blogs-domain -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveModel=false
```
 在blogs-root目录下创建了blogs-domain目录。
在blogs-root目录下pom文件中增加了一个模块
```xml
  <modules>
    <module>blogs-domain</module>
  </modules>
 ```
 (2)、修改blogs-domain下的pom文件

删除 <groupId>org.yywang</groupId>和<version>1.0-SNAPSHOT</version>，添加<packaging>jar</packaging>。

修改后的pom文件如下：
```xml
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.yywang</groupId>
    <artifactId>blogs-root</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <artifactId>blogs-domain</artifactId>
  <packaging>jar</packaging>
  <name>blogs-domain</name>
  <url>http://maven.apache.org</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
 3、创建blogs-persist，持久化模块
(1)、在blogs-root目录，进入命令行，输入
```xml
mvn archetype:create -DgroupId=org.yywang -DartifactId=blogs-persist -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveModel=false
```
  在blogs-root目录下，增加了blogs-persist目录。

  在blogs-root目录下的pom文件，modules节点下多了<module>blogs-persist</module>

(2)、修改blogs-persist目录下的pom文件增加blogs-domain的依赖，修改为如下：
```xml
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.yywang</groupId>
    <artifactId>blogs-root</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <artifactId>blogs-persist</artifactId>
  <packaging>jar</packaging>
  <name>blogs-persist</name>
  <url>http://maven.apache.org</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.yywang</groupId>
      <artifactId>blogs-domain</artifactId>
      <version>${project.version}</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
 3、创建blogs-service,业务模块

(1)、在blogs-root目录，进入命令行，输入
```xml
mvn archetype:create -DgroupId=org.yywang -DartifactId=blogs-service -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveModel=false
```
  在blogs-root目录下，增加了blogs-service目录。
  在blogs-root目录下的pom文件，modules节点下多了<module>blogs-service</module>
(2)、修改blogs-service目录下的pom文件增加blogs-persist的依赖，修改为如下：
```xml
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.yywang</groupId>
    <artifactId>blogs-root</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <artifactId>blogs-service</artifactId>
  <packaging>jar</packaging>
  <name>blogs-service</name>
  <url>http://maven.apache.org</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.yywang</groupId>
      <artifactId>blogs-persist</artifactId>
      <version>${project.version}</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
  4、创建blogs-web,web交互模块
  (1)、在blogs-root目录，进入命令行，输入
```xml
mvn archetype:create -DgroupId=org.yywang -DartifactId=blogs-web -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveModel=false
```
  在blogs-root目录下，增加了blogs-web目录。

  在blogs-root目录下的pom文件，modules节点下多了<module>blogs-web</module>

 (2)、修改blogs-web目录下的pom文件增加blogs-service的依赖，修改为如下：
```xml
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.yywang</groupId>
    <artifactId>blogs-root</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <artifactId>blogs-web</artifactId>
  <packaging>war</packaging>
  <name>blogs-web Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>org.yywang</groupId>
      <artifactId>blogs-service</artifactId>
      <version>${project.version}</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>blogs-web</finalName>
  </build>
</project>
```
完成所有模块的创建后，blogs-root目录下的pom文件如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.yywang</groupId>
  <artifactId>blogs-root</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
  <name>blogs-root</name>
  <url>http://maven.apache.org</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <modules>
    <module>blogs-domain</module>
    <module>blogs-persist</module>
    <module>blogs-service</module>
    <module>blogs-web</module>
  </modules>
</project>
```
这样就完成了maven多模块项目的创建。