# 装饰者并不像你想象的那样工作🤔

> 原文：<https://dev.to/angular/decorators-do-not-work-as-you-might-expect-3gmj>

在使用一个名为 [ngx-template-streams](https://github.com/typebytes/ngx-template-streams) 的库时，简单地说，它允许您在 Angular 模板中将事件作为流来处理，我发现 decorators 不是基于实例的，而是基于类的。我没有意识到这种行为，并认为装饰器是针对每个类实例应用的。在这篇博文中，我们将仔细观察装饰者，并探究他们为什么会这样，以及我们如何创建基于实例的装饰者。

*由 Garett Mizunaka 在 Unsplash 上拍摄的封面照片*

# 对装修工的快速回顾

装修工很棒。它们允许我们为类声明和成员添加注释和元编程语法，包括属性、访问器、参数和方法。换句话说，我们可以使用 decorators 给一个对象附加额外的责任，而不用修改任何其他对象。因此，以声明的方式组合功能块是很棒的。这意味着 [decorator 设计模式](https://en.wikipedia.org/wiki/Decorator_pattern)的设计方式是多个 decorator 可以相互堆叠，每个 decorator 都添加新的功能。

同样，许多人认为 decorators 是子类化的一种**灵活选择。子类化在编译时添加行为，因此影响所有实例，而装饰者在**运行时**向单个对象添加行为。**

因此，装饰师越来越受欢迎，坦率地说是有原因的。它们使我们的代码更容易阅读、测试和维护。因此，一些领先的开源项目采用了装饰设计模式，包括 [Angular](https://github.com/angular/angular) 、 [Inversify](https://github.com/inversify) 或 [Nest](https://github.com/nestjs/nest) 。

好吧，那么什么是室内设计师？

[Idan Dardikman](https://medium.com/@idandrd) 精彩总结了这个问题:

> 装饰器只是用一个函数包装一段代码的干净语法

TypeScript 对装饰者有**实验性的**支持。然而，有一个 [ECMAScript 装饰提案](https://github.com/tc39/proposal-decorators)已经到达第二阶段(草案)，所以他们最终可能会在 vanilla JS 中登陆。

如前所述，有不同类型的装饰者。例如，我们可以给一个类附加一个装饰器:

```
@Component()
class HeroComponent {}
} 
```

对于类装饰者来说，`@Component()`是一个很好的例子，它是 [Angular](https://angular.io/) 中的核心构件之一。它将额外的元数据附加到该类。

很可能你还会遇到一些**属性**、**方法**或**参数**装饰者:

```
@Component()
class HeroComponent {
  @Input() name: string;

  constructor(@Inject(TOKEN) someDependency: number) {}

  @deprecated
  greet() {
    console.log('Hello there!');      
  }
} 
```

所以装饰者是相当通用的，富于表现力的，强大的。现在，这篇博文并不是要详细解释装饰者。在这篇文章中，我们实现了一个属性装饰器来探究它们的行为，但是我们不会看其他类型装饰器的实现。如果你想了解更多关于装修工的知识，我强烈推荐[官方文件](https://www.typescriptlang.org/docs/handbook/decorators.html)，这[温柔的介绍](https://medium.com/iqoqo-engineering/understand-typescript-decorators-in-5-minutes-26ffc6189082)或者[这](https://blog.wizardsoftheweb.pro/tag/typescript/)关于装修工的各种话题的精彩系列。

# @钳工装饰工

是时候用一个例子来理解我在开始提到的行为了。据说 decorators】不是面向实例的，每个类和用法只调用**一次。**

为了证明这一点，我们将实现我们自己的名为`Clamp`的[属性装饰器](https://www.typescriptlang.org/docs/handbook/decorators.html#property-decorators)。

要在 TypeScript 中使用 decorators，我们必须启用名为`experimentalDecorators`的编译器选项。最好的地方是`tsconfig.json` :

```
{  "compilerOptions":  {  "target":  "ES5",  "experimentalDecorators":  true  }  } 
```

我们现在可以创建一个`Clamp`装饰器，应用于类型`number`的属性。它的工作是将属性值限制在指定的上限和下限内。

例如，如果下限是`10`，上限是`50`，那么我们的修饰应该将一个值固定在这些界限内:

```
clamp(5) // => 10
clamp(100) // => 50 
```

我们稍后将实现该功能，但是首先，让我们将注意力转移到属性装饰器上。

一个属性装饰者有如下签名:

```
type PropertyDecoratorType = (target: any, propertyKey: string | symbol) => void; 
```

这是一个普通的老式**函数**，有两个参数。`target`是**拥有**装饰属性的**对象**,`propertyKey`是装饰属性的**名称**。现在，你可能认为`target`是一个类的实例，但事实并非如此。这个`target`仅仅是这个类的**原型**，但是稍后会有更多的介绍。

上面的签名描述了一个属性装饰器，它被很好地定义了。这意味着参数是固定的，没有扩展签名的空间。然而，我们的装饰器应该是**可配置的**，并接受一个`lower`和`upper`绑定。因此，我们不得不使用一个**工厂功能**。换句话说，我们将 decorator 方法包含在另一个定义所有可配置选项的方法(工厂)中:

```
function Clamp(lowerBound: number, upperBound: number) {
  return (target: any, propertyKey: string | symbol) => {
    // logic goes here
    console.log(`@Clamp called on '${String(propertyKey)}' from '${target.constructor.name}'`);
  }
} 
```

很好，我们把一个普通的装饰工变成了一个装饰工厂来释放更多的能量。耶！

在实现逻辑之前，让我们先试一试！我们将创建一个类`TestBench`并用我们自制的`@Clamp`装饰器:
装饰一些属性

```
class TestBench {
  @Clamp(10, 20)
  a: number;

  @Clamp(0, 100)
  b: number;
} 
```

那是我们简单的测试台。注意，我们没有创建`TestBench`类的实例。所以在运行这段代码之前，让我们做一个小测试:

**问题:**你预计会发生什么？

*   **答**:没事。装饰器没有被调用，因为我们没有创建类的实例；因此，不会记录任何内容。
*   **B** :装饰厂每班叫一次**；因此，只有**一个**值被打印到控制台。**
***   **C** :工厂调用两次，每个属性调用一次；因此，将有两个**值打印到控制台。***   **D** :爆炸。**

 **好，击鼓...🥁🥁🥁

运行这段代码会得到以下输出:

```
@Clamp called on 'a' from 'TestBench'
@Clamp called on 'b' from 'TestBench' 
```

Tada！等等，什么？所以看起来我们的装饰函数被调用了两次，每个装饰属性一次。这意味着上面问题的答案是 **C** 。如有疑问，这里有一个现场演示:

[https://stackblitz.com/edit/decorators-post-1?embed=1&view=preview&](https://stackblitz.com/edit/decorators-post-1?embed=1&view=preview&)

现在的问题是，为什么，为什么在我们没有创建类的实例的情况下就调用了 decorator 方法。

# 探索装饰者的引擎盖下

为了找到这个问题的答案，我们必须更深入一点，看看如果我们使用装饰器，TypeScript 编译器实际上会生成什么。你可以运行`tsc`或者复制并粘贴代码到[类型脚本平台](http://www.typescriptlang.org/play/)。无论我们做什么，我们都应该得到下面的编译代码:

```
"use strict";
var __decorate = (this && this.__decorate) || function (decorators, target, key, desc) {
    var c = arguments.length, r = c < 3 ? target : desc === null ? desc = Object.getOwnPropertyDescriptor(target, key) : desc, d;
    if (typeof Reflect === "object" && typeof Reflect.decorate === "function") r = Reflect.decorate(decorators, target, key, desc);
    else for (var i = decorators.length - 1; i >= 0; i--) if (d = decorators[i]) r = (c < 3 ? d(r) : c > 3 ? d(target, key, r) : d(target, key)) || r;
    return c > 3 && r && Object.defineProperty(target, key, r), r;
};
function Clamp(lowerBound, upperBound) {
    return (target, propertyKey) => {
        // logic goes here
        console.log(`@Clamp called on '${String(propertyKey)}' from '${target.constructor.name}'`);
    };
}
class TestBench {}
__decorate([
    Clamp(10, 20)
], TestBench.prototype, "a", void 0);
__decorate([
    Clamp(0, 100)
], TestBench.prototype, "b", void 0); 
```

乍一看这并不容易理解，尤其是这个在顶部定义的有点神奇的`__decorate`函数。但是这种方法非常重要，尤其是它的消费方式。

那么`__decorate`从何而来，又是做什么的呢？这个方法来自编译器的最深处，在使用任何类型的修饰器时都会生成。TypeScript 有一个生成这段代码的助手，它被称为 [`decorateHelper`](https://github.com/microsoft/TypeScript/blob/master/src/compiler/transformers/ts.ts#L3293-L3304) 。一定要查看源代码。这是一个很好的学习资源。

好吧，但是它有什么用呢？简单地说，它循环遍历每个传入的装饰器，并尝试对它们进行评估。详细的解释超出了这篇文章的范围。幸运的是，有一篇优秀的[博文](https://netbasal.com/behind-the-scenes-how-typescript-decorators-operate-28f8dcacb224)对此进行了深入的解释。

所以让我们把注意力转移到生成代码的底部:

```
__decorate([
    Clamp(10, 20)
], TestBench.prototype, "a", void 0);
__decorate([
    Clamp(0, 100)
], TestBench.prototype, "b", void 0); 
```

这就是消耗`__decorate`函数的地方。此外，我们可以看到它被调用了两次，每个修饰属性调用一次，两者都获得了相同的传入的`target`，即`TestBench.prototype`。第二个参数是`propertyKey`，最后一个参数是属性描述符。在这里，`void 0`用来传`undefined`。

> `void`操作符接受一个表达式，对其求值并返回`undefined`。更多信息，请参见 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void)。还有一个很棒的 [StackOverflow 答案](https://stackoverflow.com/questions/7452341/what-does-void-0-mean)解释了与只使用`undefined`相比，这个小操作符的用途。**TL；博士** : JavaScript 并不是一个完美的世界，`undefined`也不是一个保留关键字，这意味着它只是一个变量名，我们可以给它赋一个新值。

所以上面的代码是由 TypeScript 编译器生成的代码，通常我们会在浏览器中加载代码，一旦文件被加载，它就会在浏览器中执行。换句话说，decorator 将在我们使用 decorator 的类被加载的时候被应用。因此，装饰者(这里是我们的属性装饰者)只能访问类的原型和属性名，而不能访问实例。这是**的设计**，现在我们知道编译器生成了什么，这一切都是有意义的。

到目前为止，关键的收获应该是我们现在知道了为什么 decorators 不是面向实例的，而是在浏览器中加载 JavaScript 时执行。

意识到这一点很重要，因为否则，我们可能会经历意想不到的行为。为了理解这一点，我们必须向我们的装饰器添加逻辑。

# 问题

事实上，decorators 是在类被加载的时候应用的，而不是在我们创建实例的时候，这并不是不正确的，这实际上是设计使然。那么可能会出什么问题呢？

为了找出这一点，我们从实现实际的箝位功能开始。所以让我们创建一个名为`makeClamp`的工厂，它返回一个绑定了`upper`和`lower`的`clamp`函数。在这里再次使用工厂函数可以使功能更加可重用。

```
function makeClamp(lowerBound: number, upperBound: number) {
  return function clamp(value: number) {
    return Math.max(lowerBound, Math.min(value, upperBound));
  }
} 
```

我们可以看到这个工厂返回了一个`clamp`方法。这里有一个我们如何使用它的例子:

```
const clamp = makeClamp(0, 10);

console.log(clamp(-10)); // => 0
console.log(clamp(0));   // => 0
console.log(clamp(5));   // => 5
console.log(clamp(10));  // => 10
console.log(clamp(20));  // => 10 
```

上面的例子应该让我们正确理解装饰者应该做什么。用`@Clamp`标注的类属性应该在包含的`lower`和`upper`范围内裁剪属性值。

简单地将它添加到 decorator 函数中是不够的，因为我们希望 decorator 在一个实例上操作，并且它应该在每次设置属性时固定属性值。

假设我们不知道`target`只是一个类的原型，那么我们使用`Object.defineProperty`修改目标上已经存在的属性。这将允许我们定义一个`getter`和`setter`，这正是我们所需要的。我们要做的是:

1.  使用工厂`makeClamp`创建所需的`clamp`方法。
2.  维护一些用于存储钳位属性值的内部状态。
3.  使用`Object.defineProperty`修改目标属性，并提供一个`getter`和`setter`，这样我们可以拦截对值的任何修改，并通过我们的`clamp`方法运行它。

将它写入代码可能是这样的:

```
function Clamp(lowerBound: number, upperBound: number) {
  return (target: any, propertyKey: string | symbol) => {
    console.log(`@Clamp called on '${String(propertyKey)}' from '${target.constructor.name}'`);

    // 1\. Create clamp method
    const clamp = makeClamp(lowerBound, upperBound);

    // 2\. Create internal state variable that holds the clamped value
    let value;

    // 3\. Modify target property and provide 'getter' and 'setter'. The 'getter'
    // simply returns the internal state, and the 'setter' will run any new value
    // through 'clamp' and update the internal state.
    Object.defineProperty(target, propertyKey, {
      get() {
        return value;
      },
      set(newValue: any) {
        value = clamp(newValue);
      }
    })
  }
} 
```

让我们也更新我们的测试工作台，为了简单起见，删除一个属性，并创建测试类的两个实例。此外，我们将属性设置为某个值:

```
class TestBench {
  @Clamp(10, 20)
  a: number;
}

const tb1 = new TestBench();
console.log(`Setting 'a' on TB1`)
tb1.a = 30;
console.log(`Value of 'a' on TB1:`, tb1.a);

const tb2 = new TestBench();
console.log(`Value of 'a' on TB2:`, tb2.a); 
```

运行这段代码将打印以下输出:

```
@Clamp called on 'a' from 'TestBench'
Setting 'a' on TB1
Value of 'a' on TB1: 20
Value of 'a' on TB2: 20 
```

现在，这个输出似乎有点不对劲，不是吗？我们创建第一个实例`tb1`，并立即将属性`a`设置为`30`。这导致调用`setter`，它将值限制在指定的上限和下限内。结果应该是`20`，事实也是如此。到目前为止，一切顺利。然后我们创建另一个实例`tb2`并简单地读取属性，导致`getter`被调用。尽管我们还没有在第二个实例上设置值，但它还是返回了`20`。为什么？

这就是我所说的*意外行为*的含义，至少如果我们没有意识到`target`不是类实例而是原型的事实。所以对目标的任何修改都会影响到每个实例，因为我们是在全局修改类的原型。此外，本来应该是每个装饰器的内部状态的`value`在所有实例中被**共享**，因为它们都**共享同一个装饰器范围**。事情就是这样，但是对于我们的用例来说，这并不酷。

看看这个现场演示！我强烈建议您稍微研究一下代码。

[https://stackblitz.com/edit/decorators-post-2?embed=1&view=preview&](https://stackblitz.com/edit/decorators-post-2?embed=1&view=preview&)

# 创建面向实例的装饰器

那么，如果我们希望我们的装饰器是基于实例的，我们该怎么做呢？我们当然不希望在全局范围内跨实例共享状态。

解决方案包括在应用装饰器后修改目标属性，以及在实例上用相同的属性名定义一个属性。换句话说，我们在目标原型上定义了一个带有`setter`的属性，一旦第一次使用，它将在目标实例上安装一个具有相同名称的属性，即`propertyKey`。

好了，让我们来看看代码。我添加了一大堆注释，以便更容易理解正在发生的事情:

```
function Clamp(lowerBound: number, upperBound: number) {
  return (target: any, propertyKey: string | symbol) => {
    console.log(`@Clamp called on '${String(propertyKey)}' from '${target.constructor.name}'`);

     // Create clamp method
    const clamp = makeClamp(lowerBound, upperBound);

    // Create map to store values associated to a class instance
    const values = new WeakMap();   

    // Define property on the target with only a `setter` because we don't
    // want to read from the prototype but instead from the instance.
    // Once the value of the property is set for the first time we define
    // a property with a `getter` and `setter` on the instance.
    Object.defineProperty(target, propertyKey, {
      set(newValue: any) {
        console.log('set on target');

        // This `setter` gets called once per new instance, and only the 
        // first time we set the value of the target property.

        // Here we have access to the instance `this`, so we define 
        // a property with the same name on the class instance.
        Object.defineProperty(this, propertyKey, {
          get() {
            console.log('get on instance');
            // This `getter` gets called every time we read the instance property.
            // We simply look up the instance in our map and return its value.
            return values.get(this);
          },
          set(newValue: any) {
            console.log('set on instance');
            // This `setter` is called every time we set the value of the 
            // property on the class instance.
            values.set(this, clamp(newValue));
          }
        });

        // Finally we set the value of property on the class instance.
        // This will trigger the `setter` on the instance that we defined above.
        return this[propertyKey] = newValue;
      }
    })
  }
} 
```

本质上，我们在`Object.defineProperty`中使用了`Object.defineProperty`，但是使用了不同的对象。第一个使用的是类原型`target`，第二个使用的是引用类实例的`this`。

另外，请注意，我们在装饰器的顶部使用了一个`WeakMap`来存储每个实例的属性值。一个`WeakMap`是一种特殊的`Map`，但不同的是，一个`WeakMap`并不阻止一个对象被垃圾收集，即使这个对象被用作`WeakMap`中的**键**。如果你想了解更多，看看这篇精彩的博客文章，这篇文章很好地解释了两者的区别。

好了，让我们来看看这个修改过的 decorator 版本，看看它是否真的是面向实例的，以及它是否不再在同一个类的所有实例之间共享状态。为此，我稍微更新了我们的测试平台，并添加了一些评论:

```
// When this class gets loaded, the decorator is applied and executed.
// This will define the `setter` for the target property on the prototype
// of this class.
class TestBench {
  @Clamp(10, 20)
  a: number;
}

const tb1 = new TestBench();

// This should return `undefined` because we didn't define a `getter`
// on the target prototype for this property. We only install a `getter`
// once we set the value for the first time.
console.log(`Reading 'a' on TB1`, tb1.a);

// This calls the `setter` for `target.a` and defines a property with 
// a `getter` and `setter` on the class instance for the same property.
tb1.a = 30;

// From this moment on, every time we read the value for this property
// we would call the most inner `getter`.
console.log(`Reading 'a' on TB1`, tb1.a);

// The same applies for updating the value. This will call the `setter`
// that we defined for the property of the class instance.
console.log(`Updating 'a' on TB1`);
tb1.a = 15;

// Creating a new instance doesn't do anything
const tb2 = new TestBench();

// Remember, we have globally defined a getter for `target.a` and because we
// are operating on a new instance, the target setter will be called which
// will set up the property on the new instance with their own `getter`
// and `setter` methods.
console.log(`Setting 'a' on TB2`);
tb2.a = 5;

console.log(`Reading 'a' on TB2:`, tb2.a);

// Remains unmodified because every instance has it's own property defined
// with their own `getter` and `setter`
console.log(`Reading 'a' on TB1:`, tb1.a); 
```

Tada！它似乎在起作用。我们刚刚实现了自己的装饰器，它工作在实例级，而不是基于原型的。我的意思是它仍然涉及到修改原型，但是现在每个 decorator 也操作单个实例，并且它们都是相互隔离的。

查看最终的解决方案，并仔细检查代码:

[https://stackblitz.com/edit/decorators-post-3?embed=1&view=preview&](https://stackblitz.com/edit/decorators-post-3?embed=1&view=preview&)

# 奖金

上面说明了一个成熟的解决方案，但是当我写这篇博文的时候 [Netanel Basal](https://twitter.com/NetanelBasal) 向我指出了一个更简洁、更干净的解决方案。它不需要对`Object.defineProperty`进行两次调用，因为他[发现](https://github.com/microsoft/TypeScript/issues/32395)没有忽略返回值，与文档中提到的相反，事实上返回值被用作对`Object.defineProperty`的调用的输入。

考虑到这一点，我们可以将上面的解决方案简化为下面的解决方案，它具有完全相同的行为:

```
function Clamp(lowerBound: number, upperBound: number): any {
  return (target: any, propertyKey: string | symbol) => {
    const clamp = makeClamp(lowerBound, upperBound);

    // We need a unique key here because otherwise we would be
    // calling ourselves, and that results in an infinite loop.
    const key = Symbol();

    // We can return a property descriptor that is used to define 
    // a property on the target given the `propertyKey`.
    return {
      get() {
        // Read the value from the target instance using the
        // unique symbol from above
        return this[key]; 
      },
      set(newValue: any) { 
        // Clamp the value and write it onto the target instance
        // using the unique symbol from above
        this[key] = clamp(newValue);
      }
    }
  }
} 
```

现在，这很干净，不是吗？🔥

这里有一个现场演示:

[https://stackblitz.com/edit/decorators-post-4?embed=1&view=preview&](https://stackblitz.com/edit/decorators-post-4?embed=1&view=preview&)

# 结论

Decorators 是基于类和属性的，这意味着当类被加载时，它们被应用和执行一次。这意味着`target`不是类实例，而是类的原型。对`target`的任何更改都是全局的，如果我们试图使用 decorator 作用域来维护某个内部状态，该状态将在同一个类的所有实例之间共享，并且它们都使用相同的 decorator 作用域。这可能会导致意外的行为。

然而，在本文中，我们已经看到了一个解决方案，它包含一个具有不同目标的 double `Object.defineProperty`来使装饰器基于实例。

希望到现在为止，你已经更好地理解了装饰者是如何工作的，以及为什么他们会有这样的行为。

如果你喜欢这篇文章，请随意给它竖起大拇指，如果你有任何问题或意见，请告诉我！

# 特别感谢

我要感谢 [Netanel Basal](https://twitter.com/NetanelBasal) 和 [Manfred Steyer](https://twitter.com/ManfredSteyer) 审阅了这篇文章并提供了宝贵的反馈。🙏**