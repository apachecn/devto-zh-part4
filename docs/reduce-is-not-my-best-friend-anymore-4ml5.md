# Reduce 不再是我最好的朋友了

> 原文：<https://dev.to/klvenky/reduce-is-not-my-best-friend-anymore-4ml5>

我是文卡特什。我从事网页开发已经有一段时间了。这是我在实践中所学的一个具体的 reduce 案例的简单解释。

我是 [Array.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 的忠实粉丝。我曾经是一名 java 开发人员，后来由于新项目的需要，我开始学习 javascript。我对 Java 集合不太熟悉，但不是一个好的集合。因为，我不理解 lambda 函数(java 版本的 arrow 函数),我不知道 map/filter/reduce 是什么意思。我已经阅读了几乎所有可用的文章，以了解其中的区别。最后，reduce 通过一篇类似于`implement your own map/filter using reduce`的精彩文章拯救了我们。我读了那篇文章，觉得它超级疯狂。

这对我来说是一种恩惠。我开始广泛使用`reduce`每次我必须做任何地图/过滤器过滤。我喜欢它，因为它给了我控制。人们认为我疯狂地到处使用 reduce，这是显而易见的。这是我记得的最简单的实现，用于加倍一个数字数组，并使用 reduce 过滤偶数。

```
const nums = [1, 2, 3, 4, 5, 6];
// Double the nums array
const numsDoubled = nums.reduce((acc, num) => {
  const temp = [...acc]; // I used Array.from(acc) at that time though
  temp.push(num * 2);
  return temp;
}, []);
// find the even numbers
const evenNums = nums.reduce((acc, num) => {
  const temp = [...acc];
  if (num % 2 === 0) temp.push(num); // Didn't know 0 was falsy back then
  return temp;
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

作为当时的我，我爱它胜过一切。慢慢地，我明白了什么是地图和过滤器，以及它们应该做什么。我想，“最终我会把这些东西用在正确的地方”。

### 问题

这就是我解决这个问题的全部历史。现在来谈谈我面临的实际问题，我从一个客户那里收到了一个 CSV 文件，大约有 70k-90k 行，30+列。我必须做一些计算，做一些条件检查，挑选出几个重要的字段。所以，我又开始使用我最喜欢的减肥法。

```
function extractData(records) {
  return records.reduce((acc, record) => {
    const { id, ...rest } = record;
    const others = computeRestFields(rest); // some mapping function
    const temp = { ...acc };
    temp[id] = others;
    return temp;
  }, {});
}
const file = fs.readFileSync("client-feed.csv");
const parsedData = csvParse(file); // returns an array of data
extractData(parsedData); 
```

Enter fullscreen mode Exit fullscreen mode

我已经测试了大约 100 行，对它的预期效果感到满意，并将其推送到一个无服务器函数。然而，我注意到这是内存不足的问题。然后，我开始调试，意识到我的代码太占用内存了。于是，开始寻找替代品。

### 备选 1:

```
function extractData(records) {
  return records
    .map(record => {
      const others = computeRestFields(rest);
      return { id, others };
    })
    .reduce((acc, record) => {
      const t = { ...acc };
      const { id, others } = record;
      t[id] = others;
      return t;
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

我第一个想法是改成贴图再减，而不是一下子全减。经过一番挖掘，我认为 spread 操作符的数量实际上可能会损害内存限制。这是因为，我在每一次迭代中创建了一个具有数千个键的新对象。所以，我试着把它分割成地图，然后像上面的第一种方法一样缩小。不出所料，它没有工作，因为我的无服务器提供程序的内存上限是 2GB。我被迫尝试另一种方法。

我试图通过使用 lodash 来增加操作的数量，使其更具功能性，使其成为多个操作，每个操作占用的空间都很小(至少我当时是这么想的)。但是，这些都没有成功。因此，我想到了替代方案，并想对传统的 for 循环做最后一次尝试。结果是备选方案 2。

### 备选 2:

```
function extractData(records) {
  const recordsCount = records.length;
  const result = {};
  for (let i = 0; i < recordsCount; i += 1) {
    const { id, ...rest } = record;
    result[id] = computeRestFields(rest); // some mapping function
  }
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

由于代码非常简单明了，所以我只是取出 id，然后将它推送到一个对象上，这是一个键值对。令我惊讶的是，它真的起作用了。我对结果完全不知所措。我开始分析这两者之间的区别。

## 结果

每次使用 reduce 时，我都会创建一个新对象，也就是说，对于每条记录，我都会创建一个相同大小的新对象，并向该对象添加一个新值。它增加了每次迭代运行时必须存储在内存中的值的数量。所以，确切的罪魁祸首不仅仅是 reduce 函数，我在编写第一个解决方案时不得不责怪它。

后来我明白了罪魁祸首是(显然是我！😁)减少和扩散的结合。读者可能会有一个疑问，为什么累加器每次都得到传播？原因是当时我是 eslint 的忠实粉丝，它告诉我编辑参数是不好的。即使我现在还喜欢 eslint，但我现在更像是一个看起来需要的人。我已经知道 reduce/map/filter 都可以通过一个普通 for 循环来实现(我以前已经习惯了使用 conditonal 块)。然而，所有的东西都是为了特定的目的而存在的，并利用它来解决问题。

这就是为什么当我们经常使用某个东西时，我会建议学习关键词的语义。我故意提到这个词，因为我认为不值得去探究那些我们十年才使用一次的东西。希望您能从这篇文章中学到一些东西。

如果有任何错误的假设，请务必纠正我。
干杯