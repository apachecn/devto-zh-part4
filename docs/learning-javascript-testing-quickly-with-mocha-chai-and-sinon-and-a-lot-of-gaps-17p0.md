# 用 Mocha、Chai 和 Sinon 快速学习 JavaScript 测试(还有很多空白)

> 原文：<https://dev.to/isalevine/learning-javascript-testing-quickly-with-mocha-chai-and-sinon-and-a-lot-of-gaps-17p0>

*封面图片鸣谢: [**猎人 x 猎人**漫画](https://en.wikipedia.org/wiki/Hunter_%C3%97_Hunter)作者富坚义博，由你真正迷上。< 3*

**2019 年 8 月 9 日更新:**这篇文章被称为我的新系列的第一部分，*初级 JavaScript 求职:技术和家庭的快速技巧*！希望你喜欢并找到一些有用的提示，请随时在评论中贡献更多！

[![anime gif of suited man sipping tea with a satisfied look on his face](img/84f1858d2b5ef29b7861a74ae8aeace9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N2xfwk_7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://pa1.narvii.com/6901/da1eb99c537353c1e4ba18d888a4c72cb69e5d97r1-504-389_hq.gif)

最近，我有机会完成了一项带回家的编码挑战，这项挑战要求我在解决方案中包含 JavaScript 测试。我将坦率地承认，我仍然在学习测试的细节，并且这场战斗的很大一部分是知道要测试什么。这些笔记并不打算作为一般测试的入门——相反，我想分享快速启动和运行所需的命令和语法，并编写一些单元测试。

## 那么我们在这里测试什么呢？

挑战包括构建一个类`FlavorRanker`，它接受一个文本文件进行解析并返回最受欢迎的口味排名。解析后的排名存储在属性`this.flavorObj`中，该属性被初始化为空，并在运行类函数`parseTextFile()`后被填充。这里有一个简化版本的快照:

```
// FlavorRanker.js

class FlavorRanker {
    constructor() {
        this.flavorObj = {};
    }

    parseTextFile() {    
    // fill in this.flavorObj with pairs like “grape”: { “points”: 5 }
    }
}
exports.FlavorRanker = FlavorRanker; 
```

有了这个类，我们可以马上测试一些东西:

创建了`FlavorRanker`的实例后，它的`this.flavorObj`属性是否存在？
在某些时候，`this.flavorObj`是空的吗——还是`parseTextFile()`已经成功地向它添加了名称-值对？`parseTextFile()`被调用了吗——它被调用了一次吗？

不是最健壮的测试，但它们将向我们介绍一些来自框架 Mocha、Chai 和 Sinon 的基本 JavaScript 测试语法！

## 等等，为什么我们要同时报道三件事？

简而言之:因为他们合作得太好了！简而言之，他们每个人都将为我们做些什么:

*   [Mocha](https://mochajs.org/)——一个 JavaScript 测试运行器和框架，提供了用于测试断言的`describe()` / `it()`语法。这将是您在`“scripts”: { “test”: “mocha” }`下的 package.json 文件中指定的内容。

*   [Chai](https://www.chaijs.com/) -一个为 JavaScript 测试断言增加额外可读性的库。用`expect().to.be`取代 Node.js 默认的`assert()`语法，以及许多可链接的选项。

*   Sinon -一个提供间谍功能的库，它可以检测何时调用它们，传递给它们什么参数，返回什么等等。(Sinon 提供的远不止这些，但我们将在本文中只讨论间谍。)

## 设置

要在项目中包含这些包，请使用以下命令:

这将在全球范围内安装 Mocha(不仅仅是在你当前的项目中)，并允许你在终端中访问`$ mocha`命令。(本指南不会涉及这一点。)

这将在本地安装 Chai。

这将在本地安装 Sinon。

您还需要创建一个`/test`目录，并在该目录中创建一个`test.js`文件:

```
test
|-- test.js 
```

最后，在 package.json 文件中，检查“scripts”部分，确保“test”设置为“mocha”:

```
// package.json

"scripts": {
  "test": "mocha"
}, 
```

让我们写一些测试！

### 导入

让我们在测试中加载一些特定的工具。我们将使用柴的`expect`、兴农的`spy`以及上面的`FlavorRanker`类:

```
// test.js 

const expect = require('chai').expect;
const spy = require('sinon').spy;
const FlavorRanker = require('../flavorRanker.js').FlavorRanker; 
```

### 使用 describe()来组织测试和创建上下文

Mocha 允许我们通过嵌套 describe()函数来编写测试。[这个 StackOverflow 讨论](https://stackoverflow.com/questions/19298118/what-is-the-role-of-describe-in-mocha)深入到了组织测试的时间/原因/方式，但是要点如下:

`describe(“String with test description”, function() { … } )`

**注:** [本文](https://remarkablemark.org/blog/2016/08/20/arrow-functions-in-mocha/)涵盖了为什么你**不**想在摩卡中使用箭头函数而不是函数(){}。

您可以将它们嵌套得尽可能深——只是要注意每个都建立了一个新的上下文，并且变量作用域在这里如预期的那样适用:

```
describe('Generic test name', function() {
    // variable flavorRanker does NOT exist in this context.

    describe('FlavorRanker class', function() {
        const flavorRanker = new FlavorRanker;

        describe('flavorRanker instance', function() {
            // variable flavorRanker DOES exist in this context.
        });
    });
}); 
```

### 使用它()来声明单个测试

在 describe()上下文中，每个 it()函数描述一个测试。语法是:

`it(“String with test description”, function() { … } )`

下面是两个测试，确保新创建的 FlavorRanker 实例具有 this.flavorObj 属性，并且是一个空对象:

```
describe('flavorRanker instance', function() {

            it('should have a flavorObj property that is an object', function() {
                // testable assertion
            });

            it('flavorObj should be empty', function() {
                // testable assertion
            }); 
```

### 柴:期待()

Chai 大放异彩，因为它让编写可读的测试变得如此简单。下面是 expect()的语法:

`expect(foo).to._____._____._____ …`

在空白处，您可以添加大量创建可测试断言的链式函数。下面是我们如何为上面的两个测试编写 expect()函数:

```
describe('flavorRanker instance', function() {

            it('should have a flavorObj property that is an object', function() {
                expect(flavorRanker.flavorObj).to.be.an('object');
            });

            it('flavorObj should be empty', function() {
                expect(flavorRanker.flavorObj).to.be.empty;
            }); 
```

测试将检查它们所说的内容:flavorRanker.flavorObj 是一个对象吗，它是空的吗？下面是运行`$ npm test` :
的终端输出

```
 Generic test name
    FlavorRanker class
      flavorRanker instance
        ✓ should have a flavorObj property that is an object
        ✓ flavorObj should be empty 
```

### 否则:间谍()

最后，我们可以使用 Sinon 的 spy()函数来指定一个变量来“监视”某些行为，比如函数被调用或返回值。要创建间谍:

`const spyName = spy(object, “functionName”)`

对于我们的测试，我们将为 flavorRanker 的 parseTextFile()方法创建一个 spy:

```
 describe('flavorRanker instance', function() {
            const parseTextFile = spy(flavorRanker, "parseTextFile");
        }); 
```

现在，我们可以使用 Chai 的语法编写测试来检查它是否被调用过一次:

```
describe('flavorRanker instance', function() {
    const parseTextFile = spy(flavorRanker, “parseTextFile");

    // spy detects that function has been called
    flavorRanker.parseTextFile();

    // checks that function was called once in this test’s context
    it('flavorRanker.parseTextFile() should be called once', function() {
        expect(parseTextFile.calledOnce).to.be.true;
    });

}); 
```

现在，当我们再次运行`$ npm test`时，我们的终端显示:

```
 Generic test name
    FlavorRanker class
      flavorRanker instance
        ✓ should have a flavorObj property that is an object
        ✓ flavorObj should be empty
        ✓ flavorRanker.parseTextFile() should be called once 
```

完美！

## 结论:这只是开始！

正如我在介绍中所说的，这篇文章远没有达到全面的程度——但是对于像我这样开始学习 JavaScript 测试有点害怕的人来说，有几个简单易用的工具可以帮助你开始！请在下面留下你的评论，为那些需要快速学习一些测试语法的人分享任何其他入门技巧！

### (更全面)指南和资源

[非常棒的摩卡入门指南，更有深度](https://blog.logrocket.com/a-quick-and-complete-guide-to-mocha-testing-d0e0ea09f09d/)

[帮助你编写第一个测试的优秀教程](https://codeburst.io/how-to-test-javascript-with-mocha-the-basics-80132324752e)

[摩卡、柴、西农语法的优秀小抄](https://gist.github.com/yoavniran/1e3b0162e1545055429e#mocha)

[摩卡文件](https://mochajs.org/)

[柴文书](https://www.chaijs.com/)

[否则文件](https://sinonjs.org/)