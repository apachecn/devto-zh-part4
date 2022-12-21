# JSSheet -一个 javascript 到 css 的解析器

> 原文：<https://dev.to/nicolalc/jssheet-a-javascript-to-css-parser-5alg>

大家好！

我在业余时间做一个名为 **jssheet** 的个人项目。

创建这个项目是为了学习如何使用 gulp，以及如何使用 javascript 创建一个定制的 css 预编译样式表。

你可以看看这里的实时演示:
[https://nicolalc.github.io/jssheet/](https://nicolalc.github.io/jssheet/)

目前这仍然是一个 WIP，但我认为这在将来会非常有用。

目标是:*我要用同一种语言写逻辑和风格*。

JSSheet 有这个用途，使用 JavaScript 进行样式和逻辑。

这是一个实现的例子:

```
const { fonts, layout, colors } = require("../variables");
const headersProps = {
    margin: layout.itemsMargin,
    color: colors.secondary,
    fontFamily: fonts.secondary,
}
const generics = [
    {
        selector: "h1",
        display: 'block',
        whiteSpace: 'pre-wrap',
        fontSize: fonts.sizes.h1,
        ...headersProps,
        transition: "all .1s ease-out",
        children: [
            {
                selector: ":hover",
                letterSpacing: `.5rem`,
                cursor: "default"
            }
        ]
    },
    {
        selector: "h2",
        fontSize: fonts.sizes.h2,
        ...headersProps
    },
    {
        selector: "h3",
        fontSize: fonts.sizes.h3,
        ...headersProps
    },
    {
        selector: "::-webkit-scrollbar",
        width: ".25rem",
        height: ".25rem",
    },
    {
        selector: "::-webkit-scrollbar-thumb",
        background: colors.tertiary
    },
    {
        selector: "p",
        fontSize: fonts.sizes.p,
        fontFamily: fonts.secondary,
        margin: layout.itemsMargin
    },
    {
        selector: "pre",
        background: colors.dark,
        color: "white",
        margin: 0,
        maxWidth: '95%',
        padding: layout.itemsPadding,
        borderRadius: layout.borderRadius,
        fontFamily: fonts.main,
        children: [
            {
                selector: "b",
                color: colors.secondary
            },
            {
                selector: "code",
                fontFamily: fonts.main,
                display: 'block',
                whiteSpace: 'pre-wrap'
            }
        ]
    },
    {
        selector: "ul",
        padding: 0,
        margin: 0,
        children: [
            {
            selector: "li",
            padding: 0,
            margin: 0
            }
        ]
    },
    {
        selector: 'a',
        color: colors.secondary,
        textDecoration: 'none'
    }
];

module.exports = {
    generics
}; 
```

正如你所看到的，使用 Javascript 我们可以为不同的选择器重用 css 的部分，或者定义全局共享变量，这些变量可以包括**静态**或**动态**值。

我在这个项目中添加的最酷的特性是**“align”**属性，用一行代码就可以创建复杂的 flexbox 布局。

例如下面:

```
[...]
align: "center column",
[...] 
```

会产生下面的 css:

```
[...]
display: flex;
align-items: center;
justify-content: center;
flex-direction: column;
[...] 
```

因此，这是一个很酷的捷径来处理 flexbox 布局和原型你的网站布局快！

**记住**，这是一个早期的 WIP 项目，所以它没有完成，文档也丢失了。

随意给出建议、提示和意见，如果你想合作，就叉这个 github:[https://github.com/NicolaLC/js-sheet](https://github.com/NicolaLC/js-sheet)