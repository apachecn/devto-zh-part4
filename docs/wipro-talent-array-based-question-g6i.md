# 基于 Wipro 人才阵列的问题。

> 原文：<https://dev.to/pykashchain/wipro-talent-array-based-question-g6i>

1)简单编码数组:Maya 在一个数组(array of int)中存储了很少的机密数字。为了确保其他人不容易找到这些数字，她使用了一种简单的编码。
使用的编码:每个数组元素都被替换为一个值，该值是其原始值与其后续元素值之和。
即 arr[i] = arr[i] + arr[i+1]
例如 arr[0]中的值= arr[0]的原始值+arr[1]的原始值
还要注意最后一个元素即 arr[last index]的值保持不变。

示例:
如果原始数组是-
{ 2，5，1，7，9，3}
则编码后的数组将是-
{ 7，6，8，16，12，3}

如果提供了编码数组，您应该找到–
a)原始数组中的第一个数字(索引 0 中的值)
b)原始数组中所有数字的总和

函数的原型是:
public static void findinoriginalfirstandsum(int[]input 1)；
其中 input1 是编码数组。
该方法预计将

*   找到原始数组的第一个数字的值，并将其存储在成员 output1 和
*   找出原始数组中所有数字的和，并将其存储在成员 output2 中

假设:

*   数组元素可以是正数和/或负数

示例 1:
原始数组= {2，5，1，7，9，3}
编码数组= {7，6，8，16，12，3}
原始数组中的第一个数字= 2
原始数组中所有数字的总和= 27
注意:只有“编码数组”将被提供给函数，它将执行处理以找到预期的结果值。

[https://repl.it/@VikasTomar/Wipro-talent?lite=true](https://repl.it/@VikasTomar/Wipro-talent?lite=true)