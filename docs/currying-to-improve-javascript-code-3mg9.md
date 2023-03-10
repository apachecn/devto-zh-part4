# 谄媚以制造可读的代码

> 原文：<https://dev.to/jaloplo/currying-to-improve-javascript-code-3mg9>

# 如何运用涂抹技术使代码更容易阅读

几年前，我读了罗伯特·c·马丁的《干净的代码》一书，发现它非常有用。我读到的几乎所有东西似乎都很明显，但直到此刻我才应用它们。

这本书让我意识到，即使我应用了那种编程语言的特性和功能，我的代码对其他开发人员来说也不可能像我想的那么好。这打开了我的思路，我想，也许，一个好的代码，除了考虑性能之外，对其他人来说是最容易阅读的。

现在，我参与了一个简单的个人项目，并希望与您分享我是如何实现解析器功能的。

## 设置基础

我的项目的目的是让用户根据她的位置改变当前温度的范围。我的一个模块必须是一个将当前值的刻度转换为另一个刻度的对象，比如将摄氏度转换为华氏度或者将华氏度转换为摄氏度。

我定义的第一件事是转换方法将如何工作:

*   一个带有要转换的值的参数
*   返回唯一值

为了直观地理解这一点，所有的变换方法都必须是这样的:

```
function(value) {
    return value;
} 
```

## 实现

因此，第二步是实现一个对象来管理所有这些转换方法，并避免它们作为独立的方法存在于主脚本中。您可能已经做过类似的事情，所以让我分享代码并简单解释一下。

```
const TransformationMethodsHandler = function() {
  let collection = {};

  return {
    // Returns the transformation method by getting the source and target types
    get: function(source, target) {
      if(!collection[source] || !collection[source][target]) {
        return null;
      }

      return collection[source][target];
    },

    // Registers a new tranformation method using source and target as keys
    register: function(source, target, formula) {
      if(!collection[source]) {
        collection[source] = {};
      }
      if(!collection[source][target]) {
        collection[source][target] = {};
      }

      // Added transform property to make it more usable and readable from code
      collection[source][target].transform = formula;

      // Makes easier to keep on registering new methods
      return this;
    }
  };
} 
```

正如你在`register`函数中看到的，我需要源值的类型，还有目标值的类型来注册一个转换方法，这两个值都被用作键。看起来很简单，所以让我向你展示我是如何注册摄氏、华氏和开尔文标度的方法的:

```
let transformationHandler = new TransformationMethodsHandler()
  .register(CELSIUS_SCALE, KELVIN_SCALE, (degree) => degree + 273.15)
  .register(KELVIN_SCALE, CELSIUS_SCALE, (degree) => degree - 273.15)
  .register(CELSIUS_SCALE, FAHRENHEIT_SCALE, (degree) => (9*degree/5) + 32)
  .register(FAHRENHEIT_SCALE, CELSIUS_SCALE, (degree) => (5*(degree-32)) / 9)
  .register(FAHRENHEIT_SCALE, KELVIN_SCALE, (degree) => (5*(degree-32)) / 9 + 273.15)
  .register(KELVIN_SCALE, FAHRENHEIT_SCALE, (degree) => (9*(degree - 273.15)/5) + 32); 
```

除了我的解释，我还设置了`register`函数来返回对象本身，以便更容易注册所有的转换方法。我的代码似乎更好注册所有的规模。您可以添加更多的方法，甚至使用不同的刻度或类型，例如，我们可以在同一个处理程序中混合使用温度刻度和长度类型(米和英里)。

一旦我们初始化了 handler 对象，我们就可以开始使用它了。假设用户引入了一个值，或者您从 API 调用中读取了该值，我们如何使用 handler 对象将它转换为正确的值呢？

```
let celsius = 24;
let fahrenheit = transformationHandler.get(CELSIUS_SCALE, FAHRENHEIT_SCALE).transform(celsius);
let kelvin = transformationHandler.get(FAHRENHEIT_SCALE, KELVIN_SCALE).transform(fahrenheit);

console.log(celsius, fahrenheit, kelvin); // 24, 75.2, 297.15 
```

很酷，对吧？除了检索正确的转换方法之外，我们不需要做任何努力就可以改变温标。我认为这非常有用，可以让编码人员节省时间，并确定他们正在使用的转换方法。如果一个方法检索了一个错误的值，他们只需要在注册转换方法时修复这个错误。尝试添加更多的变换方法，并将值更改为不同的比例或类型。

反正我对这段代码感觉不太对，看似对但很难读懂。有没有一种方法可以让它更容易阅读？

## 运用谄媚手法

我想有一个不同的代码，以便让其他编码者阅读和理解的方式比以前的代码更容易。有可能实现下面的代码行吗？

```
let newValue = Transformer(value).from(source).to(target); 
```

好了，我们手中掌握的高级 javascript 技术之一来了，**。对于那些不知道什么是奉承的人，你有很多解释这种技巧的网络文章，[链接](https://duckduckgo.com/?q=currying+javascript&atb=v91-1&ia=web)。我的简单解释是 Currying 允许创建返回函数的函数，这些函数使用调用函数的参数或变量。这是一个简短的解释，但是总结了我将在下面几行中向您展示的内容。**

 **在这一步中，我们需要创建一个名为`Transformer`的新对象，它只用一个方法返回一个对象:`from`。`from`方法必须用`to`方法返回一个对象，这个方法必须返回转换后的值。让我们看看下面的代码:

```
let Transformer = (function(value) {
    return {
        from: function(source) {
            return {
                to: function(target) {
                    return transformationHandler.get(source, target).transform(value);
                }
            };
        },
    };
})(); 
```

你第一次读到这样的东西可能有点疯狂，直到你明白它是如何工作的。这是一个非常简单的 currying 函数，它解决了我们面临的问题，代码易于阅读。

现在，我们的代码可以用一种更简单的方式来修改:

```
let celsius = 24;

// CODE DIFFICULT TO READ
let fahrenheit = transformationHandler.get(CELSIUS_SCALE, FAHRENHEIT_SCALE).transform(celsius);
let kelvin = transformationHandler.get(FAHRENHEIT_SCALE, KELVIN_SCALE).transform(fahrenheit);

// CODE THAT IT IS EASIER TO READ
fahrenheit = Transformer(celsius).from(CELSIUS_SCALE).to(FAHRENHEIT_SCALE);
kelvin = Transformer(fahrenheit).from(FAHRENHEIT_SCALE).to(KELVIN_SCALE);

console.log(celsius, fahrenheit, kelvin); // 24, 75.2, 297.15 
```

我们可以从 currying 技术中获得更多的优势。让我们考虑显示当前的温度，但是使用所有可用的刻度。Currying 可以帮助我们简化流程。记住,`from`方法返回一个我们可以使用的对象。在这种情况下，我们可以将它赋给一个变量，然后用它来获得不同标度的值。

```
let transformFromCelsius = Transformer(celsius).from(CELSIUS_SCALE);
fahrenheit = transformFromCelsius.to(FAHRENHEIT_SCALE);
kelvin = transformFromCelsius.to(KELVIN_SCALE);

console.log(celsius, fahrenheit, kelvin); // 24, 75,.2, 297.15 
```

## 总结

总之，在本文中，您已经看到了如何注册方法来将值从一种类型转换为另一种类型，并在对象中处理它们。您还看到，即使有了一个好的功能实现，我们还可以更进一步，使用 currying 等高级技术让其他开发人员更容易阅读我们的代码。

我希望你觉得这篇文章有趣并且有用。让我知道你对它的想法。这里你有了[要点](https://gist.github.com/jaloplo/28a954ef407cc8303a980e00b7eab561)。**