# 那一刻我意识到 forEach()不返回任何东西。

> 原文：<https://dev.to/kaynguyen/the-moment-i-realized-foreach-does-not-return-anything-4ab5>

**Javascript ES5 的`forEach()`方法和我一直相处得很好，直到上周我试图在这个简单的函数中实现它，这个函数接受两个数组并决定它们是否包含公共项。**

## **我的两个`forEach()`回路之一拒绝完成任务**

所以我有两个测试数组开始:

```
const animals = ["lion", "dog", "kangaroo"];
const pets = ["hamster", "parrot", "dog", "cat"];

haveCommonItems(animals, pets); // should return "true" 
```

Enter fullscreen mode Exit fullscreen mode

显然，两个输入都包含`"dog"`，所以在这种情况下，我的函数被调用时应该能够返回`true`(在一个假设相反的情况下，返回`false`)。为了[时间复杂度优化](https://en.wikipedia.org/wiki/Time_complexity)，我决定给它两个独立的循环(而不是嵌套的)，使用 Javascript ES5 的`Array.prototype.forEach()`方法:

```
const haveCommonItems = (array1, array2) => {
  array1.forEach(
    // converts array1 items into properties
    // returns an object that has those properties
  );

  array2.forEach(
    // checks if array2 items equal to properties on object created from first loop
    // returns a boolean
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我的第一个`forEach()`循环通过产生预期的对象来完成它的工作，就像这样:

```
const haveCommonItems = (array1) => {
  let obj = {};
  array1.forEach(item => !obj[item] ? obj[item] = true : null);
  console.log(obj);
};

haveCommonItems(animals); // logs {lion: true, elephant: true, dog: true, kangaroo: true} 
```

Enter fullscreen mode Exit fullscreen mode

当我的第二个孩子出生时，事情开始让我困惑。在运行函数时，我期望得到从未发生的`true`;相反我得到了一个`false` :

```
const haveCommonItems = (array1, array2) => {
  let obj = {};
  array1.forEach(item => obj[item] ? null : obj[item] = true);

  array2.forEach(item => {
    if (obj[item]) {
      return true;
    }
  });

  return false;
};

haveCommonItems(animals, pets); // returns "false" (me: "c'mon gimme a break!") 
```

Enter fullscreen mode Exit fullscreen mode

显然，要么我的`array2.forEach()`从未真正循环过数组本身，要么它确实循环过，但它只是自私，没有返回任何东西。

我对后者的看法是正确的。

## **那个所谓的回路里面究竟在想些什么？**

我跳转到 [MDN web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 做了一点研究，结果发现了一个我以前几乎没有注意到的主要特性:

> ### `forEach()` always returns the value `undefined`, which cannot be linked.

*和*

> ### There is no other way to stop or interrupt the `forEach()` loop except to throw an exception.

原来我一直有一个错误的印象，认为`forEach()`在那里是为了完全接管我的老朋友`for()` loop 的工作。事实上，`forEach()`接受了一个`callback`函数，该函数确实返回值，但是**只在`forEach()`创建的范围内接受**。换句话说，那个`callback` **里面的 return 语句从来没有**把值带出它的作用域或者退出函数；它只将该值返回给`forEach()`，然后在将`undefined`返回给它的老板`haveCommonItems()`之前，继续遍历数组的其余部分。

我的函数`haveCommonItems()`在这一点上(在得到那个`undefined`值时)不知道在那个`forEach()`里面发生了什么(可怜的小家伙！)，所以它继续到达自己的返回语句，给了我一个`false`。

所以，是的，当那个啊哈时刻还在持续的时候，我想出了一个解决方案

```
const haveCommonItems = (array1, array2) => {
  let obj = {};
  array1.forEach(item => obj[item] ? null : obj[item] = true);

  let found = false;
  array2.forEach(item => {
    if (obj[item]) {
      found = true;
    }
  });
  return found;
};

haveCommonItems(animals, pets); // returns "true" (me: "you better do!") 
```

Enter fullscreen mode Exit fullscreen mode

Javascript ES5 的`forEach()`方法，虽然总是返回`undefined`，但确实会执行副作用。因此，一旦项目等于任何一个`obj`属性，它就能够将`found`的值设置为`true`。(**注意**`forEach()`继续循环，直到数组结束，因为除非抛出异常，否则不能停止或中断)。

所以我在这里，记下这些，希望不再被一个循环所愚弄。

* * *

## **更新**

感谢下面我的开发伙伴们的精彩推荐，我已经升级了我的代码，如下所示:

```
const haveCommonItems = (array1, array2) => {
    const array1set = new Set(array1);
    return array2.some(item => array1set.has(item));

haveCommonItems(animals, pets); // returns "true"
} 
```

Enter fullscreen mode Exit fullscreen mode

再次感谢伙计们！