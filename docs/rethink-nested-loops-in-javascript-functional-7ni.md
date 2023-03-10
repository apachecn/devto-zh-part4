# 重新思考 Javascript 函数中的嵌套循环

> 原文：<https://dev.to/5422m4n/rethink-nested-loops-in-javascript-functional-7ni>

我想从这个小问题陈述开始:

```
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

// c-ish for-i loop
for (let i = 0; i < animals.length; i++) {
    for (let j = i + 1; j < animals.length; j++) {
        const a1 = animals[i];
        const a2 = animals[j];

        console.log(`${a1} and ${a2} are friends`);
    }
}
/* expected output:

ant and bison are friends
ant and camel are friends
ant and duck are friends
ant and elephant are friends
bison and camel are friends
bison and duck are friends
bison and elephant are friends
camel and duck are friends
camel and elephant are friends
duck and elephant are friends

 */ 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，而且很可能没有任何问题。

但是如何在功能上做同样的事情呢？

让我们试一试:

```
animals.forEach((a1) => {
    animals.forEach((a2) => {
        console.log(`${a1} and ${a2} are friends`);
        // WRONG!
        // > ant and ant are friends
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

嗯，如你所见，有些事情并不像预期的那样。现在所有的动物都彼此结合，甚至是与自己结合。

好吧，接下来试着解决这个问题:

```
animals.forEach((a1, xi) => {
    animals.slice(xi + 1).forEach(a2 => {
        console.log(`${a1} and ${a2} are friends`);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

耶！它工作了。让我们看看这是为什么。

`slice`函数接受一个作为起始索引的参数，数组应该从这里开始切片。在这里，我们传递了`a1`的索引+ 1，这样我们就得到了`a1`后面的一个子数组。

好吧，作为奖励，让我们更进一步，使我们的代码功能可重用。

```
const combine = (list) => list.map(
    (x, xi) => list.slice(xi + 1).map((y) => [x, y])).reduce(
        (acc, tuple) => acc.concat(tuple), []);

console.log(combine(animals));
/* expected output:

[ [ 'ant', 'bison' ],
  [ 'ant', 'camel' ],
  [ 'ant', 'duck' ],
  [ 'ant', 'elephant' ],
  [ 'bison', 'camel' ],
  [ 'bison', 'duck' ],
  [ 'bison', 'elephant' ],
  [ 'camel', 'duck' ],
  [ 'camel', 'elephant' ],
  [ 'duck', 'elephant' ] ]

 */ 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到了一个名为`combine`的 lambda，它将产生一个元组数组，我们可以如下使用:

```
var allTheAnimals = combine(animals).map(
    ([a1, a2]) => `|${a1}| and |${a2}|`).join(' are friends\n');
console.log(`${allTheAnimals} are friends`);
/* expected output:

|ant| and |bison| are friends
|ant| and |camel| are friends
|ant| and |duck| are friends
|ant| and |elephant| are friends
|bison| and |camel| are friends
|bison| and |duck| are friends
|bison| and |elephant| are friends
|camel| and |duck| are friends
|camel| and |elephant| are friends
|duck| and |elephant| are friends

 */ 
```

Enter fullscreen mode Exit fullscreen mode

注意`.map(([a1, a2])`会把元组数组展开成左右各一个。

**现在请在评论中分享你的方法**！我很好奇其他的解决方案。

感谢阅读！
干杯斯文