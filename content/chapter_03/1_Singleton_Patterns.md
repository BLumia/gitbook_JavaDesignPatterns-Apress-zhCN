# 第3章 单例模式

GoF给出的定义：确保某个类的实例只有一个，所有代码都只能访问这一个实例。

## 概念

某个特定的类的实例应该只有一个，无论如何我们只能使用那一个实例。

## 现实生活中的例子

假设你是一名板球运动的队伍的一名成员，在一场锦标赛里你的队伍要和另一只队伍进行对决。每个队需要
有个队长来投硬币决定哪个队先发球。如果你的队伍里没有队长的话，那就得先选举一个人当队长。
选出队长后，你的队伍就不能选出个人当队长了。

## 计算机世界的例子

在软件系统中，我们有时候得决定只使用一种文件系统，这种情况一般是为了资源的中心化管理。

## 图解

在这个例子中，我们先让类的构造函数变为私有（Private）的，这样就没法像通常那样实例化它了。当我们
需要这个类的实例时候，我们就做个判断。如果我们没有这个类的实例的拷贝，那就创建一个；反之，
直接返回现有的实例。

## UML类设计图

![UML Class Diagram for Singleton Patterns.](/images/chapter_03/1_SingletonPatterns_UMLClassDiagram.jpg)

## 包和类的结构图

![Package Explorer view for Singleton Patterns.](/images/chapter_03/1_SingletonPatterns_PackageExplorerView.jpg)

## 实现代码

```java
package singleton.pattern.demo;

class MakeACaptain {
    private static MakeACaptain _captain;

    //We make the constructor private to prevent the use of "new"
    private MakeACaptain() {
    }

    public static MakeACaptain getCaptain() {

        // Lazy initialization
        if (_captain == null) {
            _captain = new MakeACaptain();
            System.out.println("New Captain selected for our team");
        } else {
            System.out.print("You already have a Captain for your team.");
            System.out.println("Send him for the toss.");
        }
        return _captain;

    }
}

class SingletonPatternEx {
    public static void main(String[] args) {
        System.out.println("***Singleton Pattern Demo***\n");
        System.out.println("Trying to make a captain for our team");
        MakeACaptain c1 = MakeACaptain.getCaptain();
        System.out.println("Trying to make another captain for our team");
        MakeACaptain c2 = MakeACaptain.getCaptain();
        if (c1 == c2) {
            System.out.println("c1 and c2 are same instance");
        }
    }
}
```

## 输出结果

![Output for Singleton Patterns.](/images/chapter_03/1_SingletonPatterns_Output.jpg)

## 笔记

_为什么我们在代码中使用一个术语叫做"懒初始化（Lazy initialization）"?_  
使用了单例模式后，只有在你调用 `getCaptain()` 后才会创建这个类的实例。

_请指出一个上面代码中可以改进的地方?_  
上面的代码不是线程安全的。也就意味着，当多个线程同时调用 `getCaptain()`的时候，就有可能创建出
多个实例。

_那么，我们该怎么修改代码才能让它线程安全呢?_  
这个问题已经被讨论过很多次了，每个人都有各自的想法和方法，每种方法自身都有优点和缺点。在这里，
我们只着重说明以下几种方法：

__第1种：__ 使用 `synchronized` 关键字
```java
public static synchronized MakeACaptain getCaptain()
{
    //our code
}
```
使用上面的方法会导致一点儿额外的性能开销。

__第2种：__ "早初始化（Eager initialization）"（与 懒初始化 相对）
```java
class MakeACaptain {
    //Early initialization
    private static MakeACaptain _captain = new MakeACaptain();

    //We make the constructor private to prevent the use of "new"
    private MakeACaptain() {
    }

    // Global point of access MakeACaptain.getCaptain() is a public static method
    public static MakeACaptain getCaptain() {
        return _captain;
    }
}
```
在上面这种情况下，类总是并且只会初始化一次。

__第3种：__ 由 Bill Pugh 想出的一种办法
```java
class MakeACaptain {
    private MakeACaptain() {
    }

    //Bill Pugh solution
    private static class SingletonHelper {
        //Nested class is referenced after getCaptain() is called
        private static final MakeACaptain _captain = new MakeACaptain();
    }

    public static MakeACaptain getCaptain() {
        return SingletonHelper._captain;
    }
}
```
这种方法既不用"同步（Synchronization）"也不用"早初始化"，并且在Java里已经作为标准的
单例模式的实现方式。
