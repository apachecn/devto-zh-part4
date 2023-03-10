# 反转字符串中的单词[询问者🚖螺栓]

> 原文：<https://dev.to/alexwawl/reverse-words-in-string-asked-by-bolt-31pd>

*您好👋，
我的名字是[亚历克斯](https://twitter.com/alexwawl)，我是【Devmates.co】的[的制造者](https://devmates.co)。*😊

我们一起解决顶级科技公司提出的日常编码问题。我们分享我们的解决方案，如果有人遇到困难，我们会给予帮助，互相支持，一起在网上闲逛。🍻☕️💬

我从我们的会员那里或者只是在网上搜索(leetcode 论坛、Glassdoor、teamblind 等)得到这个问题。).这个问题有时很简单，有时也很简单👷‍♂️hard，一切都取决于技术，但我们正试图解决所有这些问题，并从中获得乐趣。🙌

今天我想分享一下博尔特提出的问题(🚖Taxify)。

欢迎在评论中分享你的解决方案。我很想找到“更好”的解决方案，并把它放到文章中。😉

##### 问题:

反转给定字符串中的每个单词。

你不能使用语言提供的方法(如拆分、反转等。)

**例题**

```
__Input:__ 'hello this is devmates'
__Output:__ 'olleh siht si setamved' 
```

##### 解:

想法很简单——遍历字符串并检查所有字符:

*   如果当前字符是空格，则将其添加到结果字符串中。
*   如果当前字符不是空格，那么我们应该开始把它累积到临时字符串中。

让我们从迭代字符串
开始

```
function reverseWordsInString(str){
        let result = '';
        for (let i = 0; i < str.length; i++){
          // here we should check is it space or not
          // if not then we should iterate over the current word
        }
        return result;
    } 
```

我们可以使用 **while 语句**对单词进行迭代。此外，我们使用相同的索引 **i** ，所以我们不会重复相同的单词两次。如果当前字符是空格' '，那么我们应该把它直接添加到我们的结果中。

```
function reverseWordsInString(str){
        let result = '';
        for (let i = 0; i < str.length; i++) {
          if (str[i] !== '  '){
            while(str[i] !== '  ' && i < str.length){
              // here we can iterate over the current word.
              i++;
            }
          } else {
            result += str[i];
          }
        }
        return result;
    } 
```

让我们以相反的顺序将我们的字累加到 **tmp** 变量中，并保存到结果中。

```
function reverseWordsInString(str){
        let result = '';
        for (let i = 0; i < str.length; i++) {
          let tmp = '';
          if (str[i] !== '  '){
            while(str[i] !== '  ' && i < str.length){
              tmp = str[i] + tmp;
              i++;
            }
            result += tmp + (str[i] ? str[i] : '');
          } else {
            result += str[i];
          }
        }
        return result;
    } 
```

这个解决方案**不好**，因为我们在两个循环中使用了相同的索引。我们如何优化它？-仅使用一个回路。我们也可以使用该功能来识别当前字符是否是分隔符。如果需要，这提供了添加更多分离器机会。

#### 优化方案

```
 function reverseWordsInString (phrase) {
          let result = "";
          let reversed_word = "";
          for (let i = 0; i < phrase.length; i++) {
              if (isSeparator(phrase[i])) {
                  result += reversed_word;
                  result += phrase[i];
                  reversed_word = "";
              } else {
                  reversed_word = phrase[i] + reversed_word;
              }
          }

          if (reversed_word.length > 0){
               result += reversed_word;
          }

          return result;
      }

      function isSeparator(symbol){
          switch (symbol) {
              case '  ':
                  return true;
              default:
                  return false;
          }
      }

      module.exports = reverseWordsInString; 
```

现在我们可以测试它了。让我们使用 [Jest](http://jestjs.io) 为我们的解决方案编写一些测试。

```
 // /../someFile.js
      function reverseWordsInString (phrase) {
          let result = "";
          let reversed_word = "";
          for (let i = 0; i < phrase.length; i++) {
              if (isSeparator(phrase[i])) {
                  result += reversed_word;
                  result += phrase[i];
                  reversed_word = "";
              } else {
                  reversed_word = phrase[i] + reversed_word;
              }
          }

          if (reversed_word.length > 0){
               result += reversed_word;
          }

          return result;
      }

      function isSeparator(symbol){
          switch (symbol) {
              case '  ':
                  return true;
              default:
                  return false;
          }
      }

      module.exports = reverseWordsInString; 
```

```
// /../someFile.test.js
    let reverseWordsInString = require('./someFile.js');

    test('Empty string', ()=>{
      expect(reverseWordsInString("")).toBe("")
    });

    test('One word', ()=>{
      expect(reverseWordsInString("hello")).toBe("olleh")
    });

    test('Valid string with single spaces', ()=>{
      expect(reverseWordsInString('hello this is devmates!')).toBe('olleh siht si !setamved')
    });

    test('Only spaces in string', ()=>{
      expect(reverseWordsInString("  ")).toBe("  ")
    });

    test('Multiply Spaces at the beginning in given string', ()=>{
      expect(reverseWordsInString(" hello this is devmates!")).toBe(" olleh siht si !setamved")
    });

    test('Multiply Spaces at the end in given string', ()=>{
      expect(reverseWordsInString("hello this is devmates! ")).toBe("olleh siht si !setamved ")
    });

    test('Multiply spaces at the beginning and at the end in given string', ()=>{
      expect(reverseWordsInString(" hello this is devmates! ")).toBe(" olleh siht si !setamved ")
    });

    test('Multiply spaces everywhere in given string', ()=>{
      expect(reverseWordsInString(" hello    this     is devmates ")).toBe(" olleh    siht     si setamved ")
    }); 
```

*感谢阅读。*🙌

希望你喜欢。我会在这里分享一些我们的问题和解决方案。你也可以参加我们在[Devmates.co](https://devmates.co)的活动，不要错过任何问题。💪

如果你有任何其他解决方案(例如就地解决)，请在评论中分享。💬

*祝你好运，玩得开心*😉