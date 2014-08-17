title: 'maven assembly plugin使用'
date: 2014-08-17 23:08:48
categories:
	- Maven
tags:
	- maven
	- java
---
####**使用场景**
在使用maven来管理项目时，项目除了web项目，还有可能为控制台程序，一般用于开发一些后台服务的程序。最近在工作中也遇到了这种场景，使用quartz开发一个任务调度程序。程序中依赖很多jar包，项目的启动时只需要初始化spring容器即可。<!--more-->
####**使用方法**
使用一个简单的基于spring框架的demo来做程序示例，来介绍maven assembly插件的使用方法。
项目中的代码目录如下：
![maven-assembly](http://yywang.qiniudn.com/maven-assembly-pre.png)
在以上代码目录中，assembly目录下为打包的描述文件，下面会详细介绍该文件，bin目录下为启动脚本以及readme等文件，main下为maven标准中建立的文件，java代码以及配置文件位于该目录下。
打包完成后压缩包目录如下：
![maven-assembly](http://yywang.qiniudn.com/maven-assembly-after.png)
打包完成后，我们可以看到bin目录来存放启动脚本等文件，config为配置文件，lib下为运行时依赖的jar包。
使用maven assembly插件需要在pom文件中配置，添加这个插件
```xml
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>2.4.1</version>
                <executions>
                    <execution>
                        <id>make-zip</id>
                        <!-- 绑定到package生命周期阶段上 -->
                        <phase>package</phase>
                        <goals>
                            <!-- 绑定到package生命周期阶段上 -->
                            <goal>single</goal>
                        </goals>
                        <configuration>
                            <descriptors> <!--描述文件路径-->
                                <descriptor>src/assembly/assembly.xml</descriptor>
                            </descriptors>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
```
其中execution节点，我们配置了执行maven assembly插件的一些配置，descriptor节点配置指向assembly.xml的路径。
在assembly.xml配置了，我们打包的目录以及相应的设置
```xml
<assembly xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2 http://maven.apache.org/xsd/assembly-1.1.2.xsd">
    <id>distribution</id>
    <formats>
        <format>zip</format>
    </formats>
    <fileSets>
        <fileSet>
            <directory>${project.basedir}\src\main\resources</directory>
            <outputDirectory>\</outputDirectory>
        </fileSet>
        <fileSet>
            <directory>${project.basedir}\src\bin</directory>
            <outputDirectory>\bin</outputDirectory>
        </fileSet>
    </fileSets>
    <dependencySets>
        <dependencySet>
            <useProjectArtifact>true</useProjectArtifact>
            <outputDirectory>lib</outputDirectory>
            <!-- 将scope为runtime的依赖包打包到lib目录下。 -->
            <scope>runtime</scope>
        </dependencySet>
    </dependencySets>
</assembly>
```
assembly.xml的配置项非常多，可以参考http://maven.apache.org/plugins/maven-assembly-plugin/assembly.html
以上只是用了很少的一部分。
format设置包输出的格式，以上格式设置的为zip格式，目前还支持zip，tar，tar.gz，tar.bz2，jar，dir，war格式
fileSet定义代码目录中与输出目录的映射，在该节点下还有 <includes/>，<excludes/>两个非常有用的节点。
比如：
```xml
        <fileSet>
            <directory>${project.basedir}\src\main\resources</directory>
            <outputDirectory>\</outputDirectory>
            <includes>
                <include>some/path</include>
            </includes>
            <excludes>
                <exclude>some/path1</exclude>
            </excludes>
        </fileSet>
```
以上代码表示归档时包括some/path，不包括some/path1
dependencySets节点下为依赖设置
在上述配置中，表示所有运行时依赖的jar包归档到lib目录下。在上述截图中lib目录下的文件就是所有依赖的jar包
![maven assembly](http://yywang.qiniudn.com/maven-assembly-lib.png)
更多节点的用法可以去官网查询
http://maven.apache.org/plugins/maven-assembly-plugin/assembly.html

下面的文章会介绍打包后的包如何做成windows服务，linux服务。
####**参考资料**
http://maven.apache.org/plugins/maven-assembly-plugin/