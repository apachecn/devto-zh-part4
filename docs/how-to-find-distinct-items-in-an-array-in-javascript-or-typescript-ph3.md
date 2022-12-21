# 如何在 javascript 或 typescript 中查找数组中的不同项

> 原文：<https://dev.to/darraghor/how-to-find-distinct-items-in-an-array-in-javascript-or-typescript-ph3>

我必须将一堆工作项目按组织区域分类，所以我需要知道数组中对象的不同类别。

在 C#/Linq 中有一个很好的 IEnumerable。Distinct(f(x))方法可用，但是 JavaScript 还没有类似的东西。如果可以避免的话，我不想安装 lodash 这样的助手。

幸运的是，我的列表是简单的字符串，所以我可以使用“Set()”。

```
// The solution is to map your objects to the array of identifiers. note: This only works if the identifier is, or can be reduced to, a primative type. If you supply objects it will use object references which might not act as you expect. There is no IEquatable in JS either.

// map objects to a list of identifier strings
let areaNames = stagingBuild.workitems.map(x => x.area)

//distinct categories will be distinct! The set will check if the value already exists
// A value in the Set may only occur once; it is unique in the Set's collection.
const distinctCategories = [...new Set(areaNames)]

// watch out becuase undefined and NaN can be stored in the Set.
// And in the case of strings, casing is respected for uniqueness 
```

Enter fullscreen mode Exit fullscreen mode