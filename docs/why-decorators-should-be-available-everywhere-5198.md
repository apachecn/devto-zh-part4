# 为什么装修工应该随处可见

> 原文：<https://dev.to/adam_cyclones/why-decorators-should-be-available-everywhere-5198>

请表示支持！[https://github.com/tc39/proposal-decorators/issues/119](https://github.com/tc39/proposal-decorators/issues/119)

让我们开始吧，我相信这个例子比文字更能说明问题。

```
class SimpleRouter {
   @get '/home'
   ({ res }) {
      return res.html();
   }

   @post 'submit/myform'
   ({ somePlugin, res }) {
      somePlugin.handleData(res.data);
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

*ps。你觉得这个有点粗糙的语法怎么样，很酷吧？*

但是为什么这不能只是一个有装饰者的对象，我被迫使用 singleton 没有好的理由。

所以人们说你不需要类，但是我担心语法糖会越来越多。