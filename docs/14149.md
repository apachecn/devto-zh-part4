# 获取字符串变量的未定义错误

> 原文：<https://dev.to/amcgrath815/getting-undefined-error-for-string-variable-1eof>

我们正在用 ReactJS 和 NextJS 创建一个简单的搜索应用程序。我们正在搜索弹性搜索索引。一切都很顺利，但现在我需要让一些结果看起来更好。在进行搜索时，我将返回一个字段，这是一个充满\n\n 字符的 JSON 对象(在对该对象使用 JSON.stringify 之后)，我需要将它转换为回车。

当我在浏览器中使用这个字段的值时，它是一个长字符串。当我尝试做:
let fc = JSON . stringify(field name)；
newfc = fc.replace("\n "，" ")；//比如说——我真的需要用 br

我得到“fc 未定义”。但 fc 显示正常，直到我尝试使用替换或分割，或任何方法。我在控制台中没有错误。

为什么我在这个字符串上没有定义？有没有其他方法可以将\n 转换成回车？