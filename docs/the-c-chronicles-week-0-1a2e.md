# C#编年史-第 0 周

> 原文：<https://dev.to/jo/the-c-chronicles-week-0-1a2e>

我已经开始在我当地的编码训练营学习 C#课程，希望能扩展我的技能，成为我目前公司的完整堆栈开发人员。我决定将我的流程记录到:

*   让我自己负责
*   记下我的笔记——回头看作为参考
*   发展我作为技术写作者的技能

第一堂课涉及环境和所有有趣的爵士乐。这对我来说相对顺利，尽管是在 mac 上。实际上这很顺利，因为我在课前安装了软件。有所准备总是好的。我们学习了一些 C#的基础知识，以及如何在 Visual Studio(代替 Visual Studio 社区)中运行它。如果您还想从 Visual Studio 运行 c#，请在您的扩展中搜索 c#。哒哒！

我通过在终端运行“dotnet run”学会了如何运行一个简单的 c#程序。
为了创建一个新的控制台 app，我们运行命令‘dot net new console-n Primitive Types’，其中-n 是 app 的名称；在这种情况下，应用程序的名称是“PrimitiveTypes”。此命令将输入创建新控制台应用程序时附带的所有文件和文件夹。
[![](img/fc249df2b24956582d2e727200bdc042.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mwGXDyyO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/921efsc1ph0ofdcuq3iy.png) 
我已经圈出了创建新控制台应用程序时出现的初始设置文件。

为了运行您的代码，请在命令行或终端中键入“dotnet run”。

### 基本类型

我喜欢 c#的一点是，它是一种严格的类型语言，这意味着你可以设置你的类型，你可以设置你的变量为整数，字符串等。例如:

```
int myFavNumber=27;
string myName="Jo"; 
```

Enter fullscreen mode Exit fullscreen mode

也就是说这个变量是一个整数。这被称为隐式对显式对隐式类型转换。所以在 c#中，如果你使用`var`关键字而不是`int`，那么这就是隐式类型，因为你是在要求程序找出类型是什么。显式是我告诉程序我希望我的类型是什么。尽管在 c#中可以更改“var”(很像 javascript)，但不能更改“const”(也很像 javascript)

C#中有几种不同的数字类型。每种类型的变化是它们占用的内存量和各自的特殊性。例如，小数比浮点数占用更多的内存和特性。
例如:

```
float myFavFloat=3.14f;
decimal myFavDec=100.01m; 
```

Enter fullscreen mode Exit fullscreen mode

这里的 *f* 和 *m* 有助于表示数字的类型。

### 基本类型:带整数的数学

你可以用整数做数学运算，只是要小心你使用的类型，因为在除法的情况下特殊性确实适用。所以:
`decimal MyFavNum=10m/3m`会给出比`int MyFavNum=10/3`更具体的结果

### 字符串

字符串是另一种数据类型。这里有一些关于字符串类型的有趣的东西。如果使用单引号声明字符串类型，它将被定义为 char(读作 car ),这是一种不同的数据类型。例如，在 javascript 中，使用双引号还是单引号并不重要。

在 c#中，我们可以用几种不同的方式连接(或添加字符串)。

```
string myFavColor="Yellow";
string mySecFavColor="Purple";
Console.WriteLine(myFavColor + " "+ mySecFavColor); 
```

Enter fullscreen mode Exit fullscreen mode

这里的结果将是“黄紫色”

另一种连接字符串的方法是变量插值，我们使用美元符号并将变量名括在花括号中。

```
string myFavColor="Yellow";
string mySecFavColor="Purple";
Console.WriteLine($"{myFavColor} + " "+ {mySecFavColor}"); 
```

Enter fullscreen mode Exit fullscreen mode

### 布尔

布尔有真有假。为了声明一个布尔值，我们:
`bool isDone=true;`

### 控制流程

```
 {
            Console.WriteLine("Hello World!");
            Console.WriteLine("What is your name?");
            string name = Console.ReadLine();
           if(name==""){
                Console.WriteLine("I didn't hear you");
          } 
           else if(name=="Jo" && 7>5){
            Console.WriteLine("Hello Boss");
          }
           else{
                  Console.WriteLine($"Hello {name}");
            } 
       } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们介绍一下控制台。ReadLine 方法，在这里我们可以接受用户的输入。控制流看起来也与 javascript 的控制流相似，甚至语法也非常相似。

### 控制流量开关

代替几个 else-if 语句，我们可以使用一个 *switch 语句*向用户提供多个结果。在本例中，我们使用 switch 语句根据一周中的日期为用户提供不同的答案。

```
Console.WriteLine("Hello World!");
            Console.WriteLine("What day of the week is it?");
            string input=Console.ReadLine().ToLower();

            switch(input){
                case "monday":
                Console.Write("5 days till the weekend");
                break;

                case "tuesday":
                Console.Write("Tuesday blues but you can do it!");
                break;

                case "wednesday":
                Console.Write("Happy humpday!");
                break;

                case "thursday":
                Console.Write("Almost friday!");
                break;

                case "friday":
                Console.Write("tgif amirite");
                break;

                case "saturday":
                 case "sunday":
                Console.Write("bottomless mimosas");
                break;

                default:
                Console.WriteLine("I don't understand that input");
                break;
            }

            Console.WriteLine("Bye!");

        } 
```

Enter fullscreen mode Exit fullscreen mode

为了提高开关的使用，我们可以使用*默认值*和*穿越值*。我们在 switch 的末尾插入一个默认值，当前面的条件都不满足时，它将作为我们的最终“else”。这种情况下的失败是**案例“星期六”**，您希望向用户提供相同的结果，而不考虑答案。在这种情况下，既然是周末，我们希望答案是“无底洞含羞草，不管是周六还是周日。

## 控制流程:For 循环

for 循环是一种反复运行代码块直到满足条件的方法

```
Console.WriteLine("Hello World!");
           for (int i=0; i < 10;i++) 
            { 
           if(i==3){ 
              Console.WriteLine("i is equal to " + i); 
              continue;
            } 

              Console.WriteLine("i is not 3 it is " + i); 

           } 

           Console.WriteLine("end"); 
```

Enter fullscreen mode Exit fullscreen mode

continue 语句说，“当你到达这一行时，重新开始循环，继续下一次迭代”
**break**语句允许我们说，如果我们已经找到了我们要找的东西，继续前进并停止循环。

### 控制边流边循环

我们使用 while 循环来继续运行相同的代码，它可以无限期地运行，直到停止。它通常用于继续运行应用程序，直到应用了某个条件。while 循环不应该等同于 false。

```
 int i=0; 
          while(i<100){
          i++;
            if(i%2==0){
             continue;
             } 
             Console.Write(i); 
```

Enter fullscreen mode Exit fullscreen mode

第 0 周到此结束！