# 用 flatMap 缩进(类似 xml)文本！

> 原文：<https://dev.to/pcdevil/indent-xml-like-text-with-flatmap-47d6>

对于一个业余爱好项目，我需要缩进我的代码编译的 XML 文本，并提出了一个非常简单的解决方案。

输入是一个带有 XML 标记的数组，所有子节点都嵌套在其他数组中。

一个简短的例子:

```
const input = [
    '<!DOCTYPE html>',
    '<html lang="en">',
    [
        '<head>',
        [
            'Hello, World',
        ],
        '</head>',
        '<body>',
        [
            '<h1>It works!</h1>',
        ],
        '</body>',
    ],
    '</html>',
]; 
```

我想要实现的输出是这样的:

```
<!DOCTYPE html>
<html lang="en">
    <head>
        Hello, World
    </head>
    <body>
        <h1>It works!</h1>
    </body>
</html> 
```

对于给定的输入，下面的代码将实现目标:

```
 function indentText (nodes) {
    return Array.isArray(nodes)
        ? nodes.flatMap(indentText).map(node => '\t' + node)
        : nodes;
}

const output = input
    .flatMap(indentText)
    .join('\n');

console.log(output); 
```

回电演练:

*   传递给 [Array.prototype.flatMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap) 的回调将遍历所有项目，并期望返回一个项目或一个数组用于*展平*。
*   如果传递的值不是一个数组，它就返回它——因为我们想保持这个值。
*   如果是数组，那就完全是另一回事了:因为 flatMap 只在第一层起作用，所以该方法通过 flatMap 递归调用自身来解析更深的层。
    *   递归调用确保实际的调用中没有数组，只有文本(假设输入是正确的)
    *   映射项目并为其添加缩进前缀。在最深的层次上，它将在每个项目上添加一个字符，这个字符将在递归调用过程中向上累积