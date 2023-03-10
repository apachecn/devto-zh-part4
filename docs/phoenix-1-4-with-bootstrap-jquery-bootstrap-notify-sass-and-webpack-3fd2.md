# Phoenix 1.4，带有 Bootstrap、jQuery、Bootstrap Notify、SASS 和 Webpack

> 原文：<https://dev.to/sophiabrandt/phoenix-1-4-with-bootstrap-jquery-bootstrap-notify-sass-and-webpack-3fd2>

我目前正在通过 Shankar Dhanasekrann 的 **[掌握凤凰框架](https://shankardevy.com/phoenix-book/)** 。

这本书教你如何使用 Phoenix 构建一个测试驱动开发的商店应用程序。

但是代码用的是凤凰 1.3。

Phoenix 1.3 仍然使用 [brunch](https://brunch.io/) 进行 JavaScript 资产生成。该框架附带了 CSS 的 Bootstrap。

在 [v1.4 中，作者用 webpack](https://phoenixframework.org/blog/upgrading-from-13x-to-140) 代替了早午餐。而现在 Phoenix 用的是[毫克](https://milligram.io/)，一个极简的 CSS 框架，而不是重量级的 Bootstrap。

但是如果要用 **Bootstrap，SASS，jquery** 和 **jquery 插件**像 **[Bootstrap Notify](http://bootstrap-notify.remabledesigns.com/)** 呢？

Abdulhakim Haliru 写了一篇关于如何安装 Bootstrap 等的好博文。。

如果你也想使用 jquery 插件和 SASS 一些额外的步骤是必要的。

# 安装 NPM 插件

导航到 Phoenix 项目并找到`assets`文件夹。

```
$ cd assets
$ npm install --save font-awesome bootstrap bootstrap4-notify jquery
$ npm install --save-dev file-loader sass-loader@7.3.1 node-sass 
```

Enter fullscreen mode Exit fullscreen mode

给你的网页添加“咆哮般”的通知。比起弹出窗口，它们看起来更好。

我在使用最新版本的`sass-loader`时遇到了问题，但 7.3.1 版本可以正常工作。

如果你不想使用`bootstrap4-notify`，你可以**使用`popper.js`进行 Bootstrap 的“原生”弹出** :

```
$ cd assets
$ npm install --save font-awesome bootstrap popper.js jquery
$ npm install --save-dev file-loader sass-loader@7.3.1 node-sass 
```

Enter fullscreen mode Exit fullscreen mode

# 网页包配置

将此添加到您的`assets`文件夹中的`webpack.config.js`:

```
// at the top of the file where your import statements are
//...
const webpack = require('webpack')

// inside the `module.exports` object
module.exports = (env, options) => ({
  // ....
  module: {
    rules: [
      {
        test: /\.(scss)$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader'], // needed for SCSS
      },
      {
        test: /\.(woff(2)?|ttf|eot|svg)(\?v=\d+\.\d+\.\d+)?$/, // needed for font-awesome
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[ext]',
              outputPath: '../fonts',
            },
          },
        ],
      },
    ],
  },
  plugins: [
    new webpack.ProvidePlugin({
      // add jquery
      $: 'jquery',
      jQuery: 'jquery',
    }),
  ],
}) 
```

Enter fullscreen mode Exit fullscreen mode

字体 Awesome 需要`file-loader`，添加 jquery 支持需要`webpack.ProvidePlugin`。

这里是我的 [webpack.config.js](https://github.com/sophiabrandt/mango/blob/master/assets/webpack.config.js) 的链接。

# 给 CSS 添加自举和字体 Awesome

在`assets/css`下创建`app.scss`文件(删除`app.css` ):

```
$fa-font-path: '~font-awesome/fonts';
@import '~bootstrap/scss/bootstrap.scss';
@import '~font-awesome/scss/font-awesome.scss';

/* Phoenix flash messages */
.alert:empty {
  display: none;
}

/* Phoenix inline forms in links and buttons */
form.link,
form.button {
  display: inline;
} 
```

Enter fullscreen mode Exit fullscreen mode

在官方网站上阅读更多关于如何将[字体 Awesome 与萨斯/SCSS 配合使用的信息。](https://fontawesome.com/how-to-use/on-the-web/using-with/sass)

# 给 JavaScript 添加 jquery 和 jquery 插件

这是一个在购物车中添加商品时使用 Bootstrap Notify 的示例。

记下文件顶部的导入。

文件是`assets/js`下的`app.js`:

```
import 'bootstrap'
import 'bootstrap4-notify'
import $ from 'jquery'
import css from '../css/app.scss'
import '../../deps/phoenix_html'

window.jQuery = $
window.$ = $

function ajaxHandler(e) {
  e.preventDefault()
  var post_url = $(this).attr('action') //get form action url
  var form_data = $(this).serialize() //Encode form elements for submission

  $.post(post_url, form_data, function(response) {
    // use $.notify for Bootstrap-Notify
    $.notify({
      message: response.message,
      offset: 60,
      type: 'success',
    })
    $('.cart-count').text(response.cart_count)
  })
}

var ajaxCart = {
  init: function() {
    $(function() {
      $('.cart-form').on('submit', ajaxHandler)
    })
  },
}

// init ajax
ajaxCart.init() 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步阅读

*   [掌握凤凰框架](https://shankardevy.com/phoenix-book/)
*   [用 Phoenix 设置引导程序](https://github.com/Plangora/phoenix_bootstrap)
*   [如何在 Phoenix 1.4 项目上安装 Bootstrap](https://medium.com/wecode-ng/how-to-install-bootstrap-on-phoenix-1-4-project-c8aa724dcdeb)
*   [Elixirforum:给 Phoenix 1.4 项目添加 Bootstrap 和 Jquery](https://elixirforum.com/t/add-bootstrap-and-jquery-to-phoenix-1-4-project/22498)
*   [如何用 Bootstrap 4 安装 popper.js？](https://stackoverflow.com/questions/47039812/how-to-install-popper-js-with-bootstrap-4#47470007)