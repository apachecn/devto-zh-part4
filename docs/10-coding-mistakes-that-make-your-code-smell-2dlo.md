# 让你的代码变臭的 10 个编码错误

> 原文：<https://dev.to/hussein_cheayto/10-coding-mistakes-that-make-your-code-smell-2dlo>

我通过浏览几个不同的程序、编写书籍(如安迪·亨特&迪夫·托马斯的《实用程序员》和罗伯特·c·马丁的《干净的代码》)和重构它们，创建了这个列表。当我做出每一个改变时，我总是在想我为什么要做出这样的改变。结果是当我读代码时，一个很长的清单让我觉得很糟糕。在本文中，我提到了我在阅读代码时遇到的 10 大编码错误。

## **1-多余的注释**

如果注释描述的是描述自身的东西，那么它就是多余的。
**例如:**
i++；//增量 I

## **2-写得不好的评论**

花点时间写评论，确保花点时间让它成为你能写的最好的评论。使用正确的语法和标点符号，并保持简洁。
**例如:**

public void display tutorial()//使用时，将打开教程窗口显示教程

**修正:**

public void display tutorial()//显示窗口

**相关文章:** [没有学位也能成功的 5 大网络开发者](https://dev.to/hussein_cheayto/top-5-web-developers-you-should-follow-to-succeed-without-a-degree-25lg)

## **3-注释掉的代码**

你见过多少次几百个代码被评论？？谁知道它有多老？谁知道它是否有意义？然而，没有人会删除它，因为每个人都认为其他人需要它或对它有计划。这段代码放在那里腐烂，随着时间的推移，变得越来越臭。解决方案？？？很简单。删了就好！！不要担心，如果有人真的需要，他/她可以回去查看以前的版本。

## **4-参数太多**

函数应该有少量的参数。没有争论是最好的，其次是一，二，三。超过三个是非常值得怀疑的，应该带着偏见避免。

**相关文章:** [10 人跟随在没有学位的情况下获得游戏开发工作](https://dev.to/hussein_cheayto/10-people-to-follow-to-land-a-job-in-game-development-without-a-degree-4lo)

## **5-一个功能多个任务**

理想情况下，每个功能应该只服务于一个任务。

**比如:**

public void Calculate(float a，float b，float c)
{
if(Add)
{
a = b+c；
} else if(subtract)
{
a = b-c；
}else if(乘)
{
a = b * c；
}
}

**更正:**
public void Add(浮点 a，浮点 b，浮点 c)
{
a=b+c
}

public void Subtract(float a，float b，float c)
{
a=b-c

public void Multiply(float a，float b，float c)
{
a=b*c
}

## **6-失效功能**

从不被调用的方法应该被丢弃。保留死代码是一种浪费。不要害怕删除功能。记住，你的源代码控制系统仍然记得它。

**相关文章:** [睡觉时发财](https://dev.to/hussein_cheayto/get-rich-while-sleeping-j3n)

## **7-不一致**

一致性，当它应该被应用时，可以使代码更容易阅读和修改。不一致是以某种方式做某事的习惯。一旦完成，以同样的方式做所有类似的事情。小心你选择的惯例，一旦选择，小心继续遵循它们。
**例如:**
使用变量名为‘Quit’和‘Exit’，而不是拘泥于‘Quit’或‘Exit’。

## **8-重复**

Duplication is a very serious problem. Almost every author who writes about software design mentions this rule. Dave Thomas and Andy Hunt called it the DRY3 principle (Don’t Repeat Yourself). Kent Beck made it one of the core principles of Extreme Programming and called it: “Once, and only once.”
It's simple, find and eliminate duplication wherever you can.
**For example:**
Duplications appear when using the switch/case or if/else chain that appears again and again in various modules, always testing for the same set of conditions.
**Correction**
Think about using polymorphism.

**相关文章:** [如何变得成功——第三集——布莱恩·格拉](https://dev.to/hussein_cheayto/how-to-become-successful-episode-3-bryan-guerra-4c0e)

## **9-错位的责任**

One of the most important decisions a software developer can make is where to put code.
**For example:**
where should the PI constant go? Should it be in the Math class? Perhaps it belongs in the Trigonometry class? Or maybe in the Circle class?

代码应该放在读者自然会想到的地方。PI 常量应该放在声明 trig 函数的地方。我们将把它放在一个对我们来说方便的函数中，但对读者来说不一定直观。

## **10-函数和变量名**

函数名应该说明它们做什么。总是选择描述性的名字，花时间挑选一个名字，这是值得的。你会花一些时间去思考，但是当你在一个月或一年后阅读你的代码时，这肯定会节省你的时间。
**看这段代码:**
Date newDate = Date . add(5)；
你会希望这给日期增加五天吗？或者是几周，或者几个小时？
从调用看不出函数是干什么的。

**更正:**
如果函数给日期加了五天，改变了日期，那么应该叫 IncreaseByDays 或者 AddDays。