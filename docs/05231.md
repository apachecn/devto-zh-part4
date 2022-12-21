# ES-下一次动态导入

> 原文：<https://dev.to/natelindev/es-next-dynamic-import-1ndn>

#### 问题

最近在写我的一个项目时，我写了一些这样的 javascript】

```
import itemA from '../items/itemA';
import itemB from '../items/itemB';
import itemC from '../items/itemC';
import itemD from '../items/itemD'; 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢这样的语法:

```
for(const id of ['A','B','C','D']) {
    import (item+id) from '../items/item' + id;
} 
```

Enter fullscreen mode Exit fullscreen mode

原来有一种叫做`dynamic import`的`stage 4 proposal of ECMAScript`是这样的:

```
(async () => {
  await import('./my-app.mjs');
})(); 
```

Enter fullscreen mode Exit fullscreen mode

所有现代浏览器和 node 都支持它。

#### 尝试失败

然后我继续写了一些类似这样的代码:

```
importedItems = await Promise.all(
  itemNames.map(async itemName => {
    try {
      const imported = await import(`../items/${itemName}`);
      logger.debug(`Imported item: ${itemName}`);
      return imported;
    } catch (err) {
      logger.warning(`Failed to import item: ${itemName}`);
      return null;
    }
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

但是为了以后使用`importedItems`，已经提出了一个`TypeError`，声明`importedItems`实际上是`Module`的类型，而不是预期的`item`。我试过这样的手动类型铸造:

```
return Item(imported); 
```

Enter fullscreen mode Exit fullscreen mode

但是它没有工作，因为它还不是标准化的特性(将在 ECMAScript 2020 中标准化)，所以关于它的信息很少，所以我必须自己解决它。

和`JSON.stringify()`和`console.log`相处了很久之后，我终于想通了。

#### 解

它应该是这样工作的:

```
return imported.default; 
```

Enter fullscreen mode Exit fullscreen mode

完整的工作片段

```
importedItems = await Promise.all(
  itemNames.map(async itemName => {
    try {
      const imported = await import(`../items/${itemName}`);
      logger.debug(`Imported item: ${itemName}`);
      return imported.default;
    } catch (err) {
      logger.warning(`Failed to import item: ${itemName}`);
      return null;
    }
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

#### 原因

原因是当我们像这样使用一个`import`语句:

```
import item from './itemA'; 
```

Enter fullscreen mode Exit fullscreen mode

它自动将模块`'itemA'`的`default export`加载到`item`中。

但是当我们像这样做动态导入的时候:

```
(async () => {
  const item = await import('./itemA');
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这个项目是一个`Module`，通过访问`Module.default`，我们能够获取它的默认导出，对于任何其他导出也是如此。

最初发布于:[https://blog.llldar.io/Article/View/44](https://blog.llldar.io/Article/View/44)