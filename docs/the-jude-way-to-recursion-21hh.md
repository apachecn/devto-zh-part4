# 递归的 Jude 方法

> 原文：<https://dev.to/niyiojeyinka/the-jude-way-to-recursion-21hh>

所以在我们开始之前，让我给你介绍一下，裘德，裘德已经睡了几个小时了，他似乎有点不寻常，所以我们拿出了我们想象中的工具来检测某人睡眠中的任何异常，我们在那个时候检测到，他在做梦，也在他睡觉的梦中，还发现他自己梦见自己在睡觉，也在做梦......
所以我们继续检测我们亲爱的裘德到底在做什么梦，并意识到他一直在梦想拥有一辆特斯拉
，在他梦想的最低层次，他拥有一辆更高型号的汽车，随着梦想层次的提高，他拥有一辆更低型号的汽车。  
[![Alt text of image](img/0dc0585d12ff6252052249805810d5bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yk2-yVS_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/djjd7Yz.png)

D1——在第一层次做梦
D2——在第二层次做梦
D3——在第三层次做梦
D2——在第四层次做梦

当他继续做梦的时候，我们希望他没有进入一个无休止的梦循环，并且他很快起床(当基本情况满足时)。

故事讲完了，我认为发生在裘德身上的事情可以说是一个递归(递归的形容词)梦，那么什么是
递归呢？递归就是定义一个函数或一个对象，在它的定义中调用一个函数或对象。根据维基百科的说法，它被定义为一个更简单且通常更小的 T2 版本。
嗯，你可能会想“我们他妈的需要递归做什么？”递归可以用来执行重复的任务，就像迭代循环
，尽管它不是迭代的完美替代或替代，因为它可能需要更多的空间，因为每个函数调用在到达基本
情况之前都存储在堆栈中。
堆栈？哦，那是什么？根据 wikipedia，堆栈是抽象数据类型，充当元素的集合，有两个主要操作
Push(添加元素)和 Pop(删除元素)。该操作可以以两种不同的方式进行

先进后出
和
后进先出

你可以把它看作储物容器，甚至可以把它想象成一摞书，如下图所示

[![Stack of books](img/25ae2a79bcc3d213148b1ebda820d1f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--42viKb4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://justpaste.it/img/b2638aaa4a76ff4d1c650613184931f7.jpg)

很明显，上面的例子遵循了**后进先出**的顺序，因为如果我们要安全地从书堆
中取出书而不使任何人倒下，我们必须从顶部开始。

在本文中，我们将使用递归来计算一个数的幂，并用
JAVA 实现。

//代码

...

```
class Test{

    public int repeat(int x, int n){

        if (n == 0) {
            return 1;
        }
        return  x*repeat(x,n-1);

    }
    public static void main(String[] args){
        Test test = new Test();
        System.out.println( test.repeat(5,3));

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

...

解释:

[![Alt text of image](img/f5424a9aae1eb0718456b2e0aff05f5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D8QTnUje--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/JUeeSZV.png)

从左到右，当我们第一次调用 powerOf()时，它的详细信息保存在堆栈中，当我们向上移动时(检查向上箭头)，它继续将函数的简单版本保存到堆栈内存中，直到到达基本情况，然后将结果值返回到下一个堆栈(检查向下箭头)，直到到达返回结果的主堆栈。

发现什么错误了吗？请在回复部分让我知道。总之，我刚刚完成了我的第一篇文章。