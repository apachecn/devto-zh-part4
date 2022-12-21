# object . from entries(es 2019)-object . entries(es 2017)的优雅反转

> 原文：<https://dev.to/shahzaibkhalid/object-fromentries-es2019-an-elegant-inverse-of-object-entries-es2017-o69>

嘿！👋

查看今天的开发技巧！👇

> 关注我的 Twitter [@shahzaibkhalid](https://twitter.com/shahzaibkhalid) 以获得更多开发技巧！✨

# 对象.条目🚀

对于对象中的每个键-值对，`Object.entries`给出一个数组，其中第一个元素是键，第二个元素是值。

`Object.entries`在与 for-of 结合使用时特别有用，因为它使您能够非常优雅地遍历对象中的所有键值对:

```
const object = { x: 42, y: 50 };
const entries = Object.entries(object);
// → [['x', 42], ['y', 50]]

for (const [key, value] of entries) {
  console.log(`The value of ${key} is ${value}.`);
}
// Logs:
// The value of x is 42.
// The value of y is 50. 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，到目前为止，还没有一种简单的方法可以从条目结果返回到一个等价的对象。🎉

# [T1】object . from entries🦄](#objectfromentries)

```
const object = { x: 42, y: 50 };
const entries = Object.entries(object);
// → [['x', 42], ['y', 50]]

const result = Object.fromEntries(entries);
// → { x: 42, y: 50 } 
```

Enter fullscreen mode Exit fullscreen mode

# 实际用法👀

一个常见的用例是转换对象。现在，您可以通过迭代它的条目，然后使用您可能已经熟悉的数组方法来实现这一点:

```
const object = { x: 42, y: 50, abc: 9001 };
const result = Object.fromEntries(
  Object.entries(object)
    .filter(([ key, value ]) => key.length === 1)
    .map(([ key, value ]) => [ key, value * 2 ])
);
// → { x: 84, y: 100 } 
```

Enter fullscreen mode Exit fullscreen mode

希望你今天学到了新东西。请在下面的评论中让我知道你对这个开发技巧的看法。👀

和平。✌️