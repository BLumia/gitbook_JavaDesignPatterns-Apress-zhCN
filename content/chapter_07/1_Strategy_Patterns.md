# 第7章 策略模式
GoF给出的定义：定义了一系列算法，把他们逐个封装起来并使它们可互相替代。策略模式使得算法可以独立于客户随意变化。

## 概念

我们可以在运行时动态的指定和变更某个的算法的行为。

## 现实生活中的例子

通常，在一场足球比赛快要结束的时候，红队比蓝队领先一分时。红队则会将进攻阵型转为防御阵形，而蓝队则会为了拿分而采取全面进攻阵型。

## 计算机世界的例子

上述情形也可以应用于电脑足球游戏中，或者，想象两种专用的存储器都取决于同一个存储器的实现，我们初始时将数据存储在第二个可用的存储器。所以，在我们储存信息前做运行时检查是必要的，并且在这种情况下，执行处理。

## 图解

我们可以根据下面的程序和类图来理解策略模式。在主函数中，我们为用户随意的选取两个arbitary（当然，你想选多少个都行）。根据用户的输入，我们的context objects将决定哪个choice应被设置和显示。

## UML类设计图
![UML Class Diagram for Strategy Patterns.](/images/chapter_07/1_StrategyPatterns_UMLClassDiagram.png)

## 包和类的结构图
High-level structure of the parts of the program is as follows:
![Package Explorer view for Strategy Patterns.](/images/chapter_07/1_StrategyPatterns_PackageExplorerView.png)

## 实现代码
```java
// IChoice.java
package choices;
public interface IChoice
{
    void myChoice(String s1, String s2);
}
//FirstChoice.java
package choices;
public class FirstChoice implements IChoice
{
    public void myChoice(String s1, String s2)
    {
        System.out.println("You wanted to add the numbers.");
        int int1, int2, sum;
        int1 = Integer.parseInt(s1);
        int2 = Integer.parseInt(s2);
        sum = int1 + int2;
        System.out.println(" The result of the addition is:" + sum);
        System.out.println("***End of the strategy***");
    }
}
// SecondChoice.java
package choices;
public class SecondChoice implements IChoice
{
    public void myChoice(String s1, String s2)
    {
        System.out.println("You wanted to concatenate the numbers.");
        System.out.println(" The result of the addition is:" + s1 + s2);
        System.out.println("***End of the strategy***");
    }
}
//Context.java
package contextofchoice;
import choices.IChoice;
public class Context
{
    IChoice myC;
    // Set a Strategy or algorithm in the Context
    public void SetChoice(IChoice ic)
    {
        myC = ic;
    }
    public void ShowChoice(String s1, String s2)
    {
        myC.myChoice(s1, s2);
    }
}
// StrategyPatternEx.java
package strategy.pattern.demo;
import java.io.IOException;
//For Java old versions-to take input from user
//import java.io.BufferedReader;
//import java.io.InputStreamReader;
/* Java 5 added a nice utility class called Scanner, to get input from user */
import java.util.Scanner;
import contextofchoice.Context;
//import choices.*;
import choices.FirstChoice;
import choices.IChoice;
import choices.SecondChoice;
class StrategyPatternEx
{
    public static void main(String[] args) throws IOException
    {
        System.out.println("***Strategy Pattern Demo***");
        Scanner in = new Scanner(System.in); //To take input from user
        IChoice ic = null;
        Context cxt = new Context();
        String input1, input2;;
        //Looping twice to test two different choices
        try
        {
            for (int i = 1; i <= 2; i++)
            {
                System.out.println("Enter an integer:");
                input1 = in.nextLine();
                System.out.println("Enter another integer:");
                input2 = in.nextLine();
                System.out.println("Enter ur choice(1 or 2)");
                System.out.println("Press 1 for Addition,
                                   2 for Concatenation");
                String c = in.nextLine();
                /*For Java old versions-use these lines to collect input
                from user
                BufferedReader br = new BufferedReader
                ( new InputStreamReader( System.in ));
                String c = br.readLine();*/
                if (c.equals("1"))
                {
                    /*If user input is 1, create object of FirstChoice
                    (Strategy 1)*/
                    ic = new FirstChoice();
                }
                else
                {
                    /*If user input is 2, create object of SecondChoice
                    (Strategy 2)*/
                    ic = new SecondChoice();
                }
                /*Associate the Strategy with Context*/
                cxt.SetChoice(ic);
                cxt.ShowChoice(input1, input2);
            }
        }
        finally
        {
            in.close();
        }
        System.out.println("End of Strategy pattern");
    }
}
```

## 输出结果

![Output for Strategy Patterns.](/images/chapter_02/1_StrategyPatterns_Output.png)

## 笔记

What is the power behind the strategy pattern?

1. This pattern can provide dynamic behavior for us. It can help us to avoid dealing
with complex algorithm-specific data structures.
2. With this pattern, the same behavior can be expressed differently. So, users can
have a wide variety of choices.

What are the challenges associated with the strategy pattern?

1. The number of objects are increased in the system.
2. Additional overhead is needed due to communication between the strategies
and their contexts.
3. Users need to be fully aware of all kinds of possible behaviors to avoid confusion.