# JS 中的基本正则表达式

> 原文：<https://dev.to/philipsterling/regular-expression-in-js-31bk>

正则表达式是用于描述文本中搜索模式的字符串。

```
var pattern = new RegExp(pattern, attributes);
//or 
var pattern = /pattern/attributes;
//eg pattern = /\n/g or pattern = new RegExp(\n,g)
//for newline characters globally 
```

事情变得复杂了

```
/[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,6}/i //What is this? 
```

模式示例-

为了更好地理解 regex 中的模式，我将展示一些复杂的例子，并把它们分解成易于理解的组件。

```
//Philip@example.com
/[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,6}/i //this is the regex for identifying an email address string 
//regardless of address

//First component
//Philip
/[A-Z0-9._%+-]/
//This component looks for a string containing a through z
//represented by A-Z or 0-9 represented by 0-9 then the other
//allowed characters in the first part of an email which are
// . _ % + -

//Second component
//Philip@
/+@/
//This component makes the return whatever block came before the
//@ sign in the email address and checks for the @ sign

//Third component
//Philip@example
/[A-Z0-9.-]/
//This component makes the return a any character block that includes
// a through z 0 through 9 . and -

//Fourth component
//Philip@example.
/+\./
//This component makes the return add a . by use of \. which
//escapes the dot in

//Fifth component
//Philip@example.com
/[A-Z]{2,6}/  //This component adds the values of any string a through z that is
//between 2 and 6 characters long.
//For example .co would work but .o or .comcomo would not 
```

属性示例-

这些通常用于字符“g”“I”和“m ”,允许您在整个字符串中进行全局搜索(g ),( I)不区分大小写，以及(m)多行匹配

```
//In our example case we used the attribute /i so that it would accept
//independent of case for any character a through z 
```

要知道的基本模式-

```
//Special Characters
//Any special characters have to be escaped into the regex 
//through the use of the \ character, these include
\0 - null

\t - tab

\n - newline

\. - .

\[ - [

//and so on

//Quantifiers
//Quantifiers determine the amount of a certain character or pattern you are looking for

a* - 0 or more a's

a+ - 1 or more a's

a? - 0 to 1 a's

a$ - any string that ends in a

^a - any string that begins with a

a{2} - exactly 2 a's in sequence

a{2,} - 2 or more a's in sequence

a{2,4} - 2 to 4 a's in sequence

//Metacharacters
//Metacharacters are used to represent a variety of character with
//limited input

. - any single character

\s - whitespace

\S - not whitespace

\d - decimal digit

\D - not a decimal digit

\w - alphanumeric character

\W - not alphanumeric character

{ab|bc} - or operator

//Ranges
//Ranges of characters are done in regex through bracket notation

[A-Z] - all characters a through z

[.] - all dots

[^.] - all not dots 
```

这是 regex 的所有基本输入，允许您更好地创建字符串搜索模式。

* * *

来源

https://regexr.com/-在实现正则表达式之前测试它们的有用工具。