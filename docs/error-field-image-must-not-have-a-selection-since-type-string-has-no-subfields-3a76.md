# 错误:“字段“image”不能有选择，因为类型“String”没有子字段。”

> 原文：<https://dev.to/stephencweiss/error-field-image-must-not-have-a-selection-since-type-string-has-no-subfields-3a76>

我试图查询我放在帖子首页的一张图片时，出现了错误:

> 字段“image”不能有选择，因为类型“String”没有子字段

查询*应该*有效，因为在我的例子中`image`正在被夏普转变，所以*有了孩子。事实证明，理论上我是对的，但实际上我需要重新配置一些东西才能让它工作。*

我如何调试这个错误:

1.  重新配置`gatsby-config`，使`plugin-sharp`和`transformer-sharp`处于第一位。这应该是你需要做的全部，但是如果你在重建后仍然有问题，继续下去。
2.  删除`.cache`、`public`和`node_modules`
3.  重新安装(`npm i`)所有依赖项
4.  重建(`npm run develop`如果你用的是盖茨比启动器)

完成所有这些步骤后，我的查询非常有效，现在我可以访问转换后的图像的子属性了。

这是我的配置文件现在的样子:

```
module.exports = {
  siteMetadata: {
    title: ‘My site’,
    description: ‘A test site’,
  },
  plugins: [
    'gatsby-plugin-sharp’, 'gatsby-transformer-sharp', 'gatsby-plugin-emotion',
    ‘gatsby-plugin-react-helmet’,
    {
      resolve: ‘gatsby-plugin-mdx’,
      options: {
        extensions: [“.mdx”,”.md”],
        defaultLayouts: {
          default: require.resolve(‘./src/components/layout.js’),
        },
        gatsbyRemarkPlugins: [{ resolve: 'gatsby-remark-images' }],
        plugins: [{ resolve: 'gatsby-remark-images' }],
      },
    },
    {
        resolve: 'gatsby-source-filesystem',
        options: {
            name: 'posts',
            path: 'content/posts' }
    },
    {
        resolve: 'gatsby-source-filesystem',
        options: {
            name: 'blog',
            path: 'content/blog' }
    },
    {
        resolve: 'gatsby-source-filesystem',
        options: {
            name: 'images',
            path: 'content/images' }
    },
  ],
}; 
```

## 资源

我发现这两个资源对调试这个错误特别有帮助:

1.  由于类型“字符串”没有子字段，因此字段“图像”不能有选择。第 13469 期盖茨比 js/盖茨比 GitHub
2.  [盖茨比形象的问题及其解决方法](https://theleakycauldronblog.com/blog/problems-with-gatsby-image-and-their-workarounds/)