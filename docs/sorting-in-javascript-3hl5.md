# JavaScript 中的排序

> 原文：<https://dev.to/sarahob/sorting-in-javascript-3hl5>

JavaScript 数组有很多漂亮的特性，其中之一就是 sort 方法。

sort 方法将对数组中的项目进行排序，并返回数组。

默认情况下，排序顺序是通过将每个项目转换为字符串并比较它们的 unicode 值来确定的。

`const names = [‘Buffy’, ‘Xander’, ‘Angel’, ‘Willow’, ‘Giles’, ‘Anya’];
names.sort();
// result: [“Angel”, “Anya”, “Buffy”, “Giles”, “Willow”, “Xander”]`

这很酷，当我们的值是一个字符串时，看起来很简单。但是数值呢？

让我们举个例子，假设我们有一个随机数数组；就上下文而言，我们称之为用户年龄。我们有一个从最年轻到最老的用户年龄排序的要求。

`const ages = [16, 24, 61, 31, 17, 39, 27, 8, 12, 82, 48, 42, 26, 46, 76, 84, 89, 46, 62, 28];
ages.sort(); // no custom function provided
// result: [12, 16, 17, 24, 26, 27, 28, 31, 39, 42, 46, 46, 48, 61, 62, 76, 8, 82, 84, 89]`

嗯，这个看起来还可以，但是等等，我很确定数字上 76 后面不是 8！

如果没有自定义函数，每一项都被转换成一个字符串，并根据它们的 Unicode 字符进行排序，这在这里是行不通的，因为我们比较的是数值而不是字符串。

不要担心，虽然我们可以提供一个自定义的比较函数来确定我们的排序顺序。

compare 函数有两个参数，通常这些参数被称为 a 和 b，但是你可以随意命名它们。

让我们创建排序函数:

`const customSort = ages.sort((a, b) => {
if(a > b){
return 1;
}else{
return -1;
}
});
// result: [8, 12, 16, 17, 24, 26, 27, 28, 31, 39, 42, 46, 46, 48, 61, 62, 76, 82, 84, 89]`

看起来好多了，但是我们到底在这里做什么？sort 函数将遍历我们的数组，一次获取两个参数，比较它们，然后移动到下两个参数，直到排序完成。

在我们的函数中，我们说，如果 a 大于 b，那么返回 1，这将把 a 排序到比 b 高的索引，否则返回-1，这将把 a 排序到比 b 低的索引。

酷！对对象数组排序呢？别担心，我们可以对对象进行排序，没问题，我们首先需要确定我们要排序的对象，让我们看看这个对象数组:

`const objects = [{name: ‘Buffy’, year: 1981}, {name: ‘Angel’, year: 1727}, {name: ‘Anya’, year: 860}, {name: ‘Spike’, year: 1850}];`

假设我们想按出生年份排序，我们想从最小到最大排序。这里我们只需要在自定义函数中指定要比较的属性。

`const customSort = objects.sort((a, b) => {
if(a.year > b.year){
return -1;
}else{
return 1;
}
});`

因为我们希望从最年轻的到最年长的，所以这次我们颠倒了逻辑，如果 a 年大于 b 年，返回-1，如果 a 年小于 b 年，返回 1。

就是这样，这就是 JavaScript 排序函数！感谢阅读。希望这个解释对你的 JavaScript 之旅有所帮助。如果有，也许给它一个掌声或留下评论:)

本帖由我的 medium 账号迁移而来:[https://medium . com/@ sarbot/sorting-in-JavaScript-a9c 04 f 865267](https://medium.com/@sarbot/sorting-in-javascript-a9c04f865267)