# 成为算法向导必须知道的 Javascript 字符串方法

> 原文：<https://dev.to/uptheirons78/javascript-string-methods-you-must-know-to-become-an-algorithms-wizard-c84>

在这篇文章中，我想谈谈 Javascript 中最常用的一些基本的字符串方法，它们在解决问题和算法时非常有用。在过去的 4 周里，我一直在 FreeCodeCamp 和 CodeWars 上致力于解决算法问题，发现我用了很多次这种方法。

如果你对数组方法感兴趣，你可以看看我的帖子:
[成为一名算法向导你必须知道的 Javascript 数组方法](https://dev.to/uptheirons78/javascript-arrays-methods-you-must-know-to-become-an-algorithms-wizard-2ec7)

1)用 ***获取字符串的长度。长度***

```
let str = "i am a string";
console.log(str.length); //13 
```

2)从带有 ***的字符串中获取数组。*【分裂()】T3。请记住，可以使用指定的分隔符字符串来确定每个拆分的位置。** 

```
const str = "Luke, I am your Father";
console.log(str.split());//[ 'Luke, I am your Father' ]
console.log(str.split(''));//["L", "u", "k", "e", ",", " ", "I", " ", "a", "m", " ", "y", "o", "u", "r", " ", "F", "a", "t", "h", "e", "r"]
console.log(str.split('  '));//[ 'Luke,', 'I', 'am', 'your', 'Father' ]
console.log(str.split(','));//[ 'Luke', ' I am your Father' ] 
```

让我们来看一个我在 CodeWars 上解决的简单算法，其中给定一串单词，函数必须返回一个等于单词长度的整数数组。

```
function wordsLength(str) {
  return str.split('  ') //first split the string at spaces to have an array of words;
            .map(word => word.length); //second use array map() to trasform any array element to its length with .length;
}

wordsLength('Luke, I am your father'); //[ 5, 1, 2, 4, 6 ] 
```

3)用***toupper case()***
将字符串转换成大写

```
const str = 'I find your lack of faith disturbing.';
console.log(str.toUpperCase()); //I FIND YOUR LACK OF FAITH DISTURBING. 
```

4)用 ***将一个字符串转换成小写字母 toLowerCase()***

```
const str = 'Help me, Obi-Wan Kenobi. You’re my only hope.';
console.log(str.toLowerCase()); //help me, obi-wan kenobi. you’re my only hope. 
```

5)检查一个字符串是否包含指定的字符 ***包括()*** 。它将返回一个布尔值(真或假)。可以在字符串中添加开始搜索的位置

```
const str = 'The Force will be with you. Always.';
console.log(str.includes('Force')); //true
//Attention: it is case sensitive!
console.log(str.includes('force')); //false
//Often it will be better to transform the given string to lowercased 
//and then check if it includes or not what you are looking for.
const newStr = str.toLowerCase();
console.log(newStr.includes('force')); //true
//Add the position where to start searching
console.log(str.includes('w', 0)); //true
console.log(str.includes('T', 1)); //false 
```

6)检查字符串是否以指定字符***【start with()***开头。它将返回一个布尔值，并且可以添加开始搜索的位置。它区分大小写。

```
const str = 'Never tell me the odds!';
console.log(str.startsWith('Never')); //true
console.log(str.startsWith('Never', 1)); //false
console.log(str.startsWith('never', 0)); //false 
```

7)用 ***endsWith()*** 检查字符串是否以指定字符结尾。它将返回一个布尔值，并且可以添加长度参数(可选)。它区分大小写。

```
const str = 'Do. Or do not. There is no try.';
console.log(str.endsWith('try.')); //true
console.log(str.endsWith('Try.')); //false
console.log(str.endsWith('try', 30)); //true
console.log(str.endsWith('try.', 30)); //false 
```

7)用 ***检查指定值在字符串中的第一次出现。*** 。如果该值不在字符串中，它将返回-1。可以添加第二个参数，从指定的索引开始搜索。

```
const str = 'When gone am I, the last of the Jedi will you be. The Force runs strong in your family. Pass on what you have learned.';
console.log(str.indexOf('h')); //1
console.log(str.indexOf('H')); //-1
console.log(str.indexOf('h', 2)); //17
console.log(str.indexOf('J', str.length)); //-1 
```

8)用 ***检查指定值在字符串中的最后一次出现。*lastIndexOf()**。如果该值不在字符串中，它将返回-1。可以添加字符串中最后一个字符的索引，作为匹配的开始。

```
const str = 'When gone am I, the last of the Jedi will you be. The Force runs strong in your family. Pass on what you have learned.';
console.log(str.lastIndexOf('h')); //105
console.log(str.lastIndexOf('h', 100)); //97
console.log(str.lastIndexOf('.')); //117
console.log(str.lastIndexOf('.', 0)); //-1 
```

9)用 ***重复一个给定的字符串。*重复()**。

```
const str = 'There’s always a bigger fish.';
console.log(str.repeat(2));//There’s always a bigger fish.There’s always a bigger fish.
//Attention: count will be converted to integer!
console.log(str.repeat(5.5));//There’s always a bigger fish.There’s always a bigger fish.There’s always a bigger fish.There’s always a bigger fish.There’s always a bigger fish. 
```

10)用 ***replace()*** 替换给定字符串中的模式。模式可以是字符串或正则表达式，替换可以是每次匹配时调用的字符串或函数。注意:如果替换符或模式是一个字符串，只有第一次出现的才会被替换。

```
const string = 'Fear is the path to the dark side.';
console.log(string.replace('Fear', 'Tears')); //Tears is the path to the dark side.
console.log(string.replace(/a/gi, 'A'));//FeAr is the pAth to the dArk side. 
```

11)使用 ***charAt()*** 从字符串中获取一个特定的字符。返回表示指定索引处的字符(恰好是一个 UTF-16 代码单位)的字符串。如果索引超出范围，则为空字符串！

```
const string = 'Fear leads to anger';
console.log(string.charAt(1));//e
console.log(string.charAt(string.length - 1));//r
console.log(string.charAt(string.length));//'' Index out of range!
//Attention: if no index is provided the default one is 0;
console.log(string.charAt());//F 
```

12)用 ***charCodeAt()*** 获取字符串中给定索引处字母的 UTF-16 编码。这种方法对于 ROT13 或 Caesar Cypher 这样的算法非常有用。如果没有提供索引，默认索引为 0。

```
const string = 'We must keep our faith in the Republic.';
console.log(string.charCodeAt(0));//87
console.log(string.charCodeAt(5));//115
//If you want all the UTF-16 values of any letter in a string
//split the string to have an array of letters
//map the array and change any letter to its utf-16 value with charCodeAt();
const utfValuesArr = string.split('').map(letter => letter.charCodeAt());
console.log(utfValuesArr);
//[87, 101, 32, 109, 117, 115, 116, 32, 107, 101, 101, 112, 32, 111, 117, 114, 32, 102, 97, 105, 116, 104, 32, 105, 110, 32, 116, 104, 101, 32, 82, 101, 112, 117, 98, 108, 105, 99, 46] 
```

13)使用静态***string . from charcode()***方法，从指定的 UTF-16 代码单元序列中获取一个字符串。

```
console.log(String.fromCharCode(65));//A
console.log(String.fromCharCode(105, 106, 107));//ijk
console.log(String.fromCharCode(32));//'' empty space!

const arr = [77, 97, 121, 32, 116, 104, 101, 32, 70, 111, 114, 99, 101, 32, 66, 101, 32, 87, 105, 116, 104, 32, 89, 111, 117];
const quote = arr.map(n => String.fromCharCode(n));
console.log(quote.join('')); //May the Force Be With You 
```

14)获取一段字符串，以一个新的字符串返回，不修改原字符串，用***【slice()***。它需要两个参数。BeginIndex，或者从哪里开始对字符串进行切片，以及可选的 EndIndex，从哪里停止对字符串进行切片。如果没有提供 EndIndex，它会将字符串切片到末尾。注意:负的索引从字符串的末尾开始倒数

```
const string = 'I’m just a simple man trying to make my way in the universe.';
console.log(string.slice(1));//’m just a simple man trying to make my way in the universe.
console.log(string.slice(0,10));//I’m just a
console.log(string.slice(-3));//se. 
```

15)获取开始和结束索引之间的字符串部分，或者用*将字符串结束。注意:任何小于 0 或大于 stringName.length 的参数值都被分别视为 0 和 stringName.length。任何为 NaN 的参数值都被视为 0。* 

```
const string = 'Darth Vader';
console.log(string.substring(0));//Darth Vader
console.log(string.substring(6));//Vader
console.log(string.substring(1,6));//arth 
```

16)用 ***trim()*** 去除字符串两端的空格。

```
const string = ' Yoda ';
console.log(string.trim());//Yoda 
```

我不想详尽列出所有 javascript 字符串方法，而是列出我发现在解决问题和算法时最重要的方法。为了更好地解决 JS 和问题，我建议多“玩”玩所有这些方法，并订阅[免费代码营](https://www.freecodecamp.org/)或[代码战](https://www.codewars.com/)网站，在那里你可以找到许多算法来使用，并重温你的 javascript 知识。
在 [Codewars](https://www.codewars.com/) 上，你可以寻找关于“字符串”的 7kyu 或 6kyu 算法，并用它们进行训练。会很好玩的！

我将根据大家的反应和评论，用新的信息和一些关于字符串的算法来更新这篇文章。

***码长而亨通****