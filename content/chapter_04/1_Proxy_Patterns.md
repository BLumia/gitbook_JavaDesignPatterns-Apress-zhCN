# 第4章 代理模式

GoF给出的定义：提供一个访问另一个对象的代理人或者占位符。

## 概念

我们想把一个类当作接口使用，并由它来操作实际的对象。

## 现实生活中的类子

在教室里，有个学生缺席了。老师点名的时候，这名学生的最好的朋友可能会尝试模仿他的声音，以防止老师
把他标记为缺席。

## 计算机世界的例子

比如有个银行的自动提款机，会有很多代理对象，实际银行信息是存储在远程服务器里的。我们必须知道，
在真正编程的时候，创建多个复杂对象（Complex object，也可以叫做 Heavy object）的实例是很
消耗资源的。这种情况下，我们可以创建多个代理对象（并让代理对象指向原有的对象）。这样，实际对象
的创建就可以按需进行，从而减少创建对象所消耗的时间和内存的开销。

## 图解
在下面的程序里，通过调用代理对象的 `doSomework()`，代理对象会反过来调用实际对象的
`doSomework()`。然后，我们就会从原有的类得到输出结果。

## UML类设计图

![UML Class Diagram for Proxy Patterns.](/images/chapter_04/1_ProxyPatterns_UMLClassDiagram.jpg)

## 包和类的结构图

![Package Explorer view for Proxy Patterns.](/images/chapter_04/1_ProxyPatterns_PackageExplorerView.jpg)

## 实现代码

```java
// Subject.java
package OriginalClasses;

public abstract class Subject {
    public abstract void doSomeWork();
}

// ConcreteSubject.java
package OriginalClasses;

import OriginalClasses.Subject;

public class ConcreteSubject extends Subject {

    @Override
    public void doSomeWork() {
        System.out.println(" I am from concrete subject");
    }
}

// Proxy.java
package ProxyClasses;

import OriginalClasses.*;

public class Proxy extends Subject {
    ConcreteSubject cs;

    @Override
    public void doSomeWork() {
        System.out.println("Proxy call happening now");
        //Lazy initialization
        if (cs == null) {
            cs = new ConcreteSubject();
        }
        cs.doSomeWork();
    }
}

// ProxyPatternEx.java
package proxy.pattern.demo;

import ProxyClasses.Proxy;

class ProxyPatternEx {
    public static void main(String[] args) {
        System.out.println("***Proxy Pattern Demo***\n");
        Proxy px = new Proxy();
        px.doSomeWork();
    }
}
```

## 输出结果

![Output for Proxy Patterns.](/images/chapter_04/1_ProxyPatterns_Output.jpg)

## 笔记

_有多少种不同的代理?_  
大体上有以下几种：
* 远程代理(Remote proxies)：把远程对象的具体地址隐藏起来。
* 虚拟代理(Virtual proxies)：用来提升程序性能，例如按需创建复杂对象。
* 保护性代理(Protection proxies)：一般用于控制访问权限。
* 智能引用(Smart reference)：在访问对象的时候做一些后勤工作，一个典型的例子就是
计算实际对象被引用了多少次。
