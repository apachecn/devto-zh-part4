# Jasmine Gotcha: spyOn(…)和. callThrough()只是简单地复制了参数。

> 原文：<https://dev.to/alexeyfeigin/jasmine-gotcha-spyon-and-callthrough-makes-only-a-shallow-copy-of-arguments-5108>

我最近使用 Jasmine 框架编写了一些前端 JavaScript 测试，遇到了这个小问题，我将在这里描述。

假设我们想测试一个方法是否被调用，但也想让它执行它。

```
// Base code
Obj.prototype.outerMethod = function (config = {}) {
  if (!config.subConfig) {
    config.subConfig = {};
  }
  config.subConfig.option = true;
  return this.innerMethodReturning0(config);
};
// (Excuse the ES5-style method definition…) 
```

Enter fullscreen mode Exit fullscreen mode

我们想测试是否用正确的参数调用了`innerMethodReturning0`,但出于某种原因也想让它执行。在这种情况下，测试用正确的配置调用了`innerMethodReturning0`。

(实际上我们应该单独测试`innerMethodReturning0`而不是通过调用…这是为了保持简单而设计的。)

```
// Test code
const obj = new Obj();
spyOn(obj, 'innerMethodReturning0').and.callThrough();
const result = obj.innerMethodReturning0();
expect(obj.innerMethodReturning0).toHaveBeenCalledWith({ subConfig: { option: true } });
expect(result).toEqual(0); 
```

Enter fullscreen mode Exit fullscreen mode

这可能没问题，但是让我们考虑一下如果`innerMethodReturning0`改变它的参数会发生什么。

```
// innerMethodReturning0 shallow mutation implementation
Obj.prototype.innerMethodReturning0 = function (config) {
  config.shallowProperty = true;
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个管用。

现在让我们考虑一下`innerMethodReturning0`改变了参数的深层属性的情况。例如，它可以在 config 对象上设置自己的默认设置`config.subConfig.option2: true`。

```
// innerMethodReturning0 deep mutation implementation
Obj.prototype.innerMethodReturning0 = function (config) {
  config.subConfig.option2 = true;
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，测试将失败:

```
Expected obj.innerMethodReturning0 to have been called with
{ subConfig: { option: true } }
but was called with
{ subConfig: { option: true, option2: true } }. 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 Jasmine 只在 spy 的入口处对实际的参数做了一个简单的复制，以便稍后进行比较。这意味着如果`innerMethodReturning0`对实参的深度属性进行了变异，那么实际的实参对象树也会发生变异。

下面是一个局部的解决方法，其中我们维护我们自己的参数的深层克隆。

```
// Test code
const obj = new Obj();
const callArgs = [];
const innerMethodReturning0 = obj.innerMethodReturning0.bind(obj);
spyOn(obj, 'innerMethodReturning0').and.callFake((config) => {
  callArgs.push(JSON.parse(JSON.stringify(config)));
  return innerMethodReturning0(config);
});
const result = obj.innerMethodReturning0();
expect(callArgs.length).toEqual(1);
expect(callArgs[0]).toEqual({ subConfig: { option: true } });
expect(result).toEqual(0); 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，JavaScript 中的深度克隆是可疑的，因为错误对象、函数、DOM 节点和 WeakMaps 不能被克隆(更不用说对象中的循环引用了)。

我没有在 Mocha 或其他测试框架中测试过这一点，但我怀疑由于 CPU 成本和深度克隆的限制，他们会在这样的设置中遇到类似的问题。(知道的请写在评论里。)

可能的话，最好避免使用`spyOn(…).and.callThrough()`模式。当论点可能变异时，一定要避免。

(感谢本·伍德库克和亚科夫·史密斯对本文的反馈。)