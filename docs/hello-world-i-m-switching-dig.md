# 你好世界！我要换了！

> 原文：<https://dev.to/fihra/hello-world-i-m-switching-dig>

库穆斯塔世界！こんにちはせかい!阿罗哈霍努阿！世界你好！你好，世界！你好世界！

语码转换是一个语言学话题，指说话者在交谈中使用两种或两种以上的语言。

[代码转换维基百科来源](https://en.wikipedia.org/wiki/Code-switching)

每当你开始学习一门新的编程语言时，你编写的第一个程序。每当你点击编码语言教程的第一个介绍性章节时，你通常会试图以某种形式显示 Hello World。

```
#ruby
puts "Hello World" 
```

Enter fullscreen mode Exit fullscreen mode

```
//javascript
document.write("Hello World"); 
```

Enter fullscreen mode Exit fullscreen mode

```
//C#
Console.Writeline("Hello World"); 
```

Enter fullscreen mode Exit fullscreen mode

```
//C++
cout << "Hello World";
return 0; 
```

Enter fullscreen mode Exit fullscreen mode

```
%MatLab
disp('Hello World') 
```

Enter fullscreen mode Exit fullscreen mode

```
#Python print "Hello World" 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚看了一堆不同的语言说你好，世界。他们有不同的说法。让我们尝试用 for 循环遍历一个数字数组。
ruby
JavaScript
cs harp
CPP
MATLAB
python

```
#Ruby
numArray = [2, 5, 6, 1]

for n in numArray do
   puts n
end 
```

Enter fullscreen mode Exit fullscreen mode

```
//Javascript
const numArray = [2, 5, 6, 1]

for(let i=0; i <numArray.length; i++){
   document.write(numArray[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//C#
int[] numArray = new int[4]{2, 5, 6, 1};

for(int i=0; i < numArray.length; i++){
   Console.WriteLine(numArray[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//c++
int numArray[] = {2, 5, 6, 1}; //C# can also do this

for(int i=0; i < numArray.length; i++){
   cout << numArray[i];
} 
```

Enter fullscreen mode Exit fullscreen mode

```
%MatLab
numArray = [2 5 6 1]
for num = numArray
   disp(num)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
#Python numArray = [2, 5, 6, 1]
for num in numArray:
   print num 
```

Enter fullscreen mode Exit fullscreen mode

对于一个可能刚刚开始学习第一种语言的初学者来说，可能会被语法弄得不知所措，不要担心，它们输出的都是相同的东西，但本质上它们使用的是 for 循环，只是方式不同。

注意某些语言的 for 循环的一些模式:

相似之处:

*   Ruby，MatLab，Python
*   Javascript、C#、C++

但是数组看起来像是不同的设置。我目前正在学习 Ruby & Javascript，但我已经接触了 C#、Python 和一点 C++。我不得不查找更多关于 MatLab 的信息。我主要听说过它，因为我有一个生物工程毕业的朋友使用这种语言，我很好奇语法看起来怎么样。

在进入编码训练营之前，我自学了一点 Python 和 C#。当我开始学习 Javascript 和 Ruby 为入学做准备时，我对语言语法感到困惑，尤其是从 C#到 Ruby。我的想法是“实例化变量上的‘int’和‘string’关键字在哪里？！？难道我们不想锁定这些数据类型，这样我们就不必担心使用错误的数据了吗？?"但是我决定顺其自然地学习这门语言，因为我必须强迫我的大脑适应 Ruby。进入 Ruby 之后，我开始更加欣赏这种语言的功能和它提供的显示其“[ - Ruby Magic - ]”的方法。

我们花了最多前 6 周的时间学习 Ruby，然后是 Rails，到了第 7 周，我们转而学习 Javascript。自从使用 Ruby 以来，我几乎忘记了所有的 Javascript，尽管我已经做了入学前的准备工作。然而，这有点像步入 Javascript，调整起来会快一点，因为这不是我必须转移的语法，而是思维过程。在 Ruby land，我不得不遍历数据集合，访问数据，并更改数据。转到 Javascript，我觉得我现在知道我必须对我的数据这样做，但现在只是尝试按照 Javascript 的规则以不同的方式应用它。

我觉得一旦我们熟悉了一种语言，当我们用第一语言思考时，我们就可以和另一种语言的句法进行比较。我们可以看到该语言的哪些部分可以自动转移到另一种语言，比如条件语句和循环。通过转换来研究的新东西必须是方法、数组、对象以及可能更加陌生的代码的不同部分。

让我们来看一个代码，除了你这个 C 专家，可能不是所有人都熟悉的，但你可以帮助解释更多关于这个代码的内容。所以我只是从一个教程学习网站上随便抓了一段示例代码。

```
/*
 * C program to create a linked list and display the elements in the list
 */
#include <stdio.h> #include <malloc.h> #include <stdlib.h>  
void main()
{
    struct node
    {
        int num;
        struct node *ptr;
    };
    typedef struct node NODE;

    NODE *head, *first, *temp = 0;
    int count = 0;
    int choice = 1;
    first = 0;

    while (choice)
    {
        head  = (NODE *)malloc(sizeof(NODE));
        printf("Enter the data item\n");
        scanf("%d", &head-> num);
        if (first != 0)
        {
            temp->ptr = head;
            temp = head;
        }
        else
        {
            first = temp = head;
        }
        fflush(stdin);
        printf("Do you want to continue(Type 0 or 1)?\n");
        scanf("%d", &choice);

    }
    temp->ptr = 0;
    /*  reset temp to the beginning */
    temp = first;
    printf("\n status of the linked list is\n");
    while (temp != 0)
    {
        printf("%d=>", temp->num);
        count++;
        temp = temp -> ptr;
    }
    printf("NULL\n");
    printf("No. of nodes in the list = %d\n", count);
} 
```

Enter fullscreen mode Exit fullscreen mode

本代码来源:[https://www . sanfoundry . com/c-program-create-linked-list-display-elements/](https://www.sanfoundry.com/c-program-create-linked-list-display-elements/)

我并不理解这段代码中的所有内容，但是，凭借 javascript、ruby 和 C#的一些背景知识，我已经可以看出一些相似之处。

我注意到‘while’，‘if’，‘else’是非常通用的关键字，因为语言仍然需要基本的循环和条件。“printf”很可能是 C 的打印语句，我只能猜测“scanf”接受某种用户输入。我仍然不熟悉 linkedlist 主题，所以这将是我未来的学习。

我会看到我完全不知道的语法，比如 malloc，*(因为它的设置方式，看起来不像是乘法运算符)，-->(看起来可能使用类似，但功能与 Javascript 的 arrow 函数不同)，struct(可能是 class)。“void main()”对于 ruby 或 javascript 来说也是创建块的一种不同方式，但我在 C#中见过它，还有“public”或“static”。Ruby & Javascript 可能会将整个块分组为类、定义或函数。

对我来说，这可能是一个太高深的话题，但是我觉得我能够阅读和理解它所做的部分代码，这是来自一个没有深入 C 语言的人。我确实有一些 C#背景，所以我想这可能是一种欺骗，因为他们是 C 家族的，但仍然！从目前学习 Javascript 的情况来看，我觉得我的 C#知识对此相当模糊。

如果你刚开始学习编程，我建议你专注于一门语言，直到你对这门语言有 75%-80%的熟悉，你就可以创建一些简单的应用程序。如果你已经学会了一种语言，现在正在转换到另一种语言，太好了！你可以切换到另一个学习材料，但你现在可以移动得更快一点，因为几个部分很可能更容易做到，因为你现在已经从你的第一语言体验过了。

这里有一些学习材料，可以帮助你开始学习一些语言。对于 Python & Ruby，我会建议 Zed“艰难地学习[编程语言]”，因为他假设你对编程一无所知，并分解代码。

python:
[https://www.souravsengupta.com/cds2015/python/LPTHW.pdf](https://www.souravsengupta.com/cds2015/python/LPTHW.pdf)

Ruby:
[https://www . Amazon . ca/Learn-Ruby-Hard-Way-Computational/DP/032188499 x](https://www.amazon.ca/Learn-Ruby-Hard-Way-Computational/dp/032188499X)

我通过几门 Udemy 课程学习了 C#。

c#:
[https://www.udemy.com/topic/c-sharp/](https://www.udemy.com/topic/c-sharp/)

Codecademy 也有一些语言，C++，Javascript 等:
[https://www.codecademy.com/](https://www.codecademy.com/)

TutorialsPoint 也有很多资源，MatLab:
[https://www.tutorialspoint.com/matlab/](https://www.tutorialspoint.com/matlab/)