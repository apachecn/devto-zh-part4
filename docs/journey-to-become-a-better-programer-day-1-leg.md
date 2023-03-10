# 成为更好的程序员之旅(第 1 天)

> 原文：<https://dev.to/montanacoder/journey-to-become-a-better-programer-day-1-leg>

今天我在 [HackerRank](https://www.hackerrank.com/challenges/js10-loops/problem) 上工作，完成了第 0-1 天的训练，并做了一些其他的练习。“
我对这个问题感觉非常好，想到了一个让我非常高兴的解决方案。

目标是:

1.  首先，在新的一行中打印每个元音。英语元音字母是 a、e、I、o 和 u，每个元音字母必须按照它在。

2.  其次，将每个辅音字母(即非元音字母)打印在新的一行中，顺序与它在中出现的顺序相同。

例子:
"devto "

应该这样打印:
e
o
d
v
t

我做的第一件事是使用`.split('')`从字符串中创建一个数组`let strArr=s.split('')`([@ laurieontech](https://dev.to/laurieontech)有一个很好的主题叫做[拼接！切！该死，我是说换档！](https://dev.to/laurieontech/splice-slice-shoot-i-meant-shift-424p))然后再多几个，一个元音`let v = []`，一个辅音`let c = []`。

接下来，我创建了一个只包含元音字母

`let vowels = ['a','e','i','o','u']`的静态数组。

然后，我做了一个`forEach()`循环(遍历数组中的每一项)(设置如下:`strArr.forEach(letter=>{})`‘letter’代表每一项)，在循环中我检查元音(`vowels`)数组是否包含我们在循环中使用的字母`if(vowels.includes(letter))`，如果是这样，我会将‘letter’推入一个新的元音数组(`v`)，辅音进入它们自己的

数组(`c`)。最后，将所有内容放入一个数组`return [...v ...c].join('\n')`中，然后`.join('\n')`将它们放入一个字符串中，在每个字母处创建一个新行。

把它放在一起看起来是这样的:

```
function vowelsAndConsonants(s) {
  let strArr = s.split('');
  let vowels = ['a', 'e', 'i', 'o', 'u'];
  let v = [];
  let c =[];

  strArr.forEach(letter => {
    if (vowels.includes(letter)) {
      v.push(letter);
    } else {
      c.push(letter);
    }
  })

  return [...v, ...c].join('\n');
} 
```