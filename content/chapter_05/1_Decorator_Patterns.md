# 第5章 装饰者模式

GoF给出的定义：动态的为对象附加额外的职责。装饰者模式提供了一种灵活的、可替代的子类化方式，用于
扩展已有的功能。

## 概念
这个设计模式的主要原理就是：我们不能修改已有的功能，但我们可以扩展它们。换句话说，这个设计模式
对扩展开放但对修改关闭。再细一点说，我们可以为某个特定对象添加某个特定功能，而无需修改那个对象
的类。

> 译注：  
> Open for extension but closed for modification.  
> 对扩展开放，对修改关闭。  
> 意思就是：你对现有的类的功能不满意，尽量不要去修改这个类，而是写个新的类
（新的类可以继承这个类或者它的父类）作为装饰者来"装饰"（更形象一点也叫做 包裹）现有的类。
此概念为面向对象程序设计原则之一。  

## 现实世界中的例子

比如你已经拥有了一套房子，现在你想给这个房子再加一层地板。很显然，你肯定不想改变原来的地板的结构
。你可能既想为新的地板设计结构，又不想让新的结构影响到原来的地板的设计结构。

## 计算机世界的例子

假设，在一个基于图形界面的工具箱里，我们想为组件添加一些边框属性。虽然我们可以用继承来实现它，
但这并不是最好的解决方法，因为我们的用户（或者说 客户）会因此对它失去的完整控制权。  

装饰者模式可以让我们更灵活的达到目的。比如，我们可能用另一个对象包裹这个组件。包裹这个组件的对象
就叫做"装饰者"，并且与被包裹的组件的接口保持一致。这个装饰者会把所有请求转发到那个组件，当然也能
在转发之前或者之后做些附加工作。使用这种概念，我们可以为被装饰的类添加无限量的职责。

## 图解

请仔细观察下面的例子。我们并没有尝试去改变原有的 `doJob()` 方法的自身功能，而是添加了两个
装饰者 `ConcreteDecoratorEx_1` 和 `ConcreteDecoratorEx_2` 来增强原有的功能。原有的
`doJob()` 功能并不会因为新增了装饰者而受到干扰。

## UML类设计图

![UML Class Diagram for Decorator Patterns.](/images/chapter_05/1_DecoratorPatterns_UMLClassDiagram.jpg)

## 包和类的结构图

![Package Explorer view for Decorator Patterns.](/images/chapter_05/1_DecoratorPatterns_PackageExplorerView.jpg)

## 实现代码

```java
package decorator.pattern.demo;

abstract class Component {
    public abstract void doJob();

}

class ConcreteComponent extends Component {
    @Override
    public void doJob() {
        System.out.println(" I am from Concrete Component-I am closed for modification.");

    }
}

abstract class AbstractDecorator extends Component {
    protected Component com;

    public void SetTheComponent(Component c) {
        com = c;
    }

    public void doJob() {
        if (com != null) {
            com.doJob();
        }
    }

}

class ConcreteDecoratorEx_1 extends AbstractDecorator {
    public void doJob() {
        super.doJob();
        //Add additional thing if necessary
        System.out.println("I am explicitly from Ex_1");
    }
}

class ConcreteDecoratorEx_2 extends AbstractDecorator {
    public void doJob() {
        System.out.println("");
        System.out.println("***START Ex-2***");
        super.doJob();
        //Add additional thing if necessary
        System.out.println("Explicitly From DecoratorEx_2");
        System.out.println("***END. EX-2***");
    }
}

class DecoratorPatternEx {
    public static void main(String[] args) {
        System.out.println("***Decorator pattern Demo***");
        ConcreteComponent cc = new ConcreteComponent();
        ConcreteDecoratorEx_1 cd_1 = new ConcreteDecoratorEx_1();
        // Decorating ConcreteComponent Object //cc with ConcreteDecoratorEx_1
        cd_1.SetTheComponent(cc);
        cd_1.doJob();
        ConcreteDecoratorEx_2 cd_2 = new ConcreteDecoratorEx_2();
        // Decorating ConcreteComponent Object //cc with ConcreteDecoratorEx_1 &
        //ConcreteDecoratorEX_2
        cd_2.SetTheComponent(cd_1);//Adding //results from cd_1 now
        cd_2.doJob();
    }
}
```

## 输出结果

![Output for Decorator Patterns.](/images/chapter_05/1_DecoratorPatterns_Output.jpg)

## 笔记

_装饰者模式有哪些优点?_  
1. 我们可以为某个特定对象添加新功能，而不会影响到已有的对象。
2. 我们可以递增性的编写代码。例如，我们先写一个简单的类，然后再按需为它添加装饰者。结果就是，
我们用不着在最开始设计类的时候就考虑得方方面面。另一方面，我们得知道先创建一个复杂的类然后再想
扩展它的功能是一个很复杂的过程。


_这个设计模式与继承有什么不同?_  
如果我们想为某个类添加或者删除职责，只需要把某个装饰者附加到类上 或者 从类上分离下来。如果改用
简单的继承方法的话，我们就得为新的职责创建新的类。这样会导致许多个类出现在系统里，也就意味着继承
反而会使系统变得更复杂。

_这个设计模式的最大缺点在哪里?_  
如果我们谨慎的使用这个设计模式的话，是没有什么缺点可言的。但是，在系统内创建过多的装饰者的话，
维护和调试这个系统就会变得越来越难。与此同时，过多的装饰者也会导致不必要的混乱。
