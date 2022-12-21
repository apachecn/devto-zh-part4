# Leetcode:“独特的莫尔斯电码单词”有趣的 JavaScript 一行解决✨

> 原文：<https://dev.to/acupoftee/leetcode-unique-morse-code-words-fun-javascript-one-line-solution-2j2o>

这是我解释解决编码问题的方法的系列文章的一部分。这是为了帮助我更好地表达我的思维过程，并为开发人员启发新的解决问题的方法！

**问题陈述:**

> 国际莫尔斯电码定义了一种标准编码，其中每个字母映射到一系列点和破折号，如下所示:“a”映射到”。-“，”b“映射到“-...”，“c”映射到“-”。-."，等等。现在，给定一个单词列表，每个单词可以写成每个字母的莫尔斯电码的串联。比如“cba”可以写成“-”。-..- ..."，(这是连接“-”。-."+ "-..."+ ".-").我们称这样的连接为单词的转换。返回所有单词中不同变换的数量。

**方法** :
我们需要返回给定单词列表的莫尔斯表示的数量。解决这个问题的方法如下:

1.  看`words`里的每个字。
2.  对于每个单词，看每个字母。
3.  获得每个字母的莫尔斯表示
4.  用莫尔斯字母造单词
5.  一旦我们完成了单词的构建，如果它不在我们的集合中，就把它添加到我们的集合中
6.  返回*唯一*莫尔斯表示的数量。

**解决方案** :

```
const alphabet = {
    a: '.-', b: '-...',   c: '-.-.', d: '-..', e: '.', f: '..-.', g: '--.', h: '....', i: '..',  j: '.---',  k: '-.-',  l: '.-..', m: '--',
    n: '-.',  o: '---', p: '.--.',  q: '--.-',  r: '.-.', s: '...', t: '-', u: '..-', v: '...-', w: '.--', x: '-..-',  y: '-.--', z: '--..' 
}

/**
 * @param {string[]} words the word array to convert to morse
 * @return {number} the length of unique morse representations
 */
const uniqueMorseRepresentations = words => {  
    // PREVIOUS SOLUTION:
    // return [...new Set(words.map(word => word.split('').map(letter => alphabet[letter]).join('')))].length

    // Edited on 9-9-2019
    return new Set(words.map(word => word.split('').map(letter => alphabet[letter]).join(''))).size
} 
```

Enter fullscreen mode Exit fullscreen mode

**解说** :
这是个好玩的问题套用 [Array.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ！因为我们改变了数组中的每个元素，`map()`工作得很好。我们将使用示例`words = ['gin', 'zen', 'gig', 'msg']`来解释这是如何工作的:

对于每个单词，我们将在第一次使用`map()`时拆分每个单词的字符。看完一个词我们会有这样的东西:

```
[ ['g', 'i', 'n'], 'zen', 'gig', 'msg' ] 
```

Enter fullscreen mode Exit fullscreen mode

我们将在第一个 map 方法中再次使用`map()`。我们将这样做，看看内部数组的每个字母，以创建一个莫尔斯码字。

对于第二次使用`map()`时的每个字母，我们将获得该字母的莫尔斯电码，并使用`join('')`用它造一个单词。两种`map()`方法使用一次后，我们将得到`'gin'`的第一个莫尔斯单词:

```
['--...-.', 'zen', 'gig', 'msg'] 
```

Enter fullscreen mode Exit fullscreen mode

我们将重复步骤 1-3，直到我们有了一组新的莫尔斯电码单词:

```
['--...-.', '--...-.', '--...--.', '--...--.'] 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 JavaScript 的 [Set 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)来只保留唯一的表示。Set 构造函数接受一个数组，这将是步骤 2 中显示的数组，然后删除重复的元素，所以我们将只剩下唯一的莫尔斯电码单词:

```
['--...-.', '--...--.'] 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以像这样返回集合的大小:

```
return new Set(words.map(word => word.split('').map(letter => alphabet[letter]).join(''))).size 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*Thanks for reading! As always questions, feedback, and ideas are always encouraged. Happy hacking!*