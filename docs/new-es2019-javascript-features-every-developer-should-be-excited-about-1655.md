# 每个开发人员都应该为 ES2019 JavaScript 的新特性感到兴奋

> 原文：<https://dev.to/bnevilleoneill/new-es2019-javascript-features-every-developer-should-be-excited-about-1655>

**作者:[gbolahan olagunju](https://blog.logrocket.com/author/iamgbols/)T3】✏️**

自早期以来，JavaScript 已经走过了漫长的道路，增加了许多新的内容和特性，旨在使这种语言更加用户友好，更加简洁。下面是一些我觉得很有趣的 JavaScript 新增内容。

其中一些功能已经在 Node、Chrome、Firefox 和 Safari 中可用，而其他功能仍处于提案阶段。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### 可选链接

使用`?.`操作符完成可选链接。它主要确保问号之前的值既不是未定义的也不是空的。这在评估深度嵌套对象的属性时非常有用。

在评估属性之前，需要确保`?.`操作符存在。

考虑下面的例子:

```
const users = [
  {
   name: "Olagunju Gbolahan",
   occupation: "Software Developer",
   sayName(){
    console.log(`my name is ${this.name}`);
   },
   address: { office: "New York" }
  },
  { name: "Olawaseyi Moses" },
  { name: "Tunde Ednut" }
]; 
```

Enter fullscreen mode Exit fullscreen mode

让我们考虑用户数组中的第二个用户:

```
const secondUser = users[1]; 
```

Enter fullscreen mode Exit fullscreen mode

我们可能想要得到这个用户的办公室地址。在可选的链接操作符出现之前，我们必须通过一个相对低效的过程来获取这些信息:

```
const theAddress = secondUser.address && secondUser.address.office;
console.log(theAddress); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有一个深度嵌套的对象，我们必须在每一层使用`&&`操作符来检查它的值是否存在。

但是对于可选的链接，我们只需做以下事情:

```
const theAddress = secondUser?.address?.office;
console.log(theAddress); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用对象方法的可选链接来在执行之前确认它们的存在:

```
const firstUser = users[0];
console.log(firstUser.sayName?.()); // my name is Olagunju Gbolahan 
```

Enter fullscreen mode Exit fullscreen mode

如果对象上不存在具有给定名称的方法，它将简单地返回`undefined`。

```
console.log(firstUser.sayOccupation?.()); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

因为可选的链接操作符还没有被添加到 JavaScript 规范中，所以它仍然处于[提议阶段](https://github.com/tc39/proposal-optional-chaining)。

你现在可以用[babel-plugin-proposal-optional-chaining](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)插件使用它。

### 可选捕捉绑定

当我们预先知道我们的错误是什么，并且我们不想要多余的未使用的变量时，这个特性就派上了用场。

考虑传统的尝试和捕捉块:

```
try {
  const parsedJsonData = JSON.parse(obj);
} catch (error) {
  //the variable error has to be declared weather used or unused
  console.log(obj);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是通过添加可选的 catch 绑定，我们不必提供未使用的变量——特别是当我们的 try 块有默认值时。

```
function getName () {
  let name = "Gbolahan Olagunju";
  try {
    name = obj.details.name
  } catch {}
  console.log(name);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 管道操作员

这是 Javascript 的新增内容之一，目前处于第一阶段。

它本质上有助于使对同一个参数的几个函数调用可读。

它通过将表达式的值作为参数传递给函数来实现这一点。考虑在没有管道操作符`|>`的情况下调用以下函数。

```
const capitalize = (input) =>  input[0].toUpperCase() + input.substring(1);
const removeSpaces = (input) => input.trim();
const repeat = (input) => `${input}, ${input}`; 
```

Enter fullscreen mode Exit fullscreen mode

```
const withoutpipe = repeat(capitalize(removeSpaces(' i am gbols ')));
console.log(withoutpipe); // I am gbols, I am gbols 
```

Enter fullscreen mode Exit fullscreen mode

但是有了流水线操作符，可读性可以大大提高:

```
const withpipe = ' i am gbols '
                |> removeSpaces
                |> capitalize
                |> repeat;
console.log(withpipe); // // I am gbols, I am gbols 
```

Enter fullscreen mode Exit fullscreen mode

### String.trimStart 和 String.trimEnd

这正式命名为 trimRight 和 trimLeft，但在 ES2019 中，这些名称被更改为别名 trimStart 和 trimEnd，以使用户更直观。

考虑下面的例子:

```
let massage = " Welcome to LogRocket ";
message.trimStart(); // "Welcome to LogRocket      "
message.trimEnd(); // "Welcome to LogRocket"; 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】object . from entries](#objectfromentries)

在讨论 Object.fromEntries 之前，先讨论 Object.entries 是很重要的。

ES2017 规范中添加了 Object.entries 方法，以提供一种将对象转换为其等效数组的方法，从而授予其访问所有数组方法进行处理的权限。

考虑以下对象:

```
const devs = {
  gbols: 5,
  andrew: 3,
  kelani: 10,
  dafe: 8,
};
const arrOfDevs = Object.entries(devs);
console.log(arrOfDevs);
//[
//  ["gbols", 5]
//  ["andrew", 3]
//  ["kelani", 10]
//  ["dafe", 8]
//] 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以在数组上使用`filter`方法来获取拥有 5 年以上经验的开发人员:

```
const expDevs = arrOfDevs.filter(([name, yrsOfExp]) => yrsOfExp > 5);
console.log(expDevs);
//[
//  ["kelani", 10]
//  ["dafe", 8]
//] 
```

Enter fullscreen mode Exit fullscreen mode

于是问题出现了:没有简单的方法将结果转换回对象。通常，我们会编写自己的代码将它转换回一个对象:

```
const expDevsObj = {};
for (let [name, yrsOfExp] of expDevs) {
expDevsObj[name] = yrsOfExp;
}
console.log(expDevsObj);
//{
 //dafe: 8
 //kelani: 10
//} 
```

Enter fullscreen mode Exit fullscreen mode

但是随着 Object.fromEntries 的引入，我们可以在一次滑动中做到这一点:

```
console.log(Object.fromEntries(expDevs));
//{
 //dafe: 8
 //kelani: 10
//} 
```

Enter fullscreen mode Exit fullscreen mode

### 平面和平面图

通常，由于 API 调用，我们需要处理深度嵌套的数组。在这种情况下，展平数组就显得尤为重要。

考虑下面的例子:

```
const developers = [
  {
    name: 'Gbolahan Olagunju',
    yrsOfExp: 6,
    stacks: ['Javascript', 'NodeJs', ['ReactJs', ['ExpressJs', 'PostgresSql']]]
  },
  {
    name: 'Daniel Show',
    yrsOfExp: 2,
    stacks: ['Ruby', 'Jest', ['Rails', ['JQuery', 'MySql']]]
  },
  {
    name: 'Edafe Emunotor',
    yrsOfExp: 9,
    stacks: ['PHP', 'Lumen', ['Angular', 'NgRx']]
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

```
const allStacks = developers.map(({stacks}) => stacks);
console.log(allStacks);
// [
// ['Javascript', 'NodeJs', ['ReactJs', ['ExpressJs', 'PostgresSql']]]
// ['Ruby', 'Jest', ['Rails', ['JQuery', 'MySql']]]
// ['PHP', 'Lumen', ['Angular', 'NgRx']]
// ] 
```

Enter fullscreen mode Exit fullscreen mode

`allstacks`变量包含深度嵌套的数组。要展平这个数组，我们可以使用 Array.prototype.flat。

下面是方法:

```
const flatSingle = allStacks.flat();
console.log(flatSingle);
//[
// "JavaScript",
//  "NodeJs",
// ['ReactJs', ['ExpressJs', 'PostgresSql']]]
// "Ruby",
// "Jest",
// ['Rails', ['JQuery', 'MySql']]]
// "PHP",
// "Lumen"
// ["Angular", "NgRx"]
//] 
```

Enter fullscreen mode Exit fullscreen mode

从上面我们可以推断出，数组已经扁平化了一级，这是 array.prototype.flat 的默认参数。

我们可以向 flat 方法传递一个参数，以确定我们想要展平的程度。

defaults 参数的值为 1。为了完全展平数组，我们可以传递一个无穷大的参数。`Infinity`参数完全展平数组，而不管数组的深度。

下面是方法:

```
const completelyFlat = allStacks.flat(Infinity);
console.log(completelyFlat);
//[
// "JavaScript",
// "NodeJs",
// "ReactJs",
// "ExpressJs",
// "PostgresSql",
// "Ruby",
// "Jest",
// "Rails",
// "JQuery",
// "MySql",
// "PHP",
// "Lumen",
// "Angular",
// "NgRx"
//] 
```

Enter fullscreen mode Exit fullscreen mode

### 平面图

FlatMap 是调用深度为 1 的 Map 方法和 flat 方法的组合。它通常非常有用，因为它以非常高效的方式做同样的事情。

下面是一个使用 map 和 flatMap 的简单例子:

```
let arr = ['my name is Gbols', '  ', 'and i am great developer']; 
console.log(arr.map(word => word.split('  ')));
//[
// ["my", "name", "is", "Gbols"],
// ["", ""],
// ["and", "i", "am", "great", "developer"]
//] 
```

Enter fullscreen mode Exit fullscreen mode

```
console.log(arr.flatMap(word => word.split('  ')));
//[ "my"
//  "name"
//  "is"
//  "Gbols"
//   ""
//   ""
//   "and"
//   "i"
//   "am"
//   "great"
//   "developer"
//] 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

在本文中，我们列举了 JavaScript 新增功能的诸多好处。这些增加通过减少冗长和增加可读性来增强开发人员的体验。

下面，看看我们没有提到的几个新特性:

[JSON . stringify](https://github.com/tc39/proposal-well-formed-stringify)

[排序稳定性](https://tc39.es/ecma262/#sec-array.prototype.sort)

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子【ES2019 JavaScript 新特性每个开发人员都应该感到兴奋首先出现在 [LogRocket 博客](https://blog.logrocket.com)上。