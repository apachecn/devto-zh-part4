# 有没有可能用更简单的方式编写这段代码？

> 原文：<https://dev.to/nanythery/is-it-possible-to-write-this-code-in-a-simpler-way-1a76>

我只是写了这段代码来点亮灯泡和关掉它。

灯泡有一个“开”等级。和一个“关闭”类。因此，为了处于某种状态，必须至少有一个为#灯泡激活的类。

我首先想到的是切换“开”类，然后关闭，但是没有成功，原因有两个:

classList.toggle 函数一次只允许一个类。因此，如果当时包含“开”类，则“关”类不会消失。

2)两个类此时不能共存，因为它们正在格式化同一个对象。所以我得到的是一个黄色阴影的灯泡。

所以我想出了这个条件，让它消失。

我很想知道其他可能使它更简单的方法。有什么建议吗？

对我温柔点，这是我用 JavaScript 写的第一个工作代码。:)

[点击查看密码本](https://codepen.io/nanythery/pen/WBVVMQ)

这是代码:

function switch bulb(){
var element = document . getelementbyid(" bulb ")；
var status = element . class list . toggle(" on ")；
如果(状态！= = false){
var status = element . class list . remove(" off ")；
} else
var status = element . class list . add(" off ")；
}