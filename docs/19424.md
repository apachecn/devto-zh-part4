# JavaScript 数组方法参考

> 原文：<https://dev.to/chandrasd/reference-to-array-methods-in-javascript-57bb>

JavaScript 提供了大量的数组方法。这里是不同类别中一些有用的参考

## 添加/删除元素:

*   `push(...items)`–将项目添加到数组的末尾
*   `pop()`–从数组末尾提取一个项目
*   `shift()`–从数组的开头提取一个项目
*   `unshift(...items)`–在开头添加项目
*   `slice(start, end)`–创建一个新的数组，将元素从开始位置复制到结束位置(不含结束位置)。
*   `concat(...items)`–返回一个新数组:复制当前数组的所有成员，并向其中添加项目。如果任一项是数组，则取其元素。

## 对数组进行变换

*   `map(func)` -从调用`func`的结果中为每个元素创建一个新数组。
*   `sort(func)` -就地排序数组，然后返回。
*   `reverse()` -就地反转数组，然后返回。
*   `split/join` -将字符串转换成数组，然后再转换回来。
*   `reduce(func, initial)` -通过为每个元素调用`func`并在调用之间传递中间结果来计算数组上的单个值。

## 在数组中搜索

*   `indexOf/lastIndexOf(item, pos)` -从位置`pos`开始寻找`item`，如果没有找到则返回索引或`-1`。
*   `includes(value)` -如果数组有`value`，则返回`true`，否则返回`false`。
*   `find/filter(func)` -通过函数过滤元素，返回第一个/所有使其返回`true`的值。
*   `findIndex` -类似于`find`，但是返回索引而不是值。

## 在数组中迭代

*   `forEach(func)` -为每个元素调用`func`。

关于数组和数组方法的更详细的参考，请查看 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Referenaace/Global_Objects/Array)