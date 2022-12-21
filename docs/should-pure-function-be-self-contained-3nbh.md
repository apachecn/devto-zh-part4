# 纯函数应该是独立的吗

> 原文：<https://dev.to/satansdeer/should-pure-function-be-self-contained-3nbh>

我的[上一个关于纯函数的视频](https://dev.to/satansdeer/pure-functions-and-side-effects-2hi7)我说过纯函数应该是自包含的，也就是说，它应该只从自己的作用域中访问值。

```
function pureFunction(){
  return 'Some string literal';
  // Doesn't perform side effects, is deterministic
  // thus we can be sure that it's pure 
}

function thisWontBePure(){
  return pureFunction();
  // Isn't pure because calls function from global scope.
  // Even though the function we are calling here is pure.
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你希望一个函数是纯函数，它应该只从它的参数中获取值:

```
function nowItsPure(anotherPureFunction){
  return anotherPureFunction();
  // Isn't pure because calls function from global scope.
} 
```

Enter fullscreen mode Exit fullscreen mode

对此你怎么看？纯函数可以从全局范围调用其他纯函数而仍然保持纯吗？