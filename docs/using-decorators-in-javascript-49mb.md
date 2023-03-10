# 在 JavaScript 中使用装饰器

> 原文：<https://dev.to/tpiros/using-decorators-in-javascript-49mb>

> 这篇文章最初出现在[全栈训练博客](https://blog.fullstacktraining.com/using-decorators-in-javascript/)上。

在本文中，我们回顾了 JavaScript 中的 decorator，并且我们还将看一看一些例子和怪癖，如果您现在开始尝试 decorator 的话，您可能会遇到这些例子和怪癖。

装饰者的流行(或者更确切地说，崛起)主要归功于 Angular 2+和 TypeScript，因为它们构成了前端框架的核心部分。然而，什么是装修工呢？它们为什么有用？为什么在 JavaScript 中使用它们会很有用？让我们试着回答这些问题。

在撰写本文时，根据 TC39 流程，装饰者的提议处于第 2 阶段。这意味着如果进展顺利，很快 decorators 也将成为 JavaScript 语言的一部分，但是也要注意，这个提议可能会改变，本文中的一些陈述可能不再正确。

# 有棱角的装饰者(打字稿)

让我们从一个简单的装饰器开始——假设你是一个 Angular 开发人员或者以前看过一些 Angular 代码——看起来应该非常熟悉:

```
//some.component.ts
@Component({
  selector:    'app-my-list',
  templateUrl: './some.component.html'
})
export class SomeComponent implements OnInit {
  // ...
} 
```

在上面的代码中，通过对类`SomeComponent`应用一个装饰器来赋予它额外的功能(或者换句话说，我们用一些额外的功能来装饰一个现有的类)。这里的装饰器是`@Component({})`,我们可以认为它通过包装装饰器本身的代码为类提供了一些额外的功能。这是由函数组合或高阶函数定义的相同概念(这是 React 大力提倡的概念)。

简单地说，装饰器只是一个能够扩展它所附着的元素的功能的函数。

今天，我们可以利用 JavaScript 中的高阶函数，用一种相当简单的方式没有问题:

```
function hello(user) {
  console.log(`Hi ${user}`);
}

function punctuate(fn) {
  return function(user) {
    fn(`${user}! Looking great today ;)`);
  };
};

const greeting = punctuate(hello);
greeting('John'); // Hi John! Looking great today ;) 
```

> 在 TypeScript 中使用 decorators 可以通过`tsc`的`--experimentalDecorators`命令行标志或者通过向`tsconfig.json`添加以下配置来实现:
> 
> ```
> {  "compilerOptions":  {  "target":  "ES5",  "experimentalDecorators":  true  }  } 
> ```

# 装饰图案

decorator 模式是一种面向对象的编程模式，在这种模式下，各个类可以被动态地赋予额外的功能，而不会影响同一个类的实例。现在，将这一点转化为 JavaScript 开发人员，这意味着即使我们今天可以在语言中使用高阶函数，我们也不能将相同的原则(HOF 的原则)应用于使用 ES2015 编写的类。

关于 decorator 和 JavaScript，我们还需要注意一个限制，即 decorator 只能在类和类成员上使用。

# 在 JavaScript 中使用装饰器

如前所述，装饰器不能直接在 JavaScript 中使用，因为它们还处于提议阶段。这意味着我们不得不求助于使用 Babel 来传输代码，这些代码使用了当前被浏览器或 Node.js 理解的 decorator。Babel 插件[@ Babel/plugin-proposal-decorator](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)允许我们实现这一点。

> 请注意，在撰写本文时，Babel 支持该提案的旧版本(第 2 阶段-2018 年 11 月)，但该提案自那时起已取得进展。建议暂时配合`legacy: true`选项使用该插件，直到提案中的最新变化被 Babel 实现。

让我们继续通过`.babelrc`文件创建一个 babel 配置，其内容如下:

```
{
  "plugins": [
    ["@babel/plugin-proposal-decorators", {
      "legacy": true
    }]
  ]
} 
```

为了简单起见，我使用 Node.js 来执行我的代码，并且在我的`package.json`文件中设置了下面的 npm 脚本:

```
"scripts":  {  "babel":  "node_modules/.bin/babel decorator.js --out-file decorator.es5.js",  "start":  "node decorator.es5.js"  }, 
```

这允许从终端执行`npm run babel && npm start`。

# 类成员装饰器

让我们看看如何给类成员添加装饰器——在本例中，是给类成员函数:

```
class Greeter {
  constructor(name) {
    this.name = name;
  }
  @punctuate('!')
  hello() {
    return `Hi ${this.name}`;
  }
}

function punctuate(value) {
  return function(target, key, descriptor) {
    descriptor.value = function hello() {
      return `Hi ${this.name}${value}. You are awesome ;)`;
    };
  }
}

const greeting = new Greeter('John');
greeting.hello(); // Hi John!. You are awesome ;) 
```

如你所见，装饰器只是一个函数(`punctuate()`)，我们可以决定给它传递参数(没有任何参数的装饰器当然也是有效的)。在这个特殊的例子中，我们覆盖了`hello()`函数正在做的事情，我们返回了两个句子，而不是简单的语句。继续改变`@punctuate('!')`装饰器，用`?`替换`!`，观察会发生什么。

让我们再深入一点，看看装饰函数中的参数包含了什么:

```
function punctuate(value) {
  return function(target, key, descriptor) {
    console.log('target', target);
    console.log('key', key);
    console.log('descriptor', descriptor);
  }
}

// returns ==>

target Greeter {}
key hello
descriptor { value: [Function: hello],
  writable: true,
  enumerable: false,
  configurable: true } 
```

正如我们从上面所看到的，目标是我们正在处理的类，关键是类成员函数(这也验证了我们之前所说的，装饰器处理给定的类方法)，然后我们有描述符，它是描述数据或访问器的对象。在 JavaScript:
中使用`Object.defineProperty()`时，您可能以前见过描述符对象

```
Object.defineProperty({}, 'key', {
  value: 'some value',
  configurable: false,
  enumerable: false,
  writeable: false
}); 
```

因为我们可以访问所有这些属性值，我们可以通过将`writeable`属性从`true`更改为`false`来使我们的属性成为只读的——这意味着仅仅通过使用装饰器我们就可以使类成员成为只读的:

```
class Greeter {
  constructor(name) {
    this.name = name;
  }
  @readonly()
  hello() {
    return `Hi ${this.name}`;
  }
}

function readonly() {
  return function(target, key, descriptor) {
    descriptor.writable = false;
    return descriptor;
  }
}

const greeting = new Greeter('John');
greeting.hello = function() {
  return 'Never gets called! :(';
}

console.log(greeting.hello()); // Hi John 
```

> 注意，新的 hello 函数从来没有被调用过——作为一个测试，去掉`@readonly` decorator，然后看看代码的行为。

我们也可以使用这种技术使类成员(非方法)成为只读的。

> 注意，为了让下面的例子工作，我们需要安装`@babel/plugin-proposal-class-properties`并用选项`loose: true`将其添加到`.babelrc`。这是必要的，因为类属性还不是 JavaScript 语言的一部分。

```
class Greeter {
  @readonly name = 'John';
  hello() {
    return `Hi ${this.name}`;
  }
}

function readonly(target, key, descriptor) {
  descriptor.writable = false;
  return descriptor;
}

const greeting = new Greeter();
greeting.name = 'Jack';
greeting.hello(); // Hi John 
```

如您所见，我们不能覆盖`name`属性，因为我们已经将其修饰为只读。

> 作为比较，看看如果在`.babelrc` :
> 中使用`legacy: false`选项，`target`对象会有什么不同

```
function punctuate(value) {
  return function(target) {
    console.log(target);
  }
}

// returns ==>

Object [Descriptor] {
  kind: 'method',
  key: 'hello',
  placement: 'prototype',
  descriptor:
   { value: [Function: hello],
     writable: true,
     configurable: true,
     enumerable: false } } 
```

> 返回的信息是相同的，但这次返回的是整个描述符对象。

# 类装修工

到目前为止，我们已经看到了如何修饰类方法，但是也有可能修饰整个类。主要区别在于，类成员装饰器只对前面的方法或属性有效，而类装饰器应用于整个类。同样，就像类成员装饰器一样，它们也接受参数。

我们来看一个例子:

```
@isEmployee
class Greeter {
  constructor(name) {
    this.name = name;
  }
  hello() {
    return `Hi ${this.name}`;
  }
}

function isEmployee(target) {
  return class extends target {
    constructor(...args) {
      super(...args);
      this.isEmployee = true;
    }
  }
}

const greeting = new Greeter('John');
greeting; // Greeter { name: 'John', isEmployee: true } 
```

如上所述，我们能够使用注释向我们的类添加一个新的属性。

使用`legacy: false`选项会是什么样子？这当然涉及到更多的编码，但是结果是一样的:

```
@isEmployee(false)
class Greeter {
  name = 'John';

  hello() {
    return `Hi ${this.name}`;
  }
}

function isEmployee(value) {
  return function(descriptor) {
    const {
      kind,
      elements
    } = descriptor;

    const newElements = elements.concat([{
      kind: 'field',
      placement: 'own',
      key: 'isEmployee',
      initializer: () => {
        return value
      },
      descriptor: {
        configurable: true,
        writable: false,
        enumerable: true
      }
    }]);
    return {
      kind,
      elements: newElements
    }
  }
}

const greet = new Greeter();
greet; // Greeter { name: 'John', isEmployee: false } 
```

如果我们不想向装饰者发送参数，我们可以这样做:

```
function isEmployee(descriptor) {
  const {
    kind,
    elements
  } = descriptor;

  const newElements = elements.concat([{
    kind: 'field',
    placement: 'own',
    key: 'isEmployee',
    initializer: () => {
      return false
    },
    descriptor: {
      configurable: true,
      writable: false,
      enumerable: true
    }
  }]);
  return {
    kind,
    elements: newElements
  }
} 
```

# TC39 规格和`core-decorators.js`

我建议你阅读装饰者的建议和规范，因为它包含了关于它们的重要信息:它讨论了内置装饰者(这将是 JavaScript 语言的一部分)以及其他内容。此外，我还推荐查看 [core-decorators.js](https://www.npmjs.com/package/core-decorators) ，它允许你使用一些按照 TC39 规范定义的阶段 0 装饰器。

# 结论

希望这篇文章已经让您对装饰者的当前状态有了一个很好的了解。它们无疑是有用的，我们现在可以在 TypeScript 中毫无问题地使用它们(使用适当的标志)，但 JavaScript 支持也即将到来。我试图概述我在试验这个特性时遇到的一些怪癖——很明显，变化正在实现中，但是一旦它们成为 JavaScript 标准的一部分，它们将为开发人员提供一些所需的额外功能。