# 每天发布代码行

> 原文：<https://dev.to/starbist/announcing-code-line-daily-4ch5>

上周我发布了我的副业项目:[代码行每日](https://cld.silvestar.codes)。

这是一个非常简单的项目:每天都会引入一行新的代码。这些行主要是前端、PHP 和 WordPress 代码。以下是我如何建立它！

## 幕后

为了构建这个项目，我使用了 [Starter 项目](https://starter.silvestar.codes)。这是我去年创建的一个基于 Gulp 的样板文件。它成长了很多，对于这个项目，我引入了 JSON 数据文件。

```
{  "list":  [  {  "line":  "all: unset",  "note":  "Resets all properties to default.",  "link":  "https://developer.mozilla.org/en-US/docs/Web/CSS/all",  "language":  "CSS",  "author":  "Silvestar",  "date":  "2019-08-01"  },  {  "line":  "overflow: initial",  "note":  "Sets overflow to default state.",  "link":  "https://css-tricks.com/almanac/properties/o/overflow/",  "language":  "CSS",  "author":  "Silvestar",  "date":  "2019-08-02"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

*[参见](https://github.com/maliMirkec/code-line-daily/blob/master/data/lines.json)的完整代码。*

下面是基本的 JSON 结构——一个对象项数组。 [gulp-data](https://www.npmjs.com/package/gulp-data) 插件用于包含、解析和显示主页、归档和作者页面上的数据。

```
- var list = locals.data.list ? locals.data.list.filter(item => new Date(item.date) < new Date()) : []; 
```

Enter fullscreen mode Exit fullscreen mode

*[参见](https://github.com/maliMirkec/code-line-daily/blob/master/src/html/_layout/variables.pug)的完整代码。*

为了只显示当前行而不显示未来行，我使用了带有日期条件的`filter`函数。这样，以后的行就不会显示了。另外，我已经设置了 Zapier 在每天早上触发一个新的构建。

```
- thisItem = list.slice(-1).pop()

h3= thisItem.line 
```

Enter fullscreen mode Exit fullscreen mode

*[参见](https://github.com/maliMirkec/code-line-daily/blob/master/src/html/index.pug)的完整代码。*

为了在主页上只显示一行，我从一组行中提取了最后一项。

```
ul
  each listValue, listIndex in list.reverse()
    li
      h3= listValue.line 
```

Enter fullscreen mode Exit fullscreen mode

*[参见](https://github.com/maliMirkec/code-line-daily/blob/master/src/html/archive.pug)的完整代码。*

为了显示行列表，我反转了数组，然后使用`each`循环遍历每一项。

该网站托管在 Netlify 上，由 Netlify CMS 提供支持，通过能够在浏览器上发布新行，使我的生活更加轻松。

## 结论

我很喜欢构建这个项目，对于如何重用代码，我也有一些其他的想法，但是现在我会把这些留给我自己。

我已经知道我不需要一个经典的页面生成器。我可以用我的吞咽式发电机。它的伟大之处在于它包含了许多现代技术和最佳实践，这为我节省了很多开发这个项目的时间。

如果你有令人兴奋的代码要分享，请随意[向网站贡献](https://cld.silvestar.codes/contribute)。Code Line Daily 是一个开源项目。