# 将不带空格的文本拆分成 Node.js 中的单词列表

> 原文：<https://dev.to/parsakafi/split-text-without-spaces-into-list-of-words-in-node-js-28c9>

对于我最近的项目，我需要一个技巧来分割包含英语单词的字符串，以分隔单词。我的栈是 Node.js，没有为我的需要开发包。

例如，我需要将字符串“youneedtolearnfromyourmistakes”拆分为单词“你需要从错误中学习”。

出于这个原因，我开始寻找开发一个适合我需要的包。最后，通过一些开源项目，我开发了一个将字符串拆分成单词的包。名为“文字忍者”的软件包已经发布。

[https://github . com/parsakafi/worsninja](https://github.com/parsakafi/wordsninja)
[https://npmj . com/package/worsninja](https://www.npmjs.com/package/wordsninja)

## 安装

```
npm install wordsninja --save 
```

加载包

```
const WordsNinjaPack = require('wordsninja');
const WordsNinja = new WordsNinjaPack(); 
```

加载字典

```
await WordsNinja.loadDictionary(); // First load dictionary 
```

添加单词

```
WordsNinja.addWords('new word'); 
```

因素

*   `word`:单词(字符串|数组)

### 拆分句子

```
let words = WordsNinja.splitSentence(string, {camelCaseSplitter, capitalizeFirstLetter, joinWords}); 
```

因素

*   `string`:拆分的字符串
*   `options`
    *   `camelCaseSplitter`:按骆驼箱分割，默认为`false`(可选)
    *   `capitalizeFirstLetter`:首字母大写，默认为`false`(可选)
    *   `joinWords`:将连接词作为句子返回，默认为`false`(可选)

示例

```
(async () => {
    await WordsNinja.loadDictionary(); // First load dictionary
    let string = 'youneedtolearnfromyourmistakes';
    let words = WordsNinja.splitSentence(string);
    console.log(words);
})(); 
```

结果

```
[ 'you', 'need', 'to', 'learn', 'from', 'your', 'mistakes' ] 
```

#### 更多选项

```
let string = 'youneedtolearnfromyourmistakes';
let words = WordsNinja.splitSentence(string,
    {
        camelCaseSplitter: true,  // Frist camel case spliting
        capitalizeFirstLetter: true,  // Capitalize first letter of result
        joinWords: true  // Join words
    }
);
console.log(words); 
```

结果

```
You Need To Learn From Your Mistakes 
```

### 添加单词

您可以在运行时向词典添加新单词

```
WordsNinja.addWords('Parsa');  // Add one word
WordsNinja.addWords(['Parsa', 'Kafi']); // Add one or more words 
```

示例

```
let string = 'parsayouneedtolearnfromyourmistakes';
WordsNinja.addWords('Parsa');
let words = WordsNinja.splitSentence(string,
    {
        capitalizeFirstLetter: true,  // Capitalize first letter of result
        joinWords: true  // Join words
    }
);
console.log(words); 
```

结果

```
Parsa You Need To Learn From Your Mistakes 
```

希望你觉得有用:)