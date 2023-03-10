# 快速回顾 JavaScript 的 Array.prototype.sort()方法

> 原文：<https://dev.to/isalevine/a-quick-review-of-javascript-s-array-prototype-sort-method-7cm>

*封面图片鸣谢: [**猎人 x 猎人**漫画](https://en.wikipedia.org/wiki/Hunter_%C3%97_Hunter)作者富坚义博，由你真正迷上。< 3*

在面试中，了解你选择使用的语言的基础是很重要的。这听起来像是常识，但基础知识的差距会让你看起来很糟糕。在经历了一次**(咳咳)**不愉快的经历之后，包括稍微过快地浏览 JavaScript 的 array . protype . sort()方法上的 [MDN 文档，我想使用我们在本系列前面检索的同一个 JSON 对象来分解一个简单的实现。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

[![gif of hisoka holding out a hand of playing cards, some of them facing the wrong direction](img/cb26ff5bf57078fa2f4745062ee3fb7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2J0_f1_H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o8fk857mzi5bqvesp5o2.gif) 
*整理东西很重要！问问这家伙就知道了，他明明知道自己在做什么！*

# 默认 array . protype . sort()行为

默认情况下，在 JavaScript 数组上调用`.sort()`将尝试一些基本的从低到高的排序，基于字符串的字母顺序或数字的值。然而，默认行为将只比较字符串(仅首字母)或数字(遇到的第一个数字)的第一个元素**。**

以下是来自 MDN 文档的每个示例:

```
// sorting strings alphabetically, based on the first letter
var months = ['March', 'Jan', 'Feb', 'Dec'];
months.sort();
console.log(months);
// expected output: Array ["Dec", "Feb", "Jan", "March"]

// sorting integers by their first digit
var array1 = [1, 30, 4, 21, 100000];
array1.sort();
console.log(array1);
// expected output: Array [1, 100000, 21, 30, 4] 
```

可以理解的是，看到 100000 出现在 1 和 21 之间，你可能会措手不及。幸运的是，JavaScript 为我们提供了一种内置的方式来定制我们的排序行为，即在`.sort()`中使用`compareFunction`！

# 用 compareFunction 定义排序行为。排序(函数(a，b) { … })

我们可以通过添加一个名为`compareFunction`的匿名函数来改变默认的排序行为。我们将它添加到`.sort()`调用中，并传递参数`a`和`b`来表示数组的元素。

例如，我们可以比较`a`和`b`的整个值，并基于此进行排序，而不是只比较整数的第一个数字。

再次改编自 MDN 文档:

```
// sorting integers by their value
var array1 = [1, 30, 4, 21, 100000];

array1.sort(function(a, b) {   // this anonymous function is the compareFunction
    if (a < b) {    // now the entire values of a & b are compared
        return -1;
    };
    if (a > b) {
        return 1;
    };
    // a must be equal to b, no change to index
    return 0;
});

console.log(array1);
// expected output: Array [1, 4, 21, 30, 100000] 
```

太好了！现在，通过显式比较`a`和`b`的*整体*值，而不仅仅是它们的第一个数字，我们得到了我们期望的数字排序。

注意，匿名的`compareFunction`是特殊的，因为它在寻找负(-1)、零(0)或正(1)的返回:

```
Negative  =>  lower index
Zero      =>  no change
Positive  =>  higher index 
```

因此，我们可以创建任何我们希望最终返回负/零/正值的条件。

# 根据嵌套属性对 JSON 进行排序

**TL；DR:将你的嵌套属性赋给你可以在比较逻辑中轻松使用的变量！**

让我们看一个更复杂的例子:按照属性的字母顺序对来自[https://www.reddit.com/r/popular.json](https://www.reddit.com/r/popular.json)的 JSON 进行排序。

提醒一下，下面是我们将要排序的 JSON，特别是基于`data.children`数组中的属性:

[![sorted json from reddit's /r/popular board](img/c3ea7c962d2ea7110e2575c239c502fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OxvTkL1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zcaz3mscl2fwfu7iwb5.png)

我们将使用相同的[节点获取包](https://www.npmjs.com/package/node-fetch)，在本系列 :
的[上一篇文章中已经探讨过了](https://dev.to/isalevine/three-ways-to-retrieve-json-from-the-web-using-node-js-3c88)

```
const url = "https://www.reddit.com/r/popular.json"

// use the node-fetch package to retrieve JSON from the URL above
const fetch = require('node-fetch');

let settings = {
    method: "Get"
};

fetch(url, settings)
.then(res => res.json())
.then((json) => {
    sortJson(json);
}); 
```

在我们的`sortJson()`函数中，我们将使用以下步骤:

1.  我们选择一个属性来读取通过`data.children[i].data[property]`访问的`data.children`数组中的每个对象。
2.  我们定义了一个`compareFunction`，它使用我们定义的`”property”`来比较`a`和`b`的属性。我们将嵌套值分配给容易访问的变量，`property1`和`property2`。
3.  比较`property1`和`property2`。通常，这些比较返回负值(-1)、零(0)或正值(1)。原始数组将根据属性重新排序。

这是我们的函数，使用`”title”`属性:
按 upvotes 排序

```
// sort Array json.data.children alphabetically by "title"
function sortJson(json) {

    // you can easily change this to any property, such as “ups” or “author_fullname"
    let property = "title";  

    json.data.children.sort(function(a, b) {

        // create new variables to easily access nested data for sorting
        let propertyA = a.data[property];
        let propertyB = b.data[property];

        if (propertyA < propertyB) {    // checks for a “lower” alphabetical order
            return -1
        };
        if (propertyA > propertyB) {    // checks for a “higher” alphabetical order
            return 1
        };
        return 0    // if titles are equal

    });

// First 3 Objects in Array json.data.children BEFORE sort():
// [0].data.title: What do you NEVER f*** with?
// [1].data.title: [Game Thread] Florida vs. Miami (7:00PM ET)
// [2].data.title: 🎉 100.000.000 SUBSCRIBERS 🎉

// First 3 Objects in Array json.data.children AFTER sort():
// [0].data.title: 'Like you've been fired from your job': YouTubers have lost thousands of dollars after their channels were mistakenly demonetized for months
// [1].data.title: Disney+ episodes will release on a weekly basis, instead of the all at once “binge” model
// [2].data.title: Employee spits in food for Instagram likes 
```

注意，在我们的输出中，应用了正常的 JavaScript 字符串排序规则，比如撇号 **'** 出现在字母 **D** 之前。

# 结论

排序在任何语言中都是一个基本功能，所以在进行技术挑战之前一定要练习一下！此外，对于像 JavaScript 和 Ruby 这样的语言，一定要深入研究排序方法是如何实现的，因为可能会要求您创建一些定制的排序功能。

和往常一样，花点时间熟悉一下 MDN 文档也无妨，以防您需要它们作为快速参考:[MDN docs for array . protype . sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

欢迎在下面评论任何额外的排序技巧或窍门！

# 19/8/28 更新

[菲尔·纳什](https://dev.to/philnash)刚刚发布了一篇令人难以置信的关于 JavaScript 的`Array.prototype.sort()`中的陷阱和不一致的文章。具体来说，他涵盖了两个重要的主题，这两个主题是对本综述的补充:

*   如何处理空值(强制为字符串“null”或整数 0)
*   如何处理未定义的/具有未定义值的对象

**[点击这里查看他的文章！](https://dev.to/philnash/how-not-to-sort-an-array-in-javascript-l9o)T3】**