# #100DaysOfPHP 的模块 PHP 中的决策

> 原文：<https://dev.to/aphatheology/module-3-of-100daysofphp-decision-making-in-php-41jl>

PHP 允许我们基于某种逻辑或比较条件来执行动作。基于这些条件的结果，即真或假，将按照用户的要求执行动作。这就像一条双向的道路。如果你想要什么，那就走这条路，或者转到那条路。

为了使用这个特性，PHP 为我们提供了四个条件语句:

*   如果语句
*   if…else 语句
*   if…elseif…else 语句
*   交换语句

让我们看看每个条件语句是如何工作的

## **1。if 语句**

这个语句允许我们设置一个条件。为 TRUE 时，将执行 if 子句中包含的以下代码块。

**语法**
if(条件){ //如果为真则执行此代码}

**举例**
<？PHP
$ x = 12；
if ( $x > 0)
{ echo“数为正”；} ?>

**输出**
的数字为正

## **2。if…else 语句**

我们知道，如果一个条件成立，即真，那么 if 中的代码块将被执行。但是如果条件不为真，我们想执行一个动作呢？这也是其他因素发挥作用的地方。如果条件为真，则执行 If 块，否则执行 else 块。

**语法**
if(条件)
{ //如果为真则执行此代码}
else{ //如果为假则执行此代码
}

**举例**
<？PHP
$ x =-12；
if ( $x > 0)
{ echo“数为正”；}
else{ echo“数为负数”；}
？>

**输出**
数字为负数

## **3。if…elseif…else 语句**

这允许我们使用多个 if…else 语句。当有多个真实情况的条件时，我们使用这个。

**语法**
if(条件){ //如果为真则执行此代码}
elseif { //如果为真则执行此代码}
elseif { //如果为真则执行此代码}
else if {//如果为假则执行此代码}

**举例**
<？PHP
$ x = " August "；
if ( $x == "一月")
{ echo "共和国日快乐"；}
else if($ x = = " August ")
{ echo "独立日快乐！！!"；}
else{ echo“无所展示”；} ?>

**输出**
独立日快乐！！！

## **4。开关状态语句**

switch 语句用于根据不同的条件执行不同的操作。

**语法**
switch(n){ case label 1:n = label 1 时要执行的代码；打破；
case label 2:n = label 2 时要执行的代码；打破；
case label 3:n = label 3 时要执行的代码；打破；
…
默认:n 与所有标签不同时要执行的代码；}

**举例**
<？PHP
$ fav color = " red "；
switch($ favcolor){ case " red ":echo“你最喜欢的颜色是红色！”；打破；
案例“蓝色”:呼应“你最喜欢的颜色是蓝色！”；打破；
案例“绿色”:呼应“你最喜欢的颜色是绿色！”；打破；
默认:echo“你最喜欢的颜色不是红、蓝、绿！”；}
？>

## **跳转报表**

### **打破声明**

在 PHP 或任何编程语言中，如果任何特定条件为真，则使用 break 语句立即停止程序执行并退出程序。

PHP 中的循环或切换语句使用 break 语句

**举例**
<？PHP for($ I = 1；$ I<= 10；$ i++)if($ I = = 6){ break；} echo $i，“
”；}
？>

### **继续陈述**

PHP 或任何编程语言，如果任何特定条件为真，那么 continue 语句跳过程序执行，转到下一个程序。例”；}

### **退出声明** ###

PHP 中的 exit()函数是一个内置函数，用于输出消息和终止当前脚本。exit()函数只终止脚本的执行。即使调用了 exit()函数，关闭函数和对象析构函数也将始终被执行。要显示的消息作为参数传递给 exit()函数，它终止脚本并显示消息。exit()函数是 die()函数的别名。

**举例**
<？PHP//声明变量
$ a = 5；$ b = 5.0
if($ a = = $ b){//使用 exit() exit('变量相等')终止带有消息的脚本；} else {
//使用 exit() exit('变量不相等')以消息终止脚本；}
？>

**输出**变量相等

这个旅程变得越来越有趣，任务也越来越多，在练习和学习很多东西的同时也犯了很多错误。如果你能建议一个资源来进行 PHP 迷你项目(需要基础知识)，我将不胜感激，我认为这对这个领域有很大帮助，谢谢！