# 一个 div 可以有多少个唯一的 CSS 属性🤔？

> 原文：<https://dev.to/lost_semicolon/how-many-unique-css-properties-can-a-div-have-o02>

有很多 CSS 属性！但是一个 div 怎么能有？让我们来了解一下！

```
const element = document.createElement("div");
let  totalCssProperiesNo = 0;

_.forEach(element, function(e){
   totalCssProperiesNo++
});

console.log(totalCssProperiesNo); 
```

你得到 522！从今天开始。

感谢 JavaScript 老师给他的 CSS 字典书，去年他在那里运行代码，得了 415 分。