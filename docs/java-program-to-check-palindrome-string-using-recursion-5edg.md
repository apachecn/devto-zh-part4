# 使用递归检查回文字符串的 Java 程序

> 原文：<https://dev.to/ranlohith/java-program-to-check-palindrome-string-using-recursion-5edg>

如何在 java 中用递归检查一个字符串是否是 java 中的回文是最常见的 java 面试问题。

递归意味着函数调用自己。在这个 java 程序中，创建了第一个带有字符串参数“str”的“checkPalindrome()”方法。

该方法首先使用 if 语句检查用户输入的字符串长度是 0 还是 1。这里，如果字符串长度等于 0 或 1，那么字符串就是回文。

导入 Java . util . scanner；
public class RecursivePalindromeJava
{
public static booleancheck 回文(String str)
{
if(str . length()= = 0 | | str . length()= = 1)
返回 true
if(str . charat(0)= = str . charat(str . length()-1))
返回检查回文(str.substring(1，str . length()-1))；
返回假；
}
公共静态 void main(String[]args)
{
Scanner sc = new Scanner(system . in)；
System.out.println("请输入一个字符串:")；
String strInput = sc . next line()；
if(check 回文(strInput))
{
system . out . println(strInput+"是回文")；
}
else
{
system . out . println(strInput+"非回文")；
}
sc . close()；
}
}

输出:

请输入一个字符串:mom
mom 是回文。