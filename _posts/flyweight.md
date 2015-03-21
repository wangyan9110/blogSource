title: '设计模式学习--Flyweight'
date: 2014-12-19 23:09:54
categories:
	- Design pattern
	- java
tags:
	- Design pattern
	- java
---
####What
> 运用共享技术有效地支持大量细粒度的对象。

####Why
在创建大量对象时，如果每个小对象都创建一个对象都是非常消耗内存的。在古代我们的先人已经发现了，重复工作带来的成本消耗，我国古代四大发明之一活字印刷术，就是解决的这类问题。我们把每个字看做一个细粒度的对象的话，印刷一本书，就会有成千上万个小对象存在，这成千上万个字除了位置不同之外，没有其他区别。如果采用活字印刷术，就可以方便字的重用，节省大量的工作量。同样的，如果对象可以在计算机中共享，相同的对象只创建一个对象，也可以节省大量的内存空间和创建对象消耗的时间。这就是flyweight的好处。如下场景适合用flyweight模式：
1. 一个应用程序使用了大量的对象。
2. 完全由于使用大量的对象，造成很大的存储开销。
3. 对象的大多数状态都可变为外部状态。以上活字印刷的例子，字的位置就是外部状态。
4. 如果删除的外部状态，那么可以用相对较少的共享对象取代很多组对象。
5. 应用程序不依赖与对象标识。<!--more-->

####How
假设如下场景：设计一个艺术字生成器。
分析：这个例子就可以类似我们祖先发明的活字印刷术的思想来做，先检查池里是否有这个字，如果没有雕刻这个字，并存储到“池”中，如果存在从“池”中拿出这个字，放到相应的位置就可以了。

艺术字工厂类，创建字以及已创建字的缓存
```java
public class ArtWordFactory {

    private static Map<String,ArtWord> artWordMap=new HashMap<String, ArtWord>();

    public static ArtWord getArtWord(String word){
        ArtWord artWord=artWordMap.get(word);
        if(artWord==null){
            if(word.equals("zhong")){
                artWord=new ZhongArtWord();
                artWordMap.put("zhong",artWord);
            }else if(word.equals("guo")){
                artWord=new GuoArtWord();
                artWordMap.put("guo",artWord);
            }
        }
        return artWord;
    }
}
```
艺术字的共享接口，在艺术字中艺术字的位置是不共享的
```java
public interface ArtWord {

    /**
     * 不同艺术字布局不共享
     * @param position 位置
     */
    void layout(Position position);
}
```
中字实现类
```java
public class ZhongArtWord implements ArtWord {

    @Override
    public void layout(Position position) {
        System.out.println("中的位置："+position.getX()+" "+position.getY());
    }
}

```
国字实现类
```java
public class GuoArtWord implements ArtWord {


    @Override
    public void layout(Position position) {
        System.out.println("国的位置："+position.getX()+" "+position.getY());
    }
}
```
客户端调用
```java
public class App {

    public static void main(String[] args){

        ArtWord zhongArtWord=ArtWordFactory.getArtWord("zhong");
        zhongArtWord.layout(new Position(1,2));

        ArtWord guoArtWord=ArtWordFactory.getArtWord("guo");
        guoArtWord.layout(new Position(3,4));

    }
}
```
以上代码类图

![flyweight](http://7i7jaz.com1.z0.glb.clouddn.com/flyweight.png)

####Discuss
flyweight在系统需要创建大量对象的情况下，非常有用，在详细设计时，可以抽离对象的共性共享，不同变成外部状态，比如以上的例子，每个艺术字的对象是共享的，由于需要排版，所以字的位置不能固定。从此可以看出，我们的祖先是多么的聪明，在北宋时期，就使用了flyweight模式，发明了活字印刷术。这要比四人帮提出这个模式要早不知多少年。^_^
