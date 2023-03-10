# 使用 SCSS 地图作为对象-好吗？

> 原文：<https://dev.to/nicolalc/using-scss-maps-as-object-is-it-good-1lj0>

大家好！

在我最近的项目中，我更喜欢使用 SCSS [地图](https://sass-lang.com/documentation/values/maps)而不是简单的 SCSS 变量。

例如:

```
$fontSizes: (
  extraSmall: .75rem,
  small: .9rem,
  normal: 1rem,
  medium: 1.25rem,
  big: 1.5rem,
  extraBig: 2rem
); 
```

所以我可以在我的样式表中得到这些值，如下:

```
font-size: map_get($fontSizes, extraSmall); 
```

我只是**喜欢**这种方法，我已经将我所有的 SCSS 变量(不包括像 **$globalBorderRadius** 这样的原子变量)转换成地图，所以我可以将它们分组到一组特定的 scss 变量中。

你觉得 SCSS 地图怎么样？
你用过吗？如果是，这是使用它们的好方法吗？

让我知道！:)