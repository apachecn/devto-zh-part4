# ES2015 代理简介

> 原文：<https://dev.to/copperwall/introduction-to-es2015-proxys-31lf>

我今天在工作中玩了一些`Object.defineProperty`东西，我想我应该再看看 Proxys，因为我一直在设置 getters 和 setters 以及可枚举和可写字段。这让我有了一些 JavaScript 的想法。

ES2015 中的代理对象可让您通过组合目标对象(原始对象)和处理程序规范来创建新对象。处理程序规范是一个带有方法的对象，当对从`new Proxy(target, handler)`返回的对象执行某些操作时，就会调用这些方法。当我说对象时，我不仅仅指像`{ key: value }`这样的东西，函数也是对象，还有像`new String("hello")`这样的东西。 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy#Methods_of_the_handler_object) 一如既往地提供了您可以添加的所有可能的处理程序方法的精彩参考。一些非常酷的例子是 [handler.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/apply) 、 [handler.construct()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/construct) 、 [handler.set()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/set) 和 [handler.get()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/get) 。

一个愚蠢的例子是创建一个响亮的对象，这个对象在任何时候设置或访问一个值。我们的处理程序规范包括`get`和`set`方法，每当那些特定的动作发生在我们的对象上时，就会调用它们。此示例打印出对象发生的情况，并输出堆栈跟踪以查看操作发生的确切位置。如果你想知道更多关于一个对象如何在你的代码中被使用的信息，这是一个有趣的技巧。

值得注意的是，传递给代理处理程序的`target`参数是传递给代理构造函数的原始对象。*不是抄袭，是同一个参考。*你在那个对象上做的任何突变或改变都会影响到原来的对象，如果不小心的话可能会引入 bug。

```
const myObject = {};
const loudObject = new Proxy({}, {
  get(target, p) {
    console.log(`Accessing key ${String(p)} at ${(new Error()).stack}`);
    return target[p];
  },

  set(target, p, value) {
    console.log(`Setting key ${String(p)} to ${String(value)} at ${(new Error()).stack}`);
    target[p] = value;
    return true;
  }
});

// "Accessing key hello at Error
//    at Object.get (/Users/user/projects/proxy/index.js:21:62)
//    ..."
loudObject.hello;
// "Setting key hello to woop at Error
//    at Object.get (/Users/user/projects/proxy/index.js:21:62)
//    ..."
loudObject.hello = 'woop';

myObject.hello // 'woop' 
```

Enter fullscreen mode Exit fullscreen mode

代理的一个有趣的应用是为测试创建模拟或间谍功能。如果您曾经使用过 Jest、Sinon 或其他添加了对间谍和模仿的支持的测试框架(Mockito 是 JavaScript 领域之外的另一个框架)，那么您可能已经创建了一个模仿函数，以便在应用程序代码中使用该函数时断言调用该函数的内容。

下面的例子展示了如何创建一个`test.spy`方法，该方法接受一个函数并返回一个代理函数，该代理函数跟踪对代理函数的每个函数调用的参数和返回值的列表。需要注意的是，`test.spy`的返回值与传入的原始函数的行为相同，但是它有一个特殊的处理程序，在函数被调用时运行，记录关于该调用的元数据。您可以使用代理来检测您的函数，而不需要您的调用代码知道这一点。

```
const test = {
  spy: function(fn) {
    const calls = [];
    return new Proxy(stuff, {
      apply(target, thisArg, args) {
        const result = target.apply(thisArg, args);
        calls.push([args, result]);
      },

      get(target, p){
        if (p === 'calls') {
          return calls;
        }

        return target[p];
      }
    });
  }
};

function stuff(arg1, arg2) {
  return `${arg1}  ${arg2} cool`;
}

function doSomeStuff(fn) {
  return stuff(1, 2);
}

const spyStuff = test.spy(stuff);

spyStuff("hello", "cool");

doSomeStuff(spyStuff);

const calls = spyStuff.calls;
assert(calls[0][0][0] === "hello");
assert(calls[1][1] === "hello cool cool"); 
```

Enter fullscreen mode Exit fullscreen mode

代理可以是一个非常强大的抽象，但是我还没有在代码中过多地使用它，所以我不知道它是否有用，也不知道它什么时候会使你的程序过于复杂。为调试和测试用例添加它听起来很新奇，但是我不知道使用这种抽象的性能成本是多少，所以我不确定它是否是在生产代码中使用的最佳选择。

如果你遇到了任何很酷的代理用例，我真的很想听听它们！我将尝试制作一个工具，通过带有 get 和 set 处理程序的代理，使用代理在 S3 持久化 JS 对象，这可能会在我的下一篇文章中介绍。