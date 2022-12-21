# Gatsby 配置插件语法

> 原文：<https://dev.to/stephencweiss/gatsby-config-plugin-syntax-584p>

我经常纠结于配置文件的语法。什么是解析器？期权是如何运作的？等等。

结果，当我在 Frontend Masters 上阅读杰森·伦斯托夫(Jason Lengstorf)对盖茨比的介绍时，我发现了以下有趣的内容:

在 Gatsby 项目根目录下的`gatsby-config.js`文件中，我有两个插件:

```
module.exports = {
    ...
  plugins: ['gatsby-plugin-emotion', 'gatsby-plugin-react-helmet']
  ...
} 
```

然而，这在功能上相当于:

```
module.exports = {
    ...
  plugins: [{resolve: 'gatsby-plugin-emotion' }, {resolve: 'gatsby-plugin-react-helmet'}]
  ...
} 
```

前者只是简写，因为对于那些，我不需要添加任何配置。如果我安装了一个插件，而*需要更多关于如何使用它的细节，会发生什么？这就是写`gatsby-plugin-emotion`是一个对象的速记这一事实开始变得重要的地方。*

 *以`gatsby-mdx`为例，我们可以在实践中看到:

```
module.exports = {
  ...
  plugins: [
    {
      resolve: 'gatsby-mdx',
      options: {
        defaultLayouts: {
          default: require.resolve('./src/components/layout.js'),
        },
      },
    },
  ],
  ...
}; 
```

这就是说，对于我加载的任何`mdx`文件，这些文件都将被加载到我在`src/components/layout.js`中定义的布局模板中。

还有一个问题是`gatsby-mdx`会接受什么选择。为此，文档在指定配置选项方面很有帮助。

资源:

*   [跟着杰森·伦斯托夫学盖茨比|前端大师](https://frontendmasters.com/courses/gatsby/)
*   [盖茨比-插件-mdx #配置| Github](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-plugin-mdx#configuration)*