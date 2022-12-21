# 编写更好 JavaScript 的实用方法

> 原文：<https://dev.to/taillogs/practical-ways-to-write-better-javascript-26d4>

我没有看到足够多的人谈论提高 JavaScript 的实用方法。下面是我用来写更好的 JS 的一些顶级方法。

# 使用打字稿

提高 JS 的第一件事就是不写 JS。对于外行来说， [TypeScript (TS)](https://en.wikipedia.org/wiki/Microsoft_TypeScript) 是 JS 的“编译”超集(任何在 JS 中运行的东西都在 TS 中运行)。TS 在普通 JS 体验的基础上增加了一个全面的可选输入系统。在很长一段时间里，整个生态系统对 TS 的支持不够一致，以至于我不愿意推荐它。谢天谢地，那些日子已经过去很久了，大多数框架都支持开箱即用的 TS。既然我们都知道 TS *是什么*，那就让我们来谈谈为什么你会想要使用它。

TypeScript 强制执行“类型安全”。

类型安全描述了一个过程，在这个过程中，编译器验证在一段代码中所有类型都以“合法”的方式使用。换句话说，如果你创建一个接受数字的函数`foo`:

```
function foo(someNum: number): number {
  return someNum + 5;
} 
```

那个`foo`函数只能用一个数字来调用:

*好的*

```
console.log(foo(2)); // prints "7" 
```

*没好事*

```
console.log(foo("two")); // invalid TS code 
```

除了向代码中添加类型的开销之外，类型安全强制没有任何负面影响。另一方面，好处太大了，不容忽视。类型安全提供了针对常见错误/bug 的额外保护，这对于像 JS 这样无法无天的语言来说是一种福气。

[![](img/0ed23cea8e989e05e766c4aa3d194400.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQTTKn3K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ceii6tkxljkzbxi0jpd7.jpg) 
*主演希亚·勒博夫*

类型脚本类型，使得重构更大的应用程序成为可能。

重构大型 JS 应用程序可能是一场真正的噩梦。重构 JS 的大部分痛苦是由于它没有强制函数签名。这意味着，JS 函数永远不会真正被“误用”。例如，如果我有一个函数`myAPI`被 1000 个不同的服务使用:

```
function myAPI(someNum, someString) {
  if (someNum > 0) {
    leakCredentials();
  } else {
    console.log(someString);
  }
} 
```

我稍微修改了一下调用签名:

```
function myAPI(someString, someNum) {
  if (someNum > 0) {
    leakCredentials();
  } else {
    console.log(someString);
  }
} 
```

我必须 100%确定，每个使用这个函数的地方(1000 个地方)，我都正确地更新了用法。如果我错过了 1 次，我的身份就会泄露。这是 TS 的相同场景:

*之前* 

```
function myAPITS(someNum: number, someString: string) { ... } 
```

后*后* 

```
function myAPITS(someString: string, someNum: number) { ... } 
```

如您所见，`myAPITS`函数经历了与 JavaScript 函数相同的变化。但是这段代码没有产生有效的 JavaScript，而是产生了无效的 TypeScript，因为它使用的 1000 个地方现在都提供了错误的类型。由于我们之前讨论的“类型安全”，这 1000 个案例将阻止编译，并且您的凭据不会泄露(这总是好的)。

**TypeScript 让团队架构交流更容易。**

当 TS 设置正确时，如果不先定义接口和类，将很难编写代码。这也提供了一种分享简洁的、交流性的架构提案的方式。在 TS 之前，这个问题的其他解决方案已经存在，但是没有一个是在不增加额外工作的情况下自然解决的。例如，如果我想为我的后端提出一个新的`Request`类型，我可以使用 TS 将以下内容发送给一个团队成员。

```
interface BasicRequest {
  body: Buffer;
  headers: { [header: string]: string | string[] | undefined; };
  secret: Shhh;
} 
```

我已经不得不写代码，但现在我可以分享我的增量进展并获得反馈，而无需投入更多时间。不知道 TS 是不是天生就没有 JS 那么“bug”。我坚信强迫开发人员首先定义接口和 API 会产生更好的代码。

总的来说，TS 已经发展成为一个成熟的、比普通 JS 更可预测的替代品。当然，仍然需要熟悉普通的 JS，但是我最近开始的大多数新项目从一开始就是 ts。

# 使用现代功能

JavaScript 是世界上最流行的编程语言之一。你可能会认为一种被数亿人使用的 20 多年前的语言现在已经基本上被“理解”了，但事实恰恰相反。最近，对 JS 进行了许多更改和添加(是的，我知道，技术上是 ECMAScript)，从根本上改变了开发人员的体验。作为一个在过去两年才开始写 JS 的人，我的优势是没有偏见和期望。这导致了在使用语言的哪些特征和避免使用语言的哪些特征方面，出现了更加务实的、非宗教性的选择。

**`async`和`await`**

很长一段时间，异步的、事件驱动的回调是 JS 开发不可避免的一部分:

*传统回调*

```
makeHttpRequest('google.com', function (err, result) {
  if (err) {
    console.log('Oh boy, an error');
  } else {
    console.log(result);
  }
}); 
```

我不打算花时间解释为什么上面有问题([但是我在](http://cdevn.com/parallel-computing-simplified-starring-gordon-ramsay)之前有)。为了解决回调问题，JS 中加入了一个新概念“承诺”。承诺允许您编写异步逻辑，同时避免以前困扰基于回调的代码的嵌套问题。

*承诺*

```
makeHttpRequest('google.com').then(function (result) {
  console.log(result);
}).catch(function (err) {
  console.log('Oh boy, an error');
}); 
```

承诺相对于回调的最大优势是可读性和可链接性。

虽然承诺很好，但它们仍有不足之处。说到底，写承诺还是没觉得“土”。为了补救这一点，ECMAScript 委员会决定增加一种利用承诺的新方法，`async`和`await`:

*`async`和`await`*

```
try {
  const result = await makeHttpRequest('google.com');
  console.log(result);
} catch (err) {
  console.log('Oh boy, an error');
} 
```

一个警告是，任何你必须声明的东西:

*上一示例中 makeHttpRequest 的必需定义*

```
async function makeHttpRequest(url) {
  // ...
} 
```

也可以直接`await`一个承诺，因为`async`函数实际上只是一个花哨的承诺包装器。这也意味着，`async/await`代码和承诺代码在功能上是等价的。所以放心用`async/await`不用内疚。

**`let`和`const`**

对于 JS 的大部分存在，只有一个变量范围限定符`var`。`var`在如何处理范围方面有一些非常独特/有趣的规则。在 JS 的整个生命周期中，`var`的作用域行为是不一致的和令人困惑的，并且导致了意想不到的行为和错误。但是从 ES6 开始，除了`var`、`const`和`let`还有一个选择。几乎不再需要使用`var`，所以不要使用。任何使用`var`的逻辑都可以被转换成等价的基于`const`和`let`的代码。

至于什么时候用`const` vs `let`，我总是先声明一切`const`。`const`更具限制性和“不可变性”,这通常会产生更好的代码。没有太多的“真实场景”需要使用`let`，我会说 1/20 的变量我用`let`声明。剩下的都是`const`。

> 我说`const`是“不可变的”,因为它的工作方式与 C/C++中的`const`不同。对于 JavaScript 运行时来说，`const`意味着对那个`const`变量的引用永远不会改变。这并不意味着存储在该引用中的内容永远不会改变。对于原始类型(数字、布尔等)，`const`确实转化为不变性(因为它是单个内存地址)。但是对于所有的对象(类、数组、字典)，`const`并不能保证不变性。

**箭头`=>`功能**

箭头函数是在 JS 中声明[匿名函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)的简洁方法。匿名函数，描述没有明确命名的函数。通常，匿名函数作为回调或事件挂钩传递。

*香草匿名功能*

```
someMethod(1, function () { // has no name
  console.log('called');
}); 
```

在很大程度上，这种风格没有任何“错误”。普通匿名函数在作用域方面表现得“有趣”，这可能/已经导致了许多意想不到的错误。多亏了箭头函数，我们再也不用担心这个问题了。下面是用箭头函数实现的相同代码:

*匿名箭头功能*

```
someMethod(1, () => { // has no name
  console.log('called');
}); 
```

除了更简洁之外，arrow 函数还有更实际的作用域行为。箭头函数从定义它们的作用域继承了`this`。

在某些情况下，箭头函数甚至可以更简洁:

```
const added = [0, 1, 2, 3, 4].map((item) => item + 1);
console.log(added) // prints "[1, 2, 3, 4, 5]" 
```

驻留在一行上的箭头函数包括一个隐式的`return`语句。单行箭头功能不需要括号或分号。

我想说清楚。这不是一种`var`情况，普通匿名函数(特别是类方法)仍然有有效的用例。也就是说，我发现如果你总是默认使用一个箭头函数，与默认使用普通的匿名函数相比，你最终要做的调试工作要少得多。

[和往常一样，Mozilla 文档是最好的资源](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

**传播算子`...`**

提取一个对象的键/值对，并将它们作为另一个对象的子对象添加，这是一个非常常见的场景。历史上，有几种方法可以实现这一点，但是所有这些方法都很笨拙:

```
const obj1 = { dog: 'woof' };
const obj2 = { cat: 'meow' };
const merged = Object.assign({}, obj1, obj2);
console.log(merged) // prints { dog: 'woof', cat: 'meow' } 
```

这种模式非常普遍，所以上面的方法很快就变得乏味了。多亏了“扩展运算符”，再也不需要使用它:

```
const obj1 = { dog: 'woof' };
const obj2 = { cat: 'meow' };
console.log({ ...obj1, ...obj2 }); // prints { dog: 'woof', cat: 'meow' } 
```

最棒的是，它还可以无缝地与数组一起工作:

```
const arr1 = [1, 2];
const arr2 = [3, 4];
console.log([ ...arr1, ...arr2 ]); // prints [1, 2, 3, 4] 
```

这可能不是最重要的，最近的 JS 功能，但它是我的最爱之一。

**模板文字(模板字符串)**

字符串是最常见的编程结构之一。这就是为什么在许多语言中仍然很少支持原生声明字符串的原因。在很长一段时间里，JS 都在“蹩脚弦”一家。但是模板文字的加入使 JS 自成一类。原生模板文字，并方便地解决了编写字符串、添加动态内容和编写桥接多行的字符串这两个最大的问题:

```
const name = 'Ryland';
const helloString =
`Hello ${name}`; 
```

我认为代码本身就说明了问题。多么惊人的实现。

**物体破坏**

对象析构是一种从数据集合(对象、数组等)中提取值的方法，无需迭代数据或显式访问其键:

*老路子*

```
function animalParty(dogSound, catSound) {}

const myDict = {
  dog: 'woof',
  cat: 'meow',
};

animalParty(myDict.dog, myDict.cat); 
```

*解构*

```
function animalParty(dogSound, catSound) {}

const myDict = {
  dog: 'woof',
  cat: 'meow',
};

const { dog, cat } = myDict;
animalParty(dog, cat); 
```

但是等等，还有更多。您也可以在函数的签名中定义析构:

*毁灭 2*T2】

```
function animalParty({ dog, cat }) {}

const myDict = {
  dog: 'woof',
  cat: 'meow',
};

animalParty(myDict); 
```

它也适用于数组:

*解构 3*

```
[a, b] = [10, 20];

console.log(a); // prints 10 
```

你应该利用许多其他的现代功能。以下是一些让我印象深刻的例子:

*   [剩余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)
*   [导入超过要求](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
*   [数组元素查找](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)

# 总是假设你的系统是分布式的

编写并行化应用程序时，您的目标是优化您一次所做的工作量。如果您有 4 个可用内核，而您的代码只能利用一个内核，那么您 75%的潜力都被浪费了。这意味着，阻塞、同步操作是并行计算的终极敌人。但是考虑到 JS 是单线程语言，东西不在多核上运行。那么这有什么意义呢？

JS 是单线程的，但不是单文件的(像学校里的线一样)。尽管它不是并行的，但它仍然是并发的。发送一个 HTTP 请求可能需要几秒钟甚至几分钟，如果 JS 在请求返回响应之前停止执行代码，那么这种语言将无法使用。

JavaScript 用一个[事件循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)解决了这个问题。事件循环遍历已注册的事件，并根据内部调度/优先级逻辑执行它们。这就是能够“同时”发送 1000 个 HTTP 请求或“同时”从磁盘读取多个文件的原因。这里有一个问题，只有当您使用正确的特性时，JavaScript 才能使用这个功能。最简单的例子就是 for 循环:

```
let sum = 0;
const myArray = [1, 2, 3, 4, 5, ... 99, 100];
for (let i = 0; i < myArray.length; i += 1) {
  sum += myArray[i];
} 
```

普通 for 循环是编程中并行性最低的结构之一。在我的上一份工作中，我带领一个团队花了几个月的时间试图将传统的`R` lang for 循环转换成自动并行代码。这基本上是一个不可能的问题，只有等待深度学习的改进才能解决。并行化 for 循环的困难来自一些有问题的模式。顺序 for 循环非常罕见，但仅凭它们不可能保证 for 循环的可分性:

```
let runningTotal = 0;
for (let i = 0; i < myArray.length; i += 1) {
  if (i === 50 && runningTotal > 50) {
    runningTotal = 0;
  }
  runningTotal += Math.random() + runningTotal;
} 
```

这段代码只有在一次又一次地按顺序执行时，才会产生预期的结果。如果您试图一次执行多个迭代，处理器可能会基于不准确的值进行错误的分支，从而使结果无效。如果这是 C 代码，我们会有一个不同的对话，因为用法是不同的，编译器可以用循环做很多技巧。在 JavaScript 中，传统的 for 循环应该只在绝对必要的情况下使用。否则，使用以下结构:

*地图*T2】

```
// in decreasing relevancy :0
const urls = ['google.com', 'yahoo.com', 'aol.com', 'netscape.com'];
const resultingPromises = urls.map((url) => makHttpRequest(url));
const results = await Promise.all(resultingPromises); 
```

*带索引的地图*

```
// in decreasing relevancy :0
const urls = ['google.com', 'yahoo.com', 'aol.com', 'netscape.com'];
const resultingPromises = urls.map((url, index) => makHttpRequest(url, index));
const results = await Promise.all(resultingPromises); 
```

*for-each*T2】

```
const urls = ['google.com', 'yahoo.com', 'aol.com', 'netscape.com'];
// note this is non blocking
urls.forEach(async (url) => {
  try {
    await makHttpRequest(url);
  } catch (err) {
    console.log(`${err} bad practice`);
  }
}); 
```

我将解释为什么这些是对传统 for 循环的改进。不是按顺序执行每个“迭代”，而是像`map`这样的构造获取所有的元素，并将它们作为单独的事件提交给用户定义的 map 函数。这直接传达给运行时，各个“迭代”彼此之间没有联系或依赖，允许它们并发运行。在很多情况下，for 循环与`map`或`forEach`相比性能一样好(甚至更好)。我仍然认为现在损失几个周期是值得的，因为使用了定义良好的 API。这样，未来对数据访问模式实现的任何改进都将有益于您的代码。for 循环过于普通，无法对相同的模式进行有意义的优化。

*在`map`和`forEach`之外还有其他有效的异步选项，比如`for-await-of`。*

# Lint 你的代码并强制一种风格

没有一致风格(外观和感觉)的代码难以阅读和理解。因此，用任何语言编写高端代码的一个关键方面就是要有一个一致和合理的风格。由于 JS 生态系统的广度，对于棉绒和风格细节有很多选择。我怎么强调都不为过的是，你使用棉绒和强化一种风格(任何一种)比你具体选择哪种棉绒/风格要重要得多。在一天结束的时候，没有人会像我一样写代码，所以为此进行优化是一个不现实的目标。

我看到很多人问他们应该用 [eslint](https://github.com/eslint/eslint) 还是[beautiful](https://github.com/prettier/prettier)。对我来说，它们服务于非常不同的目的，因此应该结合使用。Eslint 是一个传统的“linter”，大多数时候，它会识别代码中与风格关系不大的问题，而与正确性关系更大。例如，我使用 eslint 和 [AirBNB](https://github.com/airbnb/javascript) 规则。在这种配置下，下面的代码会强制 linter 失败:

```
var fooVar = 3; // airbnb rules forebid "var" 
```

eslint 如何为您的开发周期增加价值应该是显而易见的。从本质上来说，它确保您遵循关于什么是“好”和什么不是“好”实践的规则。由于这一点，棉绒天生固执己见。和所有的观点一样，不要全信，因为短绒可能是错的。

漂亮是一个代码格式化程序。它不太关心“正确性”，更担心统一性和一致性。Prettier 不会抱怨使用了`var`，但它会自动对齐代码中的所有括号。在我个人的开发过程中，我总是把运行得更漂亮作为将代码推送到 Git 之前的最后一步。在许多情况下，让 Prettier 在每次提交回购时自动运行是有意义的。这确保了所有进入源代码控制的代码具有一致的风格和结构。

# 测试你的代码

编写测试，是一种间接但非常有效的方法来改进你编写的 JS 代码。我建议熟悉各种测试工具。您的测试需求会有所不同，没有一个工具可以处理所有的事情。JS 生态系统中有大量成熟的测试工具，所以选择工具主要取决于个人喜好。一如既往的为自己着想。

**试驾者——艾娃**

运行库上的 avajs

测试驱动是简单的框架，在很高的层次上给出结构和效用。它们通常与其他特定的测试工具结合使用，这些工具根据您的测试需求而有所不同。

艾娃是表达和简洁的恰当平衡。艾娃平行而独立的建筑是我最喜欢的地方。运行更快的测试可以节省开发人员的时间和公司的资金。艾娃自诩拥有大量优秀的特性，比如内置断言，同时设法保持最小化。

替代品:Jest、摩卡、茉莉

**间谍和树桩——西农**

[否则在 Github](https://github.com/sinonjs/sinon) 上

间谍为我们提供“函数分析”,比如一个函数被调用了多少次，它们是由什么调用的，以及其他有见地的数据。

Sinon 是一个做了很多事情的库，但只有几件做得超级好。具体来说，西农擅长间谍和树桩。特性集很丰富，但是语法很简洁。这对于存根尤其重要，考虑到它们的部分存在是为了节省空间。

备选方案:testdouble

**模仿-诺克**

[Github 上的 Nock](https://github.com/nock/nock?source=post_page---------------------------)

*HTTP mocking 是伪造 HTTP 请求过程的某个部分的过程，所以测试人员可以注入自定义逻辑来模拟服务器行为。*

Http 嘲讽可能是一种真正的痛苦，nock 使它不那么痛苦。Nock 直接覆盖 nodejs 的`request`内置，并拦截传出的 http 请求。这反过来让你完全控制反应。

替代方案:我真的不知道任何替代方案

**网络自动化- Selenium**

[Github 上的硒](https://github.com/SeleniumHQ/selenium)

对于推荐硒，我是百感交集。因为它是 web 自动化最流行的选择，它有一个庞大的社区和在线资源集。不幸的是，学习曲线非常陡峭，它依赖于大量外部库的实际使用。也就是说，这是唯一真正免费的选择，所以除非你在做一些企业级的 web 自动化，否则 Selenium 将会完成这项工作。

# 另外两个随机 JS 的东西

*   你应该很少使用`null`，可怜的`null`
*   JavaScript 中的数字很烂，总是使用基数参数和`parseInt`

# 结论

自己画。*