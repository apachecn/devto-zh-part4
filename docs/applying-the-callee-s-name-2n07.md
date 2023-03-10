# 应用被调用者的名字，即自引用函数

> 原文：<https://dev.to/bugmagnet/applying-the-callee-s-name-2n07>

显然，有些人可以用`arguments`向量做的事情是令人不快的。这可能是其中的一件事。

人们可以在运行时找到自己所在的函数的名称。

```
function calleeName(a) {
  return a.callee
    .toString()
    .split("  ")[1]
    .split("(")[0]
    .trim();
}; 
```

Enter fullscreen mode Exit fullscreen mode

将`arguments`作为参数传入，并接收调用它的函数的名称。警告:这只适用于使用`function`定义的函数，不适用于`const`。此外，匿名函数将返回一个空字符串。

示例

```
function Bruce() {
    console.log(calleeName(arguments));
}

Bruce(); 
```

Enter fullscreen mode Exit fullscreen mode

这将在控制台上显示`Bruce`。

在我们开始最有趣的事情之前，这里有一个支持函数，我在生成小部分 HTML 或 XML 时不时会用到它(在后面的演示中我也需要用到)，`tag()`。

```
function tag(a, b, c) {
  return "undefined" === typeof c ? 
    "<" + a + ">" + (null === b ? "" : b.toString()) + "</" + a + ">" :
    "<" + a + "  " + b.toString() + ">" + 
      (null === c ? "" : c.toString()) + "</" + a + ">";
} 
```

Enter fullscreen mode Exit fullscreen mode

最初这里有一个`if`，但是在闭包编译器完成之后，它变成了一个复杂的三元组`boolean ? true : false`。`a`获取 XML/HTML 标记名。`b`是要换行的数据。然而，如果定义了`c`，那么它被假定为数据，而`b`被假定为放在`a`之后的属性。

现在我们来看焦点。下面我们有一些以 XML 标记命名的函数:`xml`、`staff`和`vehicle`。我们没有将标记名编码到函数体中，而是让`calleeName`从`arguments`向量中检索它，并将其解转移到参数的副本中，然后`.apply`将其转移到`tag`函数中。

```
function xml() {
  var args = [].slice.call(arguments);
  args.unshift(calleeName(arguments));
  return tag.apply(this, args);
}

function staff() {
  var args = [].slice.call(arguments);
  args.unshift(calleeName(arguments));
  return tag.apply(this, args);
}

function vehicle() {
  var args = [].slice.call(arguments);
  args.unshift(calleeName(arguments));
  return tag.apply(this, args);
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这些函数的主体是完全相同的，但是每个函数的输出是不同的。`xml()`将输出一个带有数据和可选属性等的`<xml></xml>`。

以便像这样使用它们:

```
var Staff = staff("id='BRUAXT' age='58' languages='English,Tok Pisin,Urdu'", "Bruce");
var Vehicle = vehicle("id='BRUAXT'", "bicycle");
var Xml = xml(Staff + Vehicle);
console.log(Xml); 
```

Enter fullscreen mode Exit fullscreen mode

哪个输出

```
<xml><staff id='BRUAXT' age='58' languages='English,Tok Pisin,Urdu'>Bruce</staff><vehicle id='BRUAXT'>bicycle</vehicle></xml> 
```

Enter fullscreen mode Exit fullscreen mode

虽然我现在还不需要它，但它很有趣，我可以想到一些可能的应用。也许你也可以。