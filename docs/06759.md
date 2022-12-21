# 重新审视在 Heroku 上安装 Rails 6

> 原文：<https://dev.to/csexton/installing-rails-6-on-heroku-revisited-14ek>

在我的[原始帖子](///2019/08/02/rails-6-on-heroku.html)中，我概述了一个涉及添加 Heroku 构建包的解决方案。但是经过对 [`rails/webpacker`的讨论，](https://github.com/rails/webpacker/pull/2206)找到了更好的方法来处理这个错误。

所以如果你看到这个:

```
 Compilation failed:

 !
 ! Precompiling assets failed.
 !
 ! Push rejected, failed to compile Ruby app.
 ! Push failed 
```

我可以通过将 sass 包移动到生产依赖项而不是`devDependencies`来解决这个问题

我把两个 Sass 包移到了`dependencies`，它工作了:

```
 "dependencies": {
     "@rails/actioncable": "^6.0.0",
     "@rails/activestorage": "^6.0.0",
     "@rails/ujs": "^6.0.0",
     "@rails/webpacker": "^4.0.2",
     "turbolinks": "^5.2.0",
+ "node-sass": "^4.12.0",
+ "sass-loader": "^7.1.0"
   },
   "devDependencies": {
- "node-sass": "^4.12.0",
- "sass-loader": "^7.1.0",
     "webpack-dev-server": "^3.3.1"
   }, 
```

这最终成为一个更好的解决方案，因为:

1.  它不会重新运行纱线安装(这可能需要一段时间，[，即使它在后续运行中应该很快](https://github.com/rails/webpacker/pull/2206#issuecomment-519980678)
2.  使用我期望的库
3.  使用默认的 Heroku 构建包

感谢[布莱恩](https://github.com/bbugh)和[杰克](https://github.com/jakeNiemiec)帮我解决这个问题。对 NPM、网络包和朋友们来说，这仍是一个学习过程。