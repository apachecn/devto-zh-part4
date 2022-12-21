# 用代理修复空指针异常

> 原文：<https://dev.to/iostreamer/fixing-null-pointer-exceptions-with-proxies-19fc>

不再有`Cannot set property of undefined`

* * *

顾名思义，代理充当对象的拦截器。用户访问的每个属性、设置的每个属性、每个操作都要通过代理。

这里有一些文章可以帮助您快速了解代理:

*   [https://ponyfoo.com/articles/es6-proxies-in-depth](https://ponyfoo.com/articles/es6-proxies-in-depth)
*   [https://ponyfoo.com/articles/more-es6-proxy-traps-in-depth](https://ponyfoo.com/articles/more-es6-proxy-traps-in-depth)

现在我们对使用代理有了一些了解，我们可以尝试解决这个问题，这个问题是在实现我正在开发的多租户架构时出现的。

假设我们有一个空对象`x = {}`，出于某种原因，我们想要做`x.y.z = 23;`而不仅仅是`x.y.z`我们想要做的是添加键和子项，无限地添加到`x`。因此，`x.y.z = 23`和`x.y.z.a.i.v.b.n.s = 99`不应该抛出错误，而是应该分别创建以下对象:

```
x = {
  y: {
    z: 23
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

和

```
x = {
  y: {
    z: {
      a: {
        i: {
          v: {
            b: {
              n: {
                s: 99
              }
            }
          }
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是，如果我们在`x = {}`上做`x.y.z = 23`，我们将试图访问属性`z`，假设`y`存在于`x`上，但实际上不存在，我们在`x`中拥有的只是`{}`。

但是如果我们的`x`非常聪明，以至于如果我们试图访问一个不存在的属性，它会动态地创建那个属性，而我们的代码甚至不知道！

如果我们在`x`上拦截每个属性访问调用，检查属性是否实际存在，如果不存在，用空对象初始化它，然后继续我们的日常工作，会怎么样？！

因此，执行`x.y.z = 23`将首先在`x`上创建`y`，然后将设置`z`

这就是代理闪耀的地方，我们可以用这样一个简单的代理来实现这个魔法:

```
function getUndefinedHandlerProxy() {
    const undefinedHandlerProxy = new Proxy({}, {
        get(target, key) {
            if (target[key] === undefined) {
                target[key] = {};
            }
            return target[key];
        },
    });
    return undefinedHandlerProxy;
} 
```

Enter fullscreen mode Exit fullscreen mode

`new Proxy($1, $2)`的第一个参数基本上是`x`，或者说我们想要一个图层在上面的对象，我们想要截取的对象。第二个参数是我们的处理程序，一个陷阱和拦截器的集合。

这里，我们决定使用`get` trap 拦截属性访问调用。正如我们之前计划的那样，我们将检查属性(或本例中的键)是否存在于我们的对象(这里称为目标)上。这是使用行`if (target[key] === undefined)`完成的，如果没有，我们返回一个空对象，就像我们计划的那样。

让我们看看这如何帮助我们解决问题:

```
const x = getUndefinedHandlerProxy();
x.y.z = 23;
console.log(x.y.z); 
```

Enter fullscreen mode Exit fullscreen mode

我们的`x`现在是一个代理，`x.y.z`是在我们的代理上执行的，它在我们试图访问`y`时捕获了我们。它检查`y`是否存在，如果不存在，`x.y`返回一个空对象，之后，`z`被简单地设置在那个返回的空对象上！

不过有一个小问题，如果我们有了`x = getUndefinedHandlerProxy();`并做了`x.y.z.a = 78`会怎么样？由于`x`是代理，`y`将被自动创建为空白对象。但是由于`y`是一个简单的 plebian，访问`y.z.a`会有问题，因为`z`实际上并不存在，所以，我们会在`undefined`上设置`a`，现在我们又回到了原来的问题。我们需要`y`像`x`一样聪明，而不是一个简单的对象，所有被创建的子属性也是如此。如果`y`实际上是一个代理，就像`x`一样呢？

我们可以通过稍微改变我们的代理来做到这一点，而不是做`target[key] = {}`，我们可以做`target[key] = getUndefinedHandlerProxy()`。

现在我们的最终代码是:

```
function getUndefinedHandlerProxy() {
    const undefinedHandlerProxy = new Proxy({}, {
        get(target, key) {
            if (target[key] === undefined) {
                target[key] = getUndefinedHandlerProxy({});
            }
            return target[key];
        },
    });
    return undefinedHandlerProxy;
} 
```

Enter fullscreen mode Exit fullscreen mode

而这个，处理**一切**。

最后一点需要注意的是，当我们试图访问一个不存在的属性时，我们的代理总是会创建一个对象。现在，如果我们想检查一个属性是否存在，这在日常 javascript 中是很常见的情况。

正在做:

```
if(ourSpecialProxy.a && ourSpecialProxy.a.x) {
  // Some special code
} 
```

Enter fullscreen mode Exit fullscreen mode

不会按预期工作，条件将**总是**返回真。

避免这种情况的一种方法是不直接检查属性的存在，我们需要检查属性是否是一个实际的对象/字符串/数字。或者如果该属性不是代理。

为此，我们有:

```
function getActualValueFromUndefinedhandlerProxy(target, key) {
    if (util.types.isProxy(target[key])) {
        return undefined;
    }
    return target[key];
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的函数，接受一个目标和一个键，如果`targey[key]`是代理，那么整个事情就是一个门面，它返回`undefined`。如果没有，则返回`target[key]`。

就这样，我们实现了我们设定的目标。

*不再有`Cannot set property of undefined`！*