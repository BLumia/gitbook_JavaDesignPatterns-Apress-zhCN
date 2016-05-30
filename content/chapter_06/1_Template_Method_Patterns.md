# 第6章 模板方法模式

GoF给出的定义：为算法定义一个框架，并将算法中的某些步骤延迟到子类。模板方法能让其子类重新定义
算法的某些步骤，而不会影响到算法的整个结构。

## 概念

在模板方法中，我们先给算法定义一个最小化的（或者说 最基本的）结构，再把一些功能（或者说 职责）
延迟到它的子类里。这样，我们就能在不改变算法原有结构的情况下，重新定义算法里的一些步骤。

## 现实世界中的例子

比如我们想生产Pizza。不管怎么生产Pizza，它基本制作方法都是不变的。但批萨的配料会随着顾客的需求
而变动，比如有些顾客吃素食而有些不是。

## 计算机世界的例子

比如有个工程专业的学生。一般情况下，第一学期的课程的核心内容都一样。之后，再基于学生的具体学科
布置相应的学术论文（比如 计算机科学，电子工程）。

## 图解

下面的小程序用模板方法模式简单的实现了一个工程专业的课程。我们假设所有的工程专业的学生必须完成
数学和人际交往两门课程才能拿到最终学历。之后，再根据学生的专业添加相应的学术论文。

## UML类设计图

![UML Class Diagram for Template Method Patterns.](/images/chapter_06/1_TemplateMethodPatterns_UMLClassDiagram.jpg)

## 包和类的结构图

![Package Explorer view for Template Method Patterns.](/images/chapter_06/1_TemplateMethodPatterns_PackageExplorerView.jpg)

## 实现代码

```java
// BasicEngineering.java
package engineering.papers;

public abstract class BasicEngineering {
    // Papers() in the template method
    public void Papers() {
        //Common Papers:
        Math();
        SoftSkills();
        //Specialized Paper:
        SpecialPaper();
    }

    //Non-Abstract method Math(), SoftSkills() are //already implemented by Template class
    private void Math() {
        System.out.println("Mathematics");
    }

    private void SoftSkills() {
        System.out.println("SoftSkills");
    }

    //Abstract method SpecialPaper() must be implemented in derived classes
    public abstract void SpecialPaper();
}

// ComputerScience.java
package engineering.papers;

public class ComputerScience extends BasicEngineering {
    @Override
    public void SpecialPaper() {
        System.out.println("Object Oriented Programming");
    }
}

// Electronics.java
package engineering.papers;

public class Electronics extends BasicEngineering {
    @Override
    public void SpecialPaper() {
        System.out.println("Digital Logic and Circuit Theory");
    }
}

// TemplateMethodPatternEx.java
package template.method.pattern.Demo;

import engineeringPapers.*;

class TemplateMethodPatternEx {
    public static void main(String[] args) {
        System.out.println("***Template Method Pattern Demo***\n");

        BasicEngineering bs = new ComputerScience();
        System.out.println("Computer Sc Papers:");
        bs.Papers();
        System.out.println();
        bs = new Electronics();
        System.out.println("Electronics Papers:");
        bs.Papers();

    }
}
```

## 输出结果

![Output for Template Method Patterns.](/images/chapter_06/1_TemplateMethodPatterns_Output.jpg)

## 笔记

1. "代码复用"是这个设计模式的基本目标，我们能在许多类库中看到这种模式的应用。
2. GoF建议，我们在设计模板方法类的时候，需明确的选定哪些方法应作为钩子方法（Hook operation.
覆盖这个方法是可选的，不覆盖的话父类也会提供默认的实现），哪些方法应作为抽象方法
（Abstract operation. 也就意味着必须在子类中实现它）。

_使用这个设计模式时，我们应避免哪些主要问题?_  
我们需要将抽象（不完整的）方法的数量4减到最少。（注意：在Java里，抽象方法里不能有具体代码）
否则，每个子类都得覆盖模板方法，这个设计模式也就失去了它的意义。
