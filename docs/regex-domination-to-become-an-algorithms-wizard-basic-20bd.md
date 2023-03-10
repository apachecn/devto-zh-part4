# 正则表达式支配成为一个算法向导(基本)

> 原文：<https://dev.to/uptheirons78/regex-domination-to-become-an-algorithms-wizard-basic-20bd>

在本文中，我想仔细看看我在学习编码时发现的最奇怪的论点之一:正则表达式。每当我用正则表达式工作时，我觉得自己就像琼恩·雪诺在耶哥蕊特面前听她说 ***“你什么都不知道，琼恩·雪诺】*** 。大概是因为每当我接触到正则表达式时，我似乎对它们一无所知，☹.似乎总有我错过或不知道的新东西要学。我喜欢认为任何一个超级程序员第一次接触 Regex 的感觉都和我一样。

在 [FreeCodeCamp](https://www.freecodecamp.org/) 或 [Codewars](https://www.codewars.com/) 上对算法进行大量练习后，我可以告诉你，正则表达式在开始时很难理解，但在解决不同类型的 Javascript 问题时非常有用和友好。
那么:让我们开始潜入正则表达式的世界吧！

***正则表达式到底是什么？***
据 **Mozilla 开发者网**正则表达式有:

> 用于匹配字符串中字符组合的模式。

它的意思是 Regex，简称 Regex，允许我们在字符串中检查一系列匹配的字符。它们通常用于验证数据、执行高级搜索、替换或更新文本等。当你需要验证代码中的电话号码、电子邮件地址或密码时，你需要使用正则表达式。
所以...学着点！😄

#### 创建正则表达式

在 Javascript 中，有几种方法可以创建正则表达式:
a. **Regex Literal** :
这是最常用的语法，也是我将在本文中使用的语法。
语法:`let regex = /wizard/;`

b.**正则表达式构造器** :
当你需要动态创建正则表达式时，这个语法非常方便。
语法:`let regex = new RegExp('wizard');`

现在我们知道了如何创建一个正则表达式，让我们用我们的知识来创建一个简单的:
第一个正则表达式:***/wizard/***；
这是我们要检查正则表达式的字符串:*“成为一个算法向导是非常困难的！”*。
结果:*“成为一个算法**向导**很辛苦！”*。
我们创建的正则表达式**在字面上与单词‘wizard’**相匹配。不多不少。

#### 正则表达式 JS 方法

在 Javascript 中，使用正则表达式有不同的方法。我只谈几个。
a. ***test()*** 测试正则表达式是否匹配给定的字符串。它返回一个布尔值。
举例:

```
 let string = 'May the Force be with you';
  let regex = /Force/;
  let result = regex.test(string);
  console.log(result); // true 
```

b. ***match()*** 返回包含所有匹配字符组的数组。它总是根据字符串进行测试。如果没有找到匹配，则返回 ***null*** 。
举例:

```
 let string = 'May the Force be with you';
  let regex = /Force/;
  let result = string.match(regex);
  console.log(result);
  /**
   * output:
   * [ 'Force',
        index: 8,
        input: 'May the Force be with you',
        groups: undefined
      ]
  */ 
```

#### 简单的图案

到目前为止，我们只使用这种正则表达式。它匹配一个文字模式。让我们再举一个例子:

```
let string = 'I find your lack of faith disturbing.';
let regex = /faith/;
let result = string.match(regex);
console.log(result);
/**
 * [ 'faith',
    index: 20,
    input: 'I find your lack of faith disturbing.',
    groups: undefined
  ]
 */ 
```

#### 与旗帜同乐

正则表达式区分大小写**和**只寻找第一个匹配**。Regex 的发明者斯蒂芬·科尔·克莱尼先生是个好人，他给了我们检查不区分大小写和多个匹配的可能性。他让我们使用所谓的 T7 旗。它们使用起来也很简单:只需将它们添加在结束的正斜杠之后。请记住，您可以组合不同的标志，顺序不会影响搜索。
语法:`/pattern/flags`
a .***g***-第一次匹配后不返回的全局标志
示例:** 

```
 //No Global Flag Example
  let string = 'force force force';
  let regex = /force/; // no global flag
  let result = string.match(regex); //it will return only the first match at index 0
  console.log(result);
  /**
   * [ 'force',
      index: 0,
      input: 'force force force',
      groups: undefined
    ]
  */

  //Example with Global Flag
  let string = 'force force force';
  let regex = /force/g; // global flag on
  let result = string.match(regex); // it will all matches
  console.log(result); //['force', 'force', 'force']; 
```

B. ***i*** -不区分大小写标志
例子:

```
 //No Case Insensitive Flag
  let string = 'force force Force'; //last word is uppercased
  let regex = /force/g; // no case-insensitive flag
  let result = string.match(regex); //it will return only the first two matches
  console.log(result); //['force', 'force'];

  //Case Sensitive Flag
  let string = 'force force Force'; //last word is uppercased
  let regex = /force/gi; // case-insensitive flag
  let result = string.match(regex); //it will return all matches
  console.log(result); //['force', 'force', 'Force']; 
```

#### 字符集

它匹配方括号**/【ABC】/**中的字符串中的任意一个字符。
比如上面的**/【ABC】/**的意思是“嘿，在字符串里面寻找 **a** 、 **b** 或者**c**”
语法:`/[abc]/`它匹配一个字符串里面的 a、b 或者 c。
示例:

```
let string = 'foo poo zoo';
let regex = /[fpz]oo/gi; // matches any f, p or z followed by 'oo'
let result = string.match(regex);
console.log(result); //[ 'foo', 'poo', 'zoo' ]

//Using another Character Set it is possible to match any Vowels like 'o' 😉
//Take a look:
let string = 'foo poo zoo';
let regex = /[fpz][aeiou][aeiou]/gi; // matches any f, p or z followed by a couple of vowels
let result = string.match(regex);
console.log(result); //[ 'foo', 'poo', 'zoo' ]

//Another one:
let string = 'foo faa fee';
let regex = /f[aeiou][aeiou]/gi; // matches any f followed by a couple of vowels
let result = string.match(regex);
console.log(result); //[ 'foo', 'faa', 'fee' ] 
```

#### 否定字符集

如果在左括号`/[^abc]/`之后有一个脱字符号，那么它将匹配未括在括号中的任何内容。
比如上面的`/[^abc]/`就是*的意思“哎，找一个人物但是 **a** 、 **b** 或者 **c** 在串里面”*。
示例:

```
let string = 'fun sun gun';
let regex = /[^fs][aeiou]n/gi; // matches anything but f and s followed by a vowel and a 'n'
let result = string.match(regex);
console.log(result); //[ 'gun' ]

//Look at this one
let string = 'fun sun gun';
let regex = /[fsg][^aeiou]n/gi; // matches f,s or g followed by a letter except a vowel and then by a n
let result = string.match(regex);
console.log(result); //null 
```

#### 范围

方括号也可以包含在字符串中查找的字符范围，比如**/【a-c】/**。
例如，上面的**/【a-c】/**表示*“嘿，在字符串中寻找从 **a** 到 **c** 的一系列字符”*。它将搜索 a、b 和 c。
使用这个正则表达式很容易找到字母表中的任何字母:**/[a-z]/**/
语法:`/[a-c]/`它匹配一个字符串中的 a、b 或 c。
示例:

```
let string = 'fear tear bear';
let regex = /[a-z]ear/gi; // matches any letter of the alphabet followed by 'ear'
let result = string.match(regex);
console.log(result); // ["fear", "tear", "bear"]

//Another example with a range of numbers
let string = 't85 x76 c9';
let regex = /[a-z][0-9][0-9]/gi; // matches any letter of the alphabet followed by 2 numbers from 0 to 9
let result = string.match(regex);
console.log(result); //["t85", "x76"] 
```

#### 取反范围

像字符集一样，甚至可以否定一系列字符，例如，`/[^a-z]/`表示*“嘿，从字母表中获取除小写字母以外的任何字符，我们真的不需要它！”*。
例子:

```
let string = '88c foo @4c';
let regex = /[^a-z][^a-z][a-z]/gi; // matches a couple of non alphabet letter followed by a letter
let result = string.match(regex);
console.log(result); //["88c", "@4c"] 
```

#### 元字符

正则表达式中的字符具有非常特殊的含义。我将介绍最常用的。
a. **\d** 匹配任何数字，如【0-9】
b .**\ w**匹配任何单词字符，它表示任何字母、数字和下划线，如【a-zA-Z0-9 _】
c .**\ s**匹配一个空白字符。空格、制表符等。
d. **\t** 匹配一个制表符。
e. **。**句点匹配除换行符之外的任何字符。
f. **\D** 匹配任何非数字字符，如【^0-9].
g. **\W** 匹配任何非单词字符，如【^a-zA-Z0-9_].
h. **\S** 匹配一个非空白字符。

#### 量词

它们是在正则表达式中具有特殊含义的符号。

a. **+** 匹配前面的表达式 1 次或多次(无限制)
例如:

```
 let string = '8 88 888';
  let regex = /\d+/g; // matches a pattern of one or more digits
  let result = string.match(regex);
  console.log(result); //["8", "88", "888"]

  //Look here:
  let string = 'force 88 888';
  let regex = /\d+/g; // matches a pattern of one or more digits
  let result = string.match(regex);
  console.log(result); //[88", "888"]

  //Look at this piece of cake
  let string = 'gd god good goooooooooood';
  let regex = /go+d/g; // matches a pattern of one or more letter o
  let result = string.match(regex);
  console.log(result); //["god", "good", "goooooooooood"] 
```

b. **/*/** 匹配前面的表达式 0 次或更多次(无限制)

```
 let string = 'gd god good goooooooooood';
  let regex = /go*d/g; // matches a pattern of zero or more letter o
  let result = string.match(regex);
  console.log(result); //["gd", "god", "good", "goooooooooood"] 
```

c.**？**匹配前面的表达式 0 或 1 次，所以前面的模式是可选的

```
 let string = 'god good goooooooooood';
  let regex = /goo?d/g; // matches god or good and the second o is optional!
  let result = string.match(regex);
  console.log(result); //["god", "good"] 
```

d. **^** 匹配字符串的开头，后面的正则表达式必须在测试字符串的开头。

```
 let string = 'force';
  let regex = /^f/; // the string must start with a f
  let result = regex.test(string);
  console.log(result); //true

  //Another example
  let string = '888';
  let regex = /^[0-9]/; // the string must start with a number
  let result = regex.test(string);
  console.log(result); //true

  //Another one
  let string = 'pass0999';
  let regex = /^[0-9]\w+/; // the string must start with a number and followed by any word chars
  let result = regex.test(string);
  console.log(result); //false 
```

e. **$** 匹配字符串的结尾，并且之前的正则表达式必须在测试字符串的结尾。

```
 let string = 'pass0999';
  let regex = /\d$/; // the string must end with a digit
  let result = regex.test(string);
  console.log(result); //true 
```

f. **{x}** 精确匹配前一个正则表达式的 x 次出现。

```
 let string = 'god good goood';
  let regex = /go{2}d/gi; // check for g followed by 2 letter o and then g
  let result = string.match(regex);
  console.log(result); //["good"] 
```

g. **{x，}** 与前面的正则表达式至少匹配 x 次。

```
 let string = 'god good goood';
  let regex = /go{2,}d/gi; // check for g followed by at least 2 letters o and then g
  let result = string.match(regex);
  console.log(result); //["good", "goood"] 
```

h. **{x，y}** 匹配前面正则表达式的至少 x 次出现和最多 y 次出现。

```
 let string = 'god good goood gooood';
  let regex = /go{2,3}d/gi; // check for g followed by at least 2 and at most 3 letters o and then d
  let result = string.match(regex);
  console.log(result); //["good", "goood"] 
```

i. **x|y** 匹配 x 或 y.

```
 let string = 'good';
  let regex = /good|bad/; // check either for 'good' or 'bad'
  let result = regex.test(string);
  console.log(result); //true

  //And
  let string = 'bad';
  let regex = /good|bad/; // check either for 'good' or 'bad'
  let result = regex.test(string);
  console.log(result); //true 
```

**注意** —如果您想要使用任何特殊字符作为正则表达式的一部分，比如说您想要匹配文字。还是？，你必须用反斜杠\

#### 最后，快速回顾一下我们在第一篇关于 Regex Basic 的文章中看到的内容

```
let regex;
//SINGLE REGEX PATTERN
regex = /abc/; //match a specific string

//FLAGS
regex = /abc/i; //match a specific string case insensitive
regex = /abc/g; //match a specific string globally
regex = /abc/gi; //match a specific string globally and case insensitive

//CHARACTER SETS AND NEGATIVE CHARACTER SETS
regex = /[abc]/; //match a specific characters set
regex = /[^abc]/; //not match a specific characters set

//RANGE OF CHARACTERS AND NEGATIVE RANGE OF CHARACTERS
regex = /[a-z]/; //match a range of characters
regex = /[^a-z]/; //not match a range of characters

//METACHARACTERS
regex = /\d/; // matches any digit
regex = /\D/; // matches any non-digit
regex = /\w/; // matches any word character, underscore included (a-z, A-Z, 0-9, _)
regex = /\W/; // matches any non-word character
regex = /\s/; // matches any white space character (\r (carriage return),\n (new line), \t (tab), \f (form feed))
regex = /\S/; // matches any non-white space character
regex = /\t/; // matches any tab

//QUANTIFIERS
regex = /[0-9]+/; //match a range of characters for one or unlimited times
regex = /[0-9]{2}/; //match a range of characters exactly 2 times
regex = /[0-9]{2,3}/; //match a range of characters from 2 to 3 times
regex = /[0-9]{2,}/; //match a range of characters from 2 to unlimited times 
```

这就是正则表达式的基础。一篇关于更多高级特性的新文章将随后发表。与此同时，别忘了练习这些基本技能。如果你愿意，你可以在一个非常好的网站上在线完成，比如 [Regex101](https://regex101.com) 。

我将根据大家的反应和评论，用新的信息和一些关于 regex 的算法来更新这篇文章。
如果您觉得这篇文章有帮助，请点击💖或者🦄按钮并分享这篇文章，这样它可以帮助别人。
在[推特上关注我](https://twitter.com/UpTheIrons1978)。

***码长而亨通*****