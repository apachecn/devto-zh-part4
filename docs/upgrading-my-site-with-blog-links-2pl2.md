# 用博客链接升级我的网站

> 原文：<https://dev.to/andrewsosa/upgrading-my-site-with-blog-links-2pl2>

直到现在，我的网站( [andrewsosa.dev](https://andrewsosa.dev) )只是一些 Github 项目链接，但是现在，借助 [gatsby-source-dev](https://github.com/geocine/gatsby-source-dev) 的力量，我们现在也有了一些博客链接。

喔，博客链接！

#### *必修教程部分*

##### 1。你安装插件

```
npm i gatsby-source-dev 
```

##### 2。您添加配置

```
module.exports = {
  // ...
  plugins: [
    {
      resolve: `gatsby-source-dev`,
      options: {
        // This is your username on Dev.to
        username: `andrewsosa`,
      },
    },
   // ...
  ],
}; 
```

##### 3。*未完待续*