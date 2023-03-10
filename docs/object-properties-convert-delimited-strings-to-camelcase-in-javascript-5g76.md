# 对象属性:在 JavaScript 中将分隔字符串转换为 camelCase

> 原文：<https://dev.to/calebpitan/object-properties-convert-delimited-strings-to-camelcase-in-javascript-5g76>

几个月前，我接了一个非常好的项目，让我快乐地编码。这是一种没有依赖关系的项目。你还能指望什么我不得不吃香草口味的 DOM。

在没有 jQuery 及其数据 API 的情况下，我必须在程序获得数据属性名称后访问`HTMLElement.dataset`。

在 JavaScript 中，当使用点符号时，对象属性只有在属性名满足变量的命名约定时才能被访问——我的意思是:包含`[A-Za-z0-9_$]`。更精心地说，`[\w$]+[\d\w$]*`。

问题是。标记中的数据属性使用连字符作为分隔符，但是 javascript 不允许在数据集的本地对象表示中使用这种方式。取而代之的是进行一些转换，使它成为它应该成为的样子——骆驼案例。

我也面临着同样的问题。输入到程序中的属性名也用连字符连接。所以我必须自己进行转换，并使用索引语法从`HTMLElement.dataset`对象中访问它。

## 转换

这种转换只需要几行代码，这是一个简单的解决方案。

感谢评论中的`@Order & Chaos Creative`,他让我意识到我们实际上可以使用正则表达式。这个问题可以用两个简单的方法解决。

## 方法 1

```
function camelCase(name, delim = '-') {
  const list = Array.isArray(data) ? data : data.split(delim)
  return list.reduce((res, cur) => res + cur.charAt(0)
    .toUpperCase() + cur.slice(1)
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`delim`参数有一个缺省值，它是一个连字符。如果名字已经被分块到一个数组中，我们就让它保持原样，否则我们就把它分割成一个数组——在每次出现`delim`的时候。最好采用的方法是数组 reduce，`Array.prototype.reduce`，因为我们不需要转换连字符前的第一个单词。我们将下一个单词中的第一个字符转换成大写字母，并去掉其余的字符，然后将字符串连接起来。

## 方法二

```
function camelCase(name, delim = '-') {
  const pattern = new RegExp((delim + "([a-z])"), "g")
  return name.replace(pattern, (match, capture) => capture.toUpperCase())
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们只捕获连字符后面的小写字母，然后将一个箭头函数作为回调传递给`replace`方法，该方法获取原始匹配和捕获的组。捕获的字符串是我们需要转换成大写的。

## 申请

我们去掉了“data-”前缀，因为我们不需要它来访问数据集中的任何属性。它有 5 个单位的长度。调用函数和 eureka！

```
const name = "data-hello-to-everyone"
const cutStart = 5
const newName = camelCase(name.substring(cutStart))
// newName: "helloToEveryone" 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以得到我们的财产价值。

```
const value = HTMLElement.dataset[newName] 
```

Enter fullscreen mode Exit fullscreen mode

**注意:** HTMLElement.dataset 不是一个对象文本。它是一个`DOMStringMap`对象。

```
interface HTMLElement {
  dataset: DOMStringMap;
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望你喜欢这堂简短的课。别忘了在这里和 twitter 上关注我，留下你的反应和评论。