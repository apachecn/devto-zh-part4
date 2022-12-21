# 快速提示！映射循环已经返回了一个数组

> 原文：<https://dev.to/baltz/quick-tip-map-loop-already-returns-a-array-3jic>

当我们使用 map loop
创建一个新数组时，这种方法很常见

```
const newArray = []

array.map((item) => {
  newArray.push(item.value)
}) 
```

Enter fullscreen mode Exit fullscreen mode

对吗？但是 map 循环已经返回了一个数组。
所以我们可以这样做来代替

```
const newArray = array.map((item) => {
  return item.value
}) 
```

Enter fullscreen mode Exit fullscreen mode

[![Cool huh!](img/5dfbc6ae495bf4cc95b405cf62864cbc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6eZVAVSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://giphygifs.s3.amazonaws.com/media/KI9oNS4JBemyI/giphy.gif) 
爽哼！