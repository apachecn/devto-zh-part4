# 为什么(我们)要为“console.log”腾出空间？

> 原文：<https://dev.to/easilybaffled/why-we-make-room-for-console-log-2j52>

我从事编程工作大约有十年了。前四年在大学假装学 Java，然后 2014 年开始学 JavaScript。那是在巴别塔和 ES6 的提议出台之前的一点时间。我已经看到了 JavaScript 曾经是什么，它将成为什么的前景，现在看到它超越了所有的期望。现代 JavaScript，从 ES6 开始的语言进化，已经不可思议了，但是它留下了一个小问题。`console.log`不符合现代 JS 的特点或风格。

### Ktchn Snk

Bellow 是一个使用大量现代 JS 功能的示例函数:

`const isEqual = require('lodash.isequal'); const moment = require('moment'); function formatCurrency(num) { return ( '$' + num.toString().replace(/(\d\d\d(?=[^.]))/g, function($1) { return $1 + ','; }) ); } const transactionData = { amount: 100, date: Date.now(), description: 'Shrimp! Heaven! Now!', type: '', from: 'Daniel', to: 'Mom' };` `const pickAndFormatTransaction = ( { amount, date, description } ) => ( { amount: Number( amount ) ? formatCurrency( amount ) : amount, date: moment( date ).format( 'DD/MM/YYY' ), description } ); console.log(pickAndFormatTransaction(transactionData));`

<figure> <figcaption>❓What do you think this function would have looked like [just a few years ago?](https://codesandbox.io/s/compassionate-fire-o6xnd?fontsize=14&view=preview)</figcaption> </figure>

功能简洁，深思熟虑。我的意思是，没有额外的语法。我不需要使用一个`return`，这也意味着我不需要声明一个`const resultingObject =...;`来返回。我避免了命名参数来提取一些值。我也不需要为`amount`创建一个`if/else`块。我不是想贬低像`return`这样的东西或者明确地命名价值观。他们有自己的位置。相反，我喜欢函数可以有多稀疏，但仍然可读。

为了减少杂乱，我设置了一个新的障碍。`pickAndFormatTransaction`太简洁了，以至于没有地方放`console.log`。如果出现问题，就需要额外的工作来调试它。

### 不顺心

`const isEqual = require('lodash.isequal'); const moment = require('moment'); const diff = require('jest-diff'); function formatCurrency(num) { return ( '$' + num.toString().replace(/(\d\d\d(?=[^.]))/g, function($1) { return $1 + ','; }) ); } function pickAndFormatTransaction({ amount, date, description }) { return { amount: Number(amount) ? formatCurrency(amount) : amount, date: moment(date).format('DD/MM/YYY'), description }; }` `const expected = { amount: '$0.00', date: '19/05/2019', description: 'Shrimp! Heaven! Now!' }; const actual = pickAndFormatTransaction({ amount: 0, date: 1558307309712, description: 'Shrimp! Heaven! Now!' }); // MDN.io/console/assert console.assert( isEqual(expected, actual), 'EXPAND FOR PRETTY DIFF: \n%s', // Using string substitutions MDN.io/console#Usage diff(expected, actual) );`

<figure> <figcaption>Run and take cover! This will fail!</figcaption> </figure>

即使你知道为什么`amount`是错误的，你如何着手将`console.log`添加到有问题的代码中？

`const isEqual = require('lodash.isequal'); const moment = require('moment'); const diff = require('jest-diff'); function formatCurrency(num) { return ( '$' + num.toString().replace(/(\d\d\d(?=[^.]))/g, function($1) { return $1 + ','; }) ); }` `const pickAndFormatTransaction = ( { amount, date, description } ) => ( { amount: Number( amount ) ? formatCurrency( amount ) : amount, date: moment( date ).format( 'DD/MM/YYY' ), description } ); const expected = { amount: '$0.00', date: '19/05/2019', description: 'Shrimp! Heaven! Now!' }; const actual = pickAndFormatTransaction({ amount: 0, date: 1558307309712, description: 'Shrimp! Heaven! Now!' }); // MDN.io/console/assert console.assert( isEqual(expected, actual), 'EXPAND FOR PRETTY DIFF: \n%s', // Using string substitutions MDN.io/console#Usage diff(expected, actual) );`

<figure> <figcaption>🛠 Break open the code and try adding `console.log` to see why the amount isn't coming out right.</figcaption> </figure>

我也试了一下。

```
const pickAndFormatTransaction = ( {
        amount,
        date,
        description
} ) => {
    const formattedAmount = Number( amount )
            ? formatCurrency( amount ) 
            : amount;
    console.log( Number( amount ), formattedAmount );
    return {
        amount: formattedAmount,
        date: moment( date ).format( 'DD/MM/YYY' ),
        description
} }; 
```

如果你的和我的有一点像的话，那就是一点也不漂亮。我认为“漂亮”是主观的，但在这里和我一起工作；这是值得的。

## `console.log -> undefined`

您可能已经注意到，当您运行上面的 RunKit 示例时，您会看到`console.log`输出，然后是`undefined`。那个`undefined`是`console.log(...)`的结果(如果你喜欢，可以查看 [*WHATWG* spec](https://console.spec.whatwg.org/#logger) )。这就是调试繁琐的原因。我们必须向后弯腰绕过这个`undefined`。我还没有看到我需要那个`undefined`的时候。如果`console.log`返回它正在记录的值而不是`undefined`，我们可以节省大量的工作。

## `console.tap`

我已经创建了`console.tap`作为现代 JS 已经丢失的日志功能。

`console.tap = v => { console.log( v ); return v; };`

<figure>

<figure> <figcaption>**Oo 啊**</figcaption> </figure>

<figcaption><sub>……等等，就这样？</sub></figcaption>

</figure>

<figure>

首先，是的，我正在将它添加到全局控制台对象中。那是我的选择，我是个疯子。第二，功能围绕简单。它取一个*值，记录*的那个*值，并返回*的那个*值。对于调用函数及其周围的上下文，什么都不会发生。这意味着调试没有额外的开销或设置。

<figcaption>*In reality you can pass additional arguments into `tap`. They'll be logged but not returned</figcaption>

</figure>

> **关于名称**的补充说明:
> 我从一个同名的帮助函数中获得了`console.tap`的名称。`tap`助手接受一个函数和一个值，用该值运行函数，忽略结果，并返回值。`console.tap`本质上是以`console.log`为功能传递的`tap`。您可以在 [Lodash](https://lodash.com/docs/4.17.11#tap) 和 [Ramda](https://ramdajs.com/docs/#tap) 中看到 tap 的示例。至于原名`tap`从何而来我不确定，但我真的很想知道。

### 使用龙头

我们稍后再回到`pickAndFormatTransaction`。取而代之的是小一点的东西。

`function parseNumbers(num) { return Number(num) || 0; } function removeEvens(num) { return num % 2; }` `const result = ['1', '2', 'zero' , 3, 4, 5] .map(parseNumbers) .filter(removeEvens) .reduce(( acc, v ) => Math.max(acc, v)); console.log(result);`

<figure> <figcaption>❓There is no bug here ( at least I don't think there is ) but where would you put the `console.log` if there was?</figcaption> </figure>

`map`、`reduce`和`filter`是 ES6 和现代 JS 前进方向的最初迹象。当你把它们连在一起时，你会遇到和以前一样的问题。没有地方放一个`console.log`。你必须撕开链条才能看到中间发生了什么。

```
const filtered = ['1', '2', 'zero' , 3, 4, 5]
    .map(parseNumbers)
    .filter(removeEvens);
console.log( filtered );

const res = filtered.reduce(( acc, v ) => Math.max(acc, v)); 
```

另一方面，它可以适合任何地方。

`function parseNumbers(num) { return Number(num) || 0; } function removeEvens(num) { return num % 2; }` `const result = console.tap(['1', '2', 'zero' , 3, 4, 5] .map(parseNumbers) .filter(removeEvens)) // <- this parens closes tap .reduce(( acc, v ) => Math.max(acc, v)); console.log(result);`

<figure> <figcaption>**左右移动**左右移动`)`左右移动`console.tap`左右移动</figcaption>

左右移动</figure>

`console.tap`也可以用在链的每一部分，因为每个函数产生一个数组。

下一个例子甚至没有使用任何现代功能，它仍然存在同样的问题。

`function getUserId( user ) { return user.id } const storage = { store: { user: '{“id”:1}' }, getItem( name ) { return this.store[name] } }` `var userID = getUserId( JSON.parse(storage.getItem( 'user' )) );`

<figure> <figcaption>❓ Anyone else excited for the [pipeline operator](%E2%80%9Chttps://github.com/tc39/proposal-pipeline-operator%E2%80%9D)?</figcaption> </figure>

如果当`JSON.parse`和`Unexpected token o in JSON at position 1`一起爆发时，你必须拔出`storage.getItem`才能意识到你不小心储存了`[object Object]`和`tap`:

`function getUserId( user ) { return user.id; } const storage = { store: { user: '{“id”:1}' }, getItem( name ) { return this.store[name]; } }` `const userID = getUserId( JSON.parse( console.tap( storage.getItem( 'user' ) ) ) );`

<figure> <figcaption>🛠 Move around `console.tap`. What do you get from `console.tap(JSON).parse`?</figcaption> </figure>

至于`pickAndFormatTransaction`、*那个*超水平发挥，不如给`tap`试试。

#### Ktchn Snk

`const isEqual = require('lodash.isequal'); const moment = require('moment'); function formatCurrency(num) { return ( '$' + num.toString().replace(/(\d\d\d(?=[^.]))/g, function($1) { return $1 + ','; }) ); } const transactionData = { amount: 100, date: Date.now(), description: 'Shrimp! Heaven! Now!', type: '', from: 'Daniel', to: 'Mom' };` `const pickAndFormatTransaction = ( { amount, date, description } ) => ( { amount: Number( amount ) ? formatCurrency( amount ) : amount, date: moment( date ).format( 'DD/MM/YYY' ), description } ); console.log(pickAndFormatTransaction(transactionData));`

<figure> <figcaption>🛠 throw in a few `console.tap`s and see what you can see.</figcaption> </figure>

除了`console.tap`之外不需要添加任何东西，你就可以记录整个返回的对象，或者任何涉及数量和时刻的东西。对于`description`，您仍然需要将简写扩展为`description: description`。

## 玩得开心！

我为`console.tap`创建了一个模块，处理一些额外的细节(比如提供一个 polyfill、 [ponyfill](https://github.com/sindresorhus/ponyfill) 和[babel macro](https://babeljs.io/blog/2017/09/11/zero-config-with-babel-macros))——查看文档了解更多)，但是函数声明非常小，你可以在需要的时候自己编写。

`console.tap = v => { console.log(v); return v; }; // or, for the bold console.tap = v => (console.log(v), v);`

[![NPM](img/d45d07d7d26da22955db3de77e68fc00.png)](https://nodei.co/npm/console.tap/)

特别感谢黛比·科布林、[斯蒂芬·史密斯、](https://dev.toStephen%20Smith)贾斯汀·泽林斯基、[萨姆·内夫](https://www.linkedin.com/in/samuelneff/)和朱利安·詹森对这篇文章的评论。