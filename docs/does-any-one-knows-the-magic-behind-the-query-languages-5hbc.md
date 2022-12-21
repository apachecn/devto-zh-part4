# 有人知道查询语言背后的魔力吗？

> 原文：<https://dev.to/nhanquach/does-any-one-knows-the-magic-behind-the-query-languages-5hbc>

大家好，
最近我被分配了一项任务，我需要翻译 2，3 条从口语到 JavaScript 表达式和条件的映射逻辑规则。
我设法让它工作的方法是定义一堆可能的答案，然后写下许多陈述。但是这对于想要在以后改变映射逻辑的客户和需要维护并向团队其他成员解释的我来说似乎很难。

| 答案 1 | 答案 2 | 结果 |
| --- | --- | --- |
| 是 | 不 | one |
| 不 | 是 | Two |

映射 json

```
[{  "answer1":  "Yes",  "answer2":  "No",  "result":  1  },  {  "answer1":  "No",  "answer2":  "Yes",  "result":  2  }] 
```

```
// FE will send to BE an array of answers and BE needs to return the result

// This is terrible for scaling or dynamic mapping logic 
if(answer1 === 'Yes' && answer2 === 'No') {
  return 1;
} else if(answer1 === 'No' && answer2 === 'Yes') {
  return 2
} 
```

这真的让我想到了 SQL/非 SQL 查询语言背后的魔力，我真的很佩服它们。

如果`result`不仅仅是一个数字，可能是另一个规则呢？我们如何用 json 格式定义一个规则(例如:如果结果是 1，我想得到所有大于 10 的奇数)，然后用 JavaScript 读取并执行它？

不知道社区里有没有人可以给我一个提示/解决这个问题的方法？

非常感谢。