# 创建一个简单的 Vue 多元化过滤器

> 原文：<https://dev.to/webhookrelay/creating-a-simple-vue-pluralize-filter-j9m>

有很多方法可以创建一个多元化的函数，但是在 Vue 中你应该使用过滤器。让我们创建一个，因为它总是很方便。

## 第一步:添加复数包

让我们用[https://github.com/blakeembrey/pluralize](https://github.com/blakeembrey/pluralize)来做繁重的工作:

```
yarn add pluralize 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:注册过滤器

我们的复数过滤器将接受两个参数——第一个是过滤器中左边的变量，第二个是过滤器函数参数(取决于你的代码结构，但它通常是 *main.js* 或一个专用的过滤器文件):

```
// .. your other imports
import pluralize from 'pluralize'

// .. your other code

Vue.filter('pluralize', function (value, number) {
  return pluralize(value, number)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:使用滤镜！

现在，为了使用左边的过滤器，我们给它我们想要复数化的单词，作为一个参数，我们传递计数:

```
{{ tunnels }} {{ 'tunnel' | pluralize(tunnels) }} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样:)现在，基于**隧道**变量，它将是“1 个隧道”或“50 个隧道”。

希望这能在你需要的时候对你有用！