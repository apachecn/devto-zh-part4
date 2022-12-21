# 嵌套对象克隆灾难！！！！！

> 原文：<https://dev.to/hasan101002/nested-object-clone-disaster-4efo>

我只是试图克隆一个参数，并在一些操作后更新 redux 状态。但是一些不需要的值正在更新，导致克隆也复制了对象的引用。通常我们试图通过
将一个对象的值赋给另一个对象

```
let something= sourceObject 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果对象是嵌套的，它有时会改变源对象。克隆的另一种方式是，

```
let something= {...sourceObject} 
```

Enter fullscreen mode Exit fullscreen mode

还是一样的结果。也试过，

```
let something= Object.assign({},sourceObject) 
```

Enter fullscreen mode Exit fullscreen mode

场景没有变化。然后，洛达什前来救援，

```
let something= _.cloneDeep(sourceObject); 
```

Enter fullscreen mode Exit fullscreen mode

现在，它只是克隆对象而不改变它。