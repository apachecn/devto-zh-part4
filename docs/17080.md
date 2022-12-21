# 如何在 TypeScript 中使用接口描述函数类型

> 原文：<https://dev.to/antjanus/how-to-describe-a-function-typing-using-an-interface-in-typescript-4jge>

我总是忘记如何做到这一点，但是 TypeScript 支持使用接口来描述函数签名(它的参数和返回),这并不困难。例如，要描述一个常规的节点样式的回调，您可以这样写:

```
interface NodeStyleCallback {
  (err, data): any
} 
```

Enter fullscreen mode Exit fullscreen mode

该语法本质上是一个常规的接口，正如您所习惯的，圆括号中的参数带有一个返回类型:

```
interface SomeFunction {
  (arg1, arg2, arg3, arg4): any
}

function someFunction(arg1, arg2, arg3, arg4) {
 // doesn't matter what the return, it'll always satisfy `any`
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以为参数分配类型！

```
interface Sum {
  (arg1: number, arg2: number): number
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然你不能给一个函数分配一个接口，但是你可以用这个接口来描述变量——包括这样的参数:

```
interface IObject {
  [key: string]: any
}

interface EventHandler {
  (e: IObject): any
}

const handler: EventHandler = (e) => {
  console.log(e);
};

function handleOnClick(domEl, eventHandler: EventHandler) {
  domEl.addEventListener('click', eventHandler);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 那么泛型呢？

您可以使函数接口像任何其他接口一样通用。

```
interface SpecialHandler<U> {
  (event: U): any
}

const handler: SpecialHandler<string> = (eventString) => {
  document.title = eventString;
};

function registerEvent(eventName, handler: SpecialHandler) {

} 
```

Enter fullscreen mode Exit fullscreen mode