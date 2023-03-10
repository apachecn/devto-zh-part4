# 降低图像标题和替代文本

> 原文：<https://dev.to/stephencweiss/markdown-image-titles-and-alt-text-5fi1>

减价的简单性和威力给我留下了深刻的印象。

今天，在探索盖茨比评论图片的 API 时，我注意到了选项:`showCaptions`。 <sup>1</sup>

> 当标题属性不为空时，使用标题属性的内容为每个图像添加标题。如果 title 属性为空，而 alt 属性不为空，则使用它。将此选项设置为 true 以启用此行为。您也可以传递一个数组来指定标题应该使用哪个值——例如，传递`[‘alt’, ‘title’]`将首先使用 alt 属性，然后是标题。当设置为`true`时，与通过`[‘title’, ‘alt’]`相同。如果您只想使用标题(并省略有 alt 属性但没有标题的图像的标题)，请传递`[‘title’]`。

是“标题”的加入让我措手不及。我以前从未见过/用过那个。

我总是以:`![Alt](/path/to/img.jpg)`的格式导入我的图片。

然而，事实证明，在路径之后的*中可以传递第二个参数:标题。<sup>2</sup>T4】*

```
![Alt](/path/to/img.jpg “image title”) 
```

## 举例

我想看看这在博客中会是什么样子——所以，我试着在盖茨比的博客中添加下面的图片。

不出所料，我们在 HTML: [![](img/fb08dc4d95c0052f9ebc99687bacbc2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mr1h5cUd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/%26%26%26SFLOCALFILEPATH%26%26%26Screen%20Shot%202019-08-19%20at%206.17.19%20AM.png) 
中的`img`标签上看到了`alt`和`title`属性

```
<img class="gatsby-resp-image-image" src="/static/0006f3a2fe907f8d0b63c65e51983802/a111b/adam-solomon-hello.jpg" alt="hello!" title="adam solomon's hello"> 
```

这里是相关的`gatsby-config`——图片是作为`gatsby-plugin-mdx`选项的一部分的插件——这是我用来将 Markdown 编译成 HTML 的。

```
module.exports = {
  plugins: [
    ‘gatsby-plugin-sharp’,
    ‘gatsby-transformer-sharp’,
    {
      resolve: ‘gatsby-plugin-mdx’,
      options: {
        extensions: [‘.mdx’, ‘.md’],
        defaultLayouts: {
          default: require.resolve(‘./src/components/layout.js’),
        },
        gatsbyRemarkPlugins: [
          {
            resolve: ‘gatsby-remark-images’,
            options: {
              markdownCaptions: true,
              linkImagesToOriginal: false,
              showCaptions: [‘title’, ‘alt’],
              withWebp: true,
              tracedSVG: { color: ‘#F00’, turnPolicy: ‘TURNPOLICY_MAJORITY’ },
            },
          },
        ],
      },
    },
  ],
}; 
```

注意:`showCaptions`选项将只在文档体中显示标题*或* Alt。不是两者都有。不过，两者都存在于屏幕阅读器的 HTML 中。

对于额外的样式，我还发现 xaprb 的这篇博客文章内容丰富。 <sup>3</sup>

# 资源

*   <sup>1</sup> [评论图片|盖茨比](https://www.gatsbyjs.org/packages/gatsby-remark-images/)
*   <sup>2</sup> [Markdown 语法文档|胆大的火球](https://daringfireball.net/projects/markdown/syntax#img)
*   <sup>3</sup>