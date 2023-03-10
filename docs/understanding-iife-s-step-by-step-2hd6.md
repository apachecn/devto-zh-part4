# 理解生活的一步一步

> 原文：<https://dev.to/oinak/understanding-iife-s-step-by-step-2hd6>

## 立即调用函数表达式

哇，那是一口。根据[Wikipedia](https://en.wikipedia.org/wiki/Immediately_invoked_function_expression)的说法，IFFE 是一种 JavaScript 编程语言习语，它使用 JavaScript 的函数作用域来产生词法作用域。

但是让我们假设*词法作用域*、*变量提升*和*函数作用域*还不是你能适应的术语，不要担心，让我们看看一个 IFFE 实际上是做什么的，一次一个例子。

基本上，生活就是:

*   一项功能
*   立即评估
*   返回一个对象
*   具有公共属性(方法和值)
*   那可以指非公有的
*   并且不暴露私人的

所以最简单的形式是这样的:

```
f1 = function(){
  let secret_a = 1;
  function secret_b(){
    return 2;
  }

  return { public_a: secret_a, public_b: secret_b };
}

let obj1 = f1()

console.log('obj1.public_a: ' + obj1.public_a);         // obj1.public_a: 1
console.log('obj1.public_b: ' + obj1.public_b());       // obj1.public_b() 2
console.log('obj1.secret_a: ' + typeof(obj1.secret_a)); // obj1.secret_a: undefined
console.log('obj1.secret_b: ' + typeof(obj1.secret_b)); // obj1.secret_b: undefined 
```

Enter fullscreen mode Exit fullscreen mode

现在想象我们用`f1 =`
右边的所有东西替换`f1`

```
let obj2 = (
  // here starts f1
  function(){
    let secret_a = 1;
    function secret_b(){
      return 2;
    }

    return { public_a: secret_a, public_b: secret_b };
  } 
  // here ends f1
)()

console.log('obj2.public_a: ' + obj2.public_a);         // obj2.public_a: 1
console.log('obj2.public_b: ' + obj2.public_b());       // obj2.public_b() 2
console.log('obj2.secret_a: ' + typeof(obj2.secret_a)); // obj2.secret_a: undefined
console.log('obj2.secret_b: ' + typeof(obj2.secret_b)); // obj2.secret_b: undefined 
```

Enter fullscreen mode Exit fullscreen mode

这有同样的效果，我们已经认识到生命的著名形式。

```
(function(){ ... })() 
```

Enter fullscreen mode Exit fullscreen mode

但是一个生命不仅可以返回一个新的对象，它还可以向一个对象添加东西:

```
let obj3 = { prop1: 3 };

let obj4 = (function(expose){ // we call expose what comes from outside
  function secret_b(){
    return 2;
  }

  expose.public_b = function(){ // we add properties to expose
    return secret_b() + expose.prop1; // and read from it
  }

  return expose; // we return the received object with extra stuff
})(obj3); // we call the IIFE with some object

console.log('obj4.prop1: ' + obj4.prop1);         // obj4.prop1: 3
console.log('obj4.public_b: ' + obj4.public_b()); // obj4.public_b() 5 
```

Enter fullscreen mode Exit fullscreen mode

注意这里有四个变化:

*   我们命名我们期望的论点
*   我们给收到的对象添加东西
*   我们返回一个抽象的对象
*   我们称这种生活为来自外界的争论

但是现在，每个人都在加载多个具有复杂管道的文件，在这里，生命可以通过丰富它们来帮助你:

```
// file 1
MyObj = (function(expose){
  let secret_b = 4;

  expose.public_b = function(){
    return secret_b;
  }
  return expose;
})(window.MyObj || {});

// file 2
MyObj = (function(expose){
  expose.public_c = function(){
    return expose.public_b() + 5;
  }
  return expose;
})(window.MyObj || {});

console.log('myObj.secret_b: ' + typeof(MyObj.secret_b)); // myObj.secret_b(): undefined
console.log('myObj.public_b: ' + MyObj.public_c());       // myObj.public_b() 9 
```

Enter fullscreen mode Exit fullscreen mode

这适用于任何顺序的`file 1`和`file 2`加载，所以你可以在你的对象中有一些基本的/共享的东西，并根据需要在某些页面上增加它，但是只有当用户加载它们的时候。

这很快就会变得非常疯狂，所以最好对它强加一些约定，这样你就知道会发生什么以及将东西放在哪里

```
// Use you company or app name here to keep or your stuff namespaced
// separatedly from 3rd party libraries
window.Namespace = window.Namespace || {};

// Componen documentation
// What it does: functional description
// Example usage:
//   Namespace.Component.init({ selector: '#some_id', some_param: 30 });
window.Namespace.Component = (function(expose){
  let private_some;

  function private_helper(){
    // stuff
  }

  expose.public_method = function(){
    // stuff
  }

  expose.init = function(options){
    private_some = option.some;
  }

  return expose;
})(window.Namespace.Component || {}); 
```

Enter fullscreen mode Exit fullscreen mode

然后就可以用:

```
Namespace.Component.init({
  selector: '#some_id',
  some_param: 30
}); 
```

Enter fullscreen mode Exit fullscreen mode

这样你就可以在同一个地方拥有选择器定义和引用，如果 html 需要改变，很容易修改。

我还建议总是使用`'js-xxxx'`风格的类和 id(或数据属性),这样它们就不会干扰设计/布局任务。

你觉得怎么样？你已经使用类似的模式了吗？这对你理解生活有帮助吗？这里有什么你还不清楚用途的东西吗？