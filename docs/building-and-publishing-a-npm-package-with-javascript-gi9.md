# 使用 JavaScript 构建和发布 NPM 包

> 原文：<https://dev.to/flippedcoding/building-and-publishing-a-npm-package-with-javascript-gi9>

如果您使用 JavaScript 进行过任何开发，那么您很可能使用过 npm 包。有很多优秀的软件包，比如 lodash 或 moment，各地的开发人员都很熟悉。一些公司甚至为他们的企业应用程序使用私有 npm 包。你可以找到并使用你能想到的任何东西的包。但是你知道怎么做吗？

你很有可能反复遇到某个问题，却没有在网上找到解决方法。制作一个 npm 包可能是解决这个问题的一种方法，并与从 2008 年开始在论坛上寻找答案的其他开发人员分享修复方法。只要你懂一点 JavaScript，你就可以构建和发布你自己的包！

例如，我们将构建一个包，它接受一个数组，并根据数组的长度进行随机操作。完成本演练后，您将了解创建和发布 npm 包的所有步骤。

## 创建库的步骤

首先，您可以像平常一样从代码开始。首先练习为您的包编写测试是一个好主意，这样您就可以确定它们像应该的那样工作。

### 测试

我们将从编写几个测试开始。这将是一个常规的 JavaScript 文件，所以你可以使用任何你喜欢的测试库。我会和贾斯敏一起写一些测试。在您计划与其他开发人员共享的真实软件包上，您肯定应该做比这更多的测试。

```
describe("takes an array and adds stuff sometimes", () => {
    it ("should return an array of the same length as the input", () => {
        let arr = [5, 68, 99, 52, 591];
        let newArr = arrayRandomizer(arr);
        expect(newArr).toBe(arr.length)
    });

    it ("should add a string to some of the values in the array", () => {
        let arr = ["test1", "test54", "test87"];
        let newArr = arrayRandomizer(arr);

        expect(newArr).not.toBe(arr);
    });
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 实现

现在您已经准备好了测试，为您的代码编写实现来通过它们。

```
function randomizeArray(arr) {    
    arr.forEach((part, index) => {
        this[index] = this[index] + "guess" + index;
    }, arr);

    return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 清理干净

在这一点上你已经做得差不多了。为了准备发布，您需要做一些清理工作。首先，如果没有 package.json 文件，那么创建一个。这将保存您的包对其他包的依赖关系以及您的包的元数据。没有 package.json 文件，您将无法将包发布到 npm。

```
{
    "name": "array_randomizer",
    "version": "1.0.0",
    "main": "array_randomizer.js"
    "author": "your name"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 发布

剩下的就是出版本身了。这样做就像安装 npm-publish 包一样简单。一旦你确定你的包可以被其他人使用，使用发布的包。它为您处理幕后的事情，让您做其他事情(比如确保它实际发布)。你现在可以庆祝一下了。

您的包已经发布，您可以开始告诉人们了！做一些定期维护，以确保您的软件包没有任何漏洞。更新包的方法与发布包的方法相同。您只需运行 npm 发布脚本，文件就会得到更新。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)