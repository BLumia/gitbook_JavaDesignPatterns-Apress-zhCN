# 第2章 观察者模式
GoF给出的定义：定义了对象与对象之间"一个对多个"的依赖关系。这样，当某个对象的状态改变后，所有
依赖于这个对象的对象都将被通知并自动作出反应。  

## 概念

在这个设计模式中，有许多个观察者（对象）观察着某个特定的主题（对象）。那些观察者表现出对特定主题
感兴趣，并且想在主题内部发生变化时通知观察者，那么观察者只需把自己注册（Register）到这个主题上
。当观察者对这个主题失去兴趣后，它只需要取消注册即可。有时候这种关系模型也叫做"发行者-订阅者"
（Publisher-Subscriber）模型。

## 现实生活中的例子

比如有个名人（Celebrity）身边有很多粉丝（Fans），每个粉丝都想知道这个名人的最新动态，于是粉丝
只需要"关注"（Follow）这个名人即可。有一天某个粉丝对这个名人失去兴趣了，那这位粉丝只需要
"停止关注"（Un follow）这个名人即可。在这个例子里，粉丝就相当与观察者（Observer），而名人则是
被观察的那个主题（Subject）。

## 计算机世界的例子

在计算机科学的世界中，假设有个简单的基于用户界面（User Interface-Based）的例子，并且这个
用户界面连接到了某个数据库（或者业务逻辑）。某个用户通过这个用户界面请求查询某个数据后，查询结果
会显示在用户界面上。大多数情况下，我们一般把用户界面和数据库分开放着。当数据库内发生改变后，
用户界面就可以根据数据的变更改变所展示的信息。

## 图解

现在让我们来看一个简单的例子。在这里，我创建了一个观察者（当然，你也可以多建几个）和一个主题，
这个主题维护着一个列表，列表里存放了所有关注这个主题的观察者（不过我们简化成了只有一个观察者）。
这个观察者现在想在那个主题的标识值（Flag value）变更后被通知。从输出的结果中我们可以看到，当
标识值从5变成25后，观察者会收到相应的通知。不过，在标识值变成50的时候，观察者并没收到通知，原因
是观察者把它自己从主题里取消注册了。

## UML类设计图
![UML Class Diagram for Observer Patterns.](/images/chapter_02/1_ObserverPatterns_SimpleVersion_UMLClassDiagram.jpg)

## 包和类的结构图
![Package Explorer view for Observer Patterns.](/images/chapter_02/1_ObserverPatterns_SimpleVersion_PackageExplorerOverview.jpg)

## 实现代码
```java
package observer.pattern.demo;

import java.util.*;

class Observer {
    public void update() {
        System.out.println("flag value changed in Subject");
    }
}

interface ISubject {
    void register(Observer o);

    void unregister(Observer o);

    void notifyObservers();
}

class Subject implements ISubject {
    List<Observer> observerList = new ArrayList<Observer>();
    private int _flag;

    public int getFlag() {
        return _flag;
    }

    public void setFlag(int _flag) {
        this._flag = _flag;
        //flag value changed .So notify observer(s)
        notifyObservers();
    }

    @Override
    public void register(Observer o) {
        observerList.add(o);
    }

    @Override
    public void unregister(Observer o) {
        observerList.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (int i = 0; i < observerList.size(); i++) {
            observerList.get(i).update();
        }
    }
}

class ObserverPatternEx {
    public static void main(String[] args) {
        System.out.println("***Observer Pattern Demo***\n");
        Observer o1 = new Observer();
        Subject sub1 = new Subject();
        sub1.register(o1);
        System.out.println("Setting Flag = 5 ");
        sub1.setFlag(5);
        System.out.println("Setting Flag = 25 ");
        sub1.setFlag(25);
        sub1.unregister(o1);
        //No notification this time to o1 .Since it is unregistered.
        System.out.println("Setting Flag = 50 ");
        sub1.setFlag(50);
    }
}

```

## 输出结果

![Output for Observer Patterns.](/images/chapter_02/1_ObserverPatterns_SimpleVersion_Output.jpg)

## 笔记

上面的例子是观察者模式的一种很简单的实现，现在我们来探讨一个比上面更复杂的例子。假设：

1. 现在我们有多种不同的观察者类。
2. 并且，我们想知道主题里具体那些内容发生了变化。比如在上个例子中，虽然标识值在变更后观察者会
收到通知，但观察者并不知道变更后的标识值是多少。

显然，我们需要对上个例子里的代码做修改。注意，我们现在不能再使用一个明确的类当作观察者类：

```java
List<Observer> observerList = new ArrayList<Observer>();
```

而是改用接口：

```java
List<IObserver> observersList = new ArrayList<IObserver>();
```

代码里其他位置使用了 `Observer` 的也得改成 `IObserver`。并且 `IObserver.update()` 也得
改成 `IObserver.update(int)`，这样，观察者才能知道标识值变成了多少。

## UML类设计图

![UML Class Diagram in complex version of Observer Patterns.](/images/chapter_02/1_ObserverPatterns_ComplexVersion_UMLClassDiagram.jpg)

## 包和类的结构图

![Package Explorer view in complex version of Observer Patterns.](/images/chapter_02/1_ObserverPatterns_ComplexVersion_PackageExplorerView.jpg)

## 实现代码

```java
package observer.pattern.demo2;

import java.util.*;

interface IObserver {
    void update(int i);
}

class Observer1 implements IObserver {
    @Override
    public void update(int i) {
        System.out.println("Observer1: myValue in Subject is now: " + i);
    }
}

class Observer2 implements IObserver {
    @Override
    public void update(int i) {
        System.out.println("Observer2: observes ->myValue is changed in Subject to :" + i);
    }
}

interface ISubject {
    void register(IObserver o);

    void unregister(IObserver o);

    void notifyObservers(int i);
}

class Subject implements ISubject {
    private int myValue;

    public int getMyValue() {
        return myValue;
    }

    public void setMyValue(int myValue) {
        this.myValue = myValue;
        //Notify observers
        notifyObservers(myValue);
    }

    List<IObserver> observersList = new ArrayList<IObserver>();

    @Override
    public void register(IObserver o) {
        observersList.add(o);
    }

    @Override
    public void unregister(IObserver o) {
        observersList.remove(o);
    }

    @Override
    public void notifyObservers(int updatedValue) {
        for (int i = 0; i < observersList.size(); i++) {
            observersList.get(i).update(updatedValue);
        }
    }
}

class ObserverPatternModifiedEx {
    public static void main(String[] args) {
        System.out.println("*** Modified Observer Pattern Demo***\n");
        Subject sub = new Subject();
        Observer1 ob1 = new Observer1();
        Observer2 ob2 = new Observer2();

        sub.register(ob1);
        sub.register(ob2);

        sub.setMyValue(5);
        System.out.println();
        sub.setMyValue(25);
        System.out.println();

        //unregister ob1 only
        sub.unregister(ob1);
        //Now only ob2 will observe the change
        sub.setMyValue(100);
    }
}
```

## 输出结果

![Output in complex version of Observer Patterns.](/images/chapter_02/1_ObserverPatterns_ComplexVersion_Output.jpg)

## 小任务

_实现一个观察者模式，要求有多个不同的观察者和主题。_

> 注意：我们建议读者先自己试着做一下这个小任务，然后再来看下面的输出结果。

## UML类设计图

![UML Class Diagram of this Assignment.](/images/chapter_02/1_ObserverPatterns_Assignment_UMLClassDiagram.jpg)

## 代码实现

```java
package observer.pattern.demo3;

import java.util.*;

interface IObserver {
    void update(String s, int i);
}

class Observer1 implements IObserver {
    @Override
    public void update(String s, int i) {
        System.out.println("Observer1: myValue in " + s + " is now: " + i);
    }
}

class Observer2 implements IObserver {
    @Override
    public void update(String s, int i) {
        System.out.println("Observer2: observes ->myValue is changed in " + s + " to:" + i);
    }
}

class Observer3 implements IObserver {
    @Override
    public void update(String s, int i) {
        System.out.println("Observer3 is observing:myValue is changed in " + s + " to :" + i);
    }
}

interface ISubject {
    void register(IObserver o);

    void unregister(IObserver o);

    void notifyObservers(int i);
}

class Subject1 implements ISubject {
    private int myValue;

    public int getMyValue() {
        return myValue;
    }

    public void setMyValue(int myValue) {
        this.myValue = myValue;
        //Notify observers
        notifyObservers(myValue);
    }

    List<IObserver> observersList = new ArrayList<IObserver>();

    @Override
    public void register(IObserver o) {
        observersList.add(o);
    }

    @Override
    public void unregister(IObserver o) {
        observersList.remove(o);
    }

    @Override
    public void notifyObservers(int updatedValue) {
        for (int i = 0; i < observersList.size(); i++) {
            observersList.get(i).update(this.getClass().getSimpleName(), updatedValue);
        }
    }
}

class Subject2 implements ISubject {
    private int myValue;

    public int getMyValue() {
        return myValue;
    }

    public void setMyValue(int myValue) {
        this.myValue = myValue;
        //Notify observers
        notifyObservers(myValue);
    }

    List<IObserver> observersList = new ArrayList<IObserver>();

    @Override
    public void register(IObserver o) {
        observersList.add(o);
    }

    @Override
    public void unregister(IObserver o) {
        observersList.remove(o);
    }

    @Override
    public void notifyObservers(int updatedValue) {
        for (int i = 0; i < observersList.size(); i++) {
            observersList.get(i).update(this.getClass().getSimpleName(), updatedValue);
        }
    }
}

class ObserverPatternDemo3Ex {
    public static void main(String[] args) {
        System.out.println("*** Observer Pattern Demo3***\n");
        Subject1 sub1 = new Subject1();
        Subject2 sub2 = new Subject2();

        Observer1 ob1 = new Observer1();
        Observer2 ob2 = new Observer2();
        Observer3 ob3 = new Observer3();

        //Observer1 and Observer2 registers to //Subject 1
        sub1.register(ob1);
        sub1.register(ob2);
        //Observer2 and Observer3 registers to //Subject 2
        sub2.register(ob2);
        sub2.register(ob3);
        //Set new value to Subject 1
        //Observer1 and Observer2 get //notification
        sub1.setMyValue(50);
        System.out.println();
        //Set new value to Subject 2
        //Observer2 and Observer3 get //notification
        sub2.setMyValue(250);
        System.out.println();
        //unregister Observer2 from Subject 1
        sub1.unregister(ob2);
        //Set new value to Subject & only //Observer1 is notified
        sub1.setMyValue(550);
        System.out.println();
        //ob2 can still notice change in //Subject 2
        sub2.setMyValue(750);
    }
}
```

## 输出结果

![Output of this Assignment](/images/chapter_02/1_ObserverPatterns_Assignment_Output.jpg)
