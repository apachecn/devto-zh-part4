# TypeScript 简介

> 原文：<https://dev.to/gautemeekolsen/intro-to-typescript-5dh1>

这就是我希望被介绍给 TypeScript 的方式；简短明了。因此，如果您已经了解 JavaScript，那么这篇文章就是为您准备的。

## 什么是打字稿

如果你已经开发了很多 JavaScript，你可能已经体验过使用起来有一点麻烦。在大型代码库中，你将不得不花大量时间阅读代码，理解它做什么以及你的新代码如何与现有代码交互。此外，在实际运行代码之前，不会出现错误。

因此，TypeScript 应运而生，口号是“可伸缩的 JavaScript”。TypeScript 代码将被编译成 JavaScript，然后像您习惯的那样使用。TypeScript 具有以下优点:

*   即时型错误检测
*   更多自我记录的代码
*   更好的 IDE 体验
*   提高代码的可读性和易理解性

TypeScript 实际上只是带有类型的 JavaScript。因此，如果您将文件结尾从。js 到。ts，应该还是有效代码(any 会用很多的类型)。所以不要害怕 TypeScript，只要开始编写普通的 JavaScript 代码，并在你觉得自然的地方声明类型。

我最喜欢的另一个好处。在 JavaScript 中，有一个功能缺口，ES6 到 ES9 中的新功能仍然不能在每个浏览器或节点服务器中使用。但是在 TypeScript 中，您可以编写具有最新特性的代码，例如类和箭头函数，并且您可以将 ES3 作为目标，代码将被编译成具有原型和普通函数的 JavaScript。(这不完全正确；如果您使用诸如 Set 和 includes()之类的特性，您将需要一个更新的 JavaScript 版本)

## 设置

下载并打开一个 IDE。我推荐 Visual Studio 代码，但是其他语言也支持 TypeScript，比如 Sublime 和 Atom。

通过 npm 在计算机上安装 typescript。下载节点，如果您没有安装 npm。在终端中运行以下命令。

```
npm install -g typescript 
```

现在可以编译了。用
手动 ts 文件

```
tsc filename.ts 
```

你创造了。您想要使用的 js 文件。

但是最好的方法是创建一个项目文件夹并运行 init 命令，就像这样

```
tsc --init 
```

设置您的配置文件。看一看它，你就会对它的功能有所了解。这是您可以更改 ECMAScript 目标版本和放置编译的 JavaScript 文件的地方。

现在用文件结尾创建您的文件。分时（同 timesharing）

保存时自动编译这些文件:

```
tsc -w 
```

如果您使用 webpack 之类的代码捆绑器，设置可能会稍有不同。

## 代码

TypeScript 中的类型批注是记录函数或变量的预期约定的方法。这是通过:后跟类型来实现的。

```
let text: string = 'hello'; 
```

如果您将一个数字赋给变量 text，那将会产生一个错误，因为只有 strings 才是有效的文本类型。

让我们浏览所有可用的类型。

```
//boolean
let bool: boolean = true;

//number
let num: number = 123;

//string
let text: text = 'hello';

//Array 
//(two options)
let arr1: string[] = [];
let arr2: Array<string> = [];

//tuple
let tuple: [number, string, boolean] = [12, 'yes', false];

//enum
enum Color {Red, Blue}
let color: Color = Color.Blue;

//any
let anything: any = 'whatever';

//void
function do(): void{
  console.log('no returns');
}

//null
let nothing1: null = null;

//undefined
let nothing2: undefined = undefined;

//never 
//(types that never occurs. removing the while would give error)
const neverMethod = (): never => {
  while(true);
  console.log('never reached');
}

//object
let obj: object = {attr1: true, attr2: false};

//function 
//(functions can be passed and returned)
function func(method: Function): Function{
  method();
  return method;
}

//interface
interface Options {
  size: number;
  readonly autodelete: boolean;
}
const opt: Options = {
  size: 1,
  autodelete: true
} 
```

请注意，在 TypeScript 中，类型是在分配变量时推断出来的。这是因为每次指定类型都很繁琐。因此变量 thing 的类型是 string。

```
let thing = 'hello';
thing = 1; //error 
```

但是如果我们想要一个既可以是数字又可以是字符串的变量呢？我们可以给它任何类型，但更好的方法是指定多个类型。

```
let thing: string|number = 'hello';
thing = 1; 
```

因为变量是推断出来的，我们通常在声明变量时不指定类型。那么我们什么时候指定类型呢？对于参数和返回类型非常有用。

```
//this function must return a string, 
// because of : string after the param parenthesis
function greet(name: string, age: number): string {
  return `Hi ${name} who is ${age} years.`;
}
const msg = greet('Gaute', 27);

//this funciton can't return any values
function greet(name: string, age: number): void {
  console.log(`Hi ${name} who is ${age} years.`);
}
greet('Gaute', 27); 
```

这样，你未来的自己和同事总是知道这个函数需要什么类型，以及它将返回什么。

现在 TypeScript 让我们的 greet 方法每次都需要两个参数。在 JavaScript 中，我们可以传递零个或无限个参数。因此，TypeScript 有可选的参数，可以在方法、类和接口中使用。如果参数被省略，变量将是未定义的。

```
function greet(name: string, age?: number): string {
  let info = '';
  if (age) {
    info = `who is ${age} years.`;
  }
  return `Hi ${name}  ${info}`;
}
//It's okay to don't pass age
const msg = greet('Gaute');

interface Dialog{
  size: number,
  title?: string
}
//It's okay to don't add title
const window: Dialog = {
  size: 1
} 
```

## 结论

这是你需要知道的基本知识，也可能是你的项目需要知道的全部知识。