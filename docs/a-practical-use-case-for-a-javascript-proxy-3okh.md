# JavaScript 代理的一个实际用例

> 原文：<https://dev.to/lgraubner/a-practical-use-case-for-a-javascript-proxy-3okh>

当 JavaScript 代理被引入时，我很兴奋。这听起来像是一个不错的功能，但毕竟我缺乏任何实际的用例。直到现在！几天前，我在重构一些代码时，遇到了一个对象，它的内容有大写键。为了保持一致，我真的想让它们小写，但是这些键也存在于用户设备的本地状态中。所以仅仅在代码库中改变它们就会破坏它。为了切换到小写键，我记得 JavaScript 代理。

有了 [JavaScript 代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，你可以挂钩到对象的 getter 和 setter 机制。所以我的想法是用不区分大小写的键做一个代理来访问对象值。这实际上很容易实现:

```
function caseInsensitive(target) {
  return new Proxy(target, {
    get: (obj, key) => obj[key.toLowerCase()]
    set: (obj, key, value) => {
      obj[key.toLowerCase()] = value
      // required, see https://lrs.link/proxy
      return true
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，getters 和 setters 只是用来访问对象的键的小写字母。这种方式适用于以下所有情况:

```
const animals = caseInsensitive({
  species: 'monkey'
})

animals['SPECIES'] // monkey

animals['SpEcIeS'] = 'giraffe'
animals['species']// giraffe 
```

Enter fullscreen mode Exit fullscreen mode

简单，但有效这适合我的需要。最后，我可以说我已经使用 JavaScript 代理解决了一个问题。如果你知道任何其他用例，请在评论中告诉我！