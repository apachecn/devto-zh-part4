# 如何为 flickr 相册创建一个 WordPress 短代码

> 原文：<https://dev.to/jimfrenette/how-to-create-a-wordpress-shortcode-for-flickr-albums-4mlp>

如何创建一个 WordPress 自定义短代码来显示来自 flickr 的照片。这篇文章记录了使用 [Slick Lazy Load Photo Grid](https://jimfrenette.com/2017/12/slick-lazy-load-photo-grid-webpack-3/) 和 flickr API 在 Wordpress 文章中任何输入短代码的地方渲染相册。 [Webpack](https://webpack.js.org/) 4、 [autoprefixer](https://github.com/postcss/autoprefixer) 和 [babel](https://babeljs.io/) 包含在内，用于构建 JS 和 CSS。

#### 要求

*   [flickr API](https://www.flickr.com/services/api/) 键。从他们的 API 页面中，选择“创建应用程序”链接，了解有关如何请求 API 密钥的更多信息。
*   [Node.js](https://nodejs.org) 与 NPM

*   如果你正在寻找一个本地的 Wordpress 开发环境，我建议尝试一下 [docker4wordpress](https://github.com/wodby/docker4wordpress) ，我已经在我的文章《使用 Docker 从开发到生产的[中提到过。](https://jimfrenette.com/2018/05/wordpress-from-development-to-production-using-docker/)

### 短码

创建一个定制的短代码是通过添加 PHP 代码到主题`functions.php`文件来完成的。我更喜欢把我的 custon WordPress 函数放在单独的文件中，而不是把它们都放在现有的`functions.php`文件中。为此，创建一个`functions`文件夹，并向其中添加一个新的`slickflickr.php`文件。

将下面的`php`代码添加到`slickflickr.php`文件中，以注册自定义`shortcode_handler`功能。确保用您的 [flickr API](https://www.flickr.com/services/api/) 键替换示例`$api_key`值。

##### slickflickr.php

```
<?php
/**
 * Slick Flickr Album Shortcode
 *
 * @param array [photoset_id
 * example
 * [slickflickrshortcode photoset_id=72157685387149525]
 *
 * @return string Unordered list of images for lazy loading slick photo gallery
 *
 * url format reference:
 * https://www.flickr.com/services/api/misc.urls.html
 */
function slickflickr_shortcode_handler($args) {

    $xml = slickflickr_get_photoset($args['photoset_id']);

    ob_start();
  ?>
  <ul class="photogrid">
  <?php
    foreach($xml->photoset[0]->photo as $photo) {
        echo '<li><img data-src="https://farm' . $photo['farm'] . '.staticflickr.com/' . $photo['server'] . '/' . $photo['id'] . '_' . $photo['secret'] . '_b.jpg" alt="' . $photo['title'] . '" /></li>';
    }
    ?>
    </ul>
    <?php
    return ob_get_clean();
}
add_shortcode( 'slickflickrshortcode',  'slickflickr_shortcode_handler' );

function slickflickr_curl($url) {
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    $data = curl_exec($ch); // response
    curl_close($ch);
    return $data;
}

function slickflickr_get_photoset($photoset_id) {
    $results = null;
    $api_key = '1234a567bc89d101ef1ghij121314klm';
    $url = 'https://api.flickr.com/services/rest/?method=flickr.photosets.getPhotos&api_key=' . $api_key . '&photoset_id=' . $photoset_id . '&format=rest';
    $xml = new \SimpleXmlElement(slickflickr_curl($url));
    return $xml;
} 
```

参考: [WordPress Shortcode API](https://codex.wordpress.org/Shortcode_API)

在`functions.php`的底部或底部附近，添加这个 [require](http://php.net/manual/en/function.require.php) 语句，以便在主题中包含定制的短代码处理程序。

##### functions.php

```
...

/**
* Slick flickr Shortcode Handler
*/
require get_parent_theme_file_path( '/functions/slickflickr.php' ); 
```

> 请注意，本教程中的代码片段中的省略号`...`并不是实际代码的一部分，只是用来表示被跳过的代码，不适用于本例。

通过在帖子中输入短代码文本来测试自定义短代码。比如`[slickflickrshortcode photoset_id=72157685387149525]`。当您`view-source`这篇文章时，您应该会看到一个带有`img`元素的无序列表。flickr 相册图片的 URL 被添加到`img`元素的`data-src`属性中，用于延迟加载。样式和图片的延迟加载是由 CSS 和 JavaScript 代码处理的，我们将在下一节中把它们添加到 WordPress 主题中。

* * *

### 主题

对于前端，我们需要将 JavaScript 和 CSS 的 UI 构建过程引入到 WordPress 主题中。在本教程中，我将使用 WordPress 附带的 217 主题的副本以及我的[217 主题 Sass](https://github.com/jimfrenette/twentyseventeen-sass) 。如果你想要一个完全空白的石板，你可以使用我写的关于[的主题。](https://jimfrenette.com/2018/08/completely-blank-no-css-_s-wordpress-starter-theme)

下载或克隆[第 27 个主题 Sass](https://github.com/jimfrenette/twentyseventeen-sass) 到`mytwentyseventeen`文件夹。如果你有一个现有的主题，从 github 下载 zip 文件，解压并复制`src`文件夹到你的主题中。

```
cd wp-content/themes

git clone https://github.com/jimfrenette/twentyseventeen-sass.git mytwentyseventeen 
```

将所有的`twentyseventeen`主题文件夹和文件复制到新的`mytwentyseventeen`主题中。

```
cd wp-content/themes

cp -r twentyseventeen/ mytwentyseventeen/ 
```

在 Windows 上？我写过一些 Unix 类的命令行界面，包括 WSL ubuntu zsh nvm 等等。，还有[小天鹅哦我的 ZSH 食谱](https://jimfrenette.com/2017/02/cygwin-oh-my-zsh-recipe/)。

### UI 构建

因为我们不使用 Gulp，所以删除`gulpfile.js`和`package.json`。

在新主题中创建这些文件。我们稍后会编辑它们。举个例子，

```
cd wp-content/themes/mytwentyseventeen

# create the .babelrc file
touch .babelrc

# create a new package.json file using npm
npm init

# create the postcss config
touch postcss.config.js 
```

#### NPM 套餐

使用 Webpack 4 安装 NPM 包来传输 Javascript 和 Sass。

```
cd wp-content/themes/mytwentyseventeen

npm i webpack webpack-cli webpack-merge --save-dev

npm i autoprefixer --save-dev

npm i babel-loader @babel/core @babel/preset-env --save-dev

npm i css-loader style-loader --save-dev

npm i mini-css-extract-plugin --save-dev

npm i node-sass sass-loader --save-dev

npm i optimize-css-assets-webpack-plugin --save-dev

npm i postcss-loader --save-dev

npm i resolve-url-loader url-loader --save-dev

npm i uglifyjs-webpack-plugin --save-dev 
```

#### 网页包 4 配置

用三个`config.js`文件创建一个 webpack 文件夹。

```
cd wp-content/themes/mytwentyseventeen

mkdir webpack

cd webpack

touch base.config.js

touch dev.config.js

touch prod.config.js 
```

`base.config.js`文件包含一个对开发人员和生产人员通用的配置。

##### base.config.js

```
const path = require('path');
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  context: path.resolve(__dirname, '../src'),
  entry: {
    app: './js/index.js'
  },
  output: {
    path: path.resolve(__dirname, '../dist')
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "../style.css",
    })
  ],
  externals: {
    // require("jquery") is external and available
    // on the global var jQuery
    "jquery": "jQuery"
  },
  module: {
    rules: [
      {
        test: /\.(css|scss)$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          {
            loader: 'postcss-loader' },
          {
            /* for ~slick-carousel/slick/slick-theme.scss */
            loader: 'resolve-url-loader' },
          {
            /* for resolve-url-loader:
                source maps must be enabled on any preceding loader */
            loader: 'sass-loader?sourceMap' }
        ]
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: 'babel-loader'
      },
      { /* for ~slick-carousel/slick/slick-theme.scss */
        test: /\.(eot|woff|woff2|ttf|svg|png|jpg|gif)$/,
        loader: 'url-loader?limit=30000&name=[name]-[hash].[ext]'
      }
    ]
  }
} 
```

`dev.config.js`文件只包含开发专用的配置设置。`base.config.js`文件中的配置设置通过`webpack-merge`与这个开发配置相结合。

##### [T1】dev . config . js](#devconfigjs)

```
const merge = require('webpack-merge');
const baseConfig = require('./base.config.js');

module.exports = merge(baseConfig, {
  devtool: 'eval-source-map',
}); 
```

`prod.config.js`文件仅包含专门针对生产版本的配置设置。`base.config.js`文件中的配置设置也通过`webpack-merge`与该配置相结合。

##### 产品配置. js

```
const merge = require('webpack-merge');
const baseConfig = require('./base.config.js');
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");

module.exports = merge(baseConfig, {
  optimization: {
    minimizer: [
      new OptimizeCSSAssetsPlugin({})
    ]
  },
  plugins: [
    new UglifyJsPlugin({
      uglifyOptions: { output: { comments: false } }
    })
  ]
}); 
```

将`presets`属性添加到`.babelrc`配置文件。

##### .babelrc

```
{
  "presets": ["@babel/preset-env"]
} 
```

添加这个`plugins`属性，要求将 autoprefixer postcss 插件添加到`postcss.config.js`配置文件中。

##### postcss.config.js

```
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
} 
```

给`package.json`文件添加一个`browserslist`属性，通知我们的 transpilers 支持哪些浏览器。在这里，我们的目标是根据全球使用统计超过 2%的浏览器和大于 10 的 Internet Explorer 版本。

##### package.json

```
{
  "name": "mytwentyseventeen",
  "version": "1.0.0",
  "description": "WordPress Twenty Seventeen Theme",
  "main": "index.js",
  "browserslist": [
    "> 2%",
    "last 2 versions",
    "ie > 10"
  ],

  ...
} 
```

在`src`文件夹中，创建一个包含导入 Sass 的`index.js`入口点的`js`文件夹。

##### index.js

```
import style from '../sass/style.scss' 
```

#### [NPM-运行-脚本](https://docs.npmjs.com/cli/run-script)

在`package.json`文件中，为运行 webpack 的`scripts`对象添加`dev`和`prod`属性。

##### package.json

```
 ...

  "scripts": {
    "dev": "webpack --mode=development --watch --progress --colors --config webpack/dev.config.js",
    "build": "webpack --mode=production --progress --hide-modules --config webpack/prod.config.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },

  ...
} 
```

### 懒加载相册图片

这种技术推迟了图像的下载，使得页面加载速度更快。

在`src/js`内部，创建一个`lazyimage.js`模块文件。

##### lazyimage.js

```
export default class Lazyimage {
  constructor(options) {

      this.init();
  }

  init() {

      [].forEach.call(document.querySelectorAll('img[data-src]'), function(img) {
          img.setAttribute('src', img.getAttribute('data-src'));
          img.onload = function() {
              img.removeAttribute('data-src');
          };
      });
  }

} 
```

要从模块中导入`Lazyimage`类，请将这段代码添加到`src/js/index.js`文件中。

##### index.js

```
...

import Lazyimage from './lazyimage'

new Lazyimage(); 
```

在`src/sass`文件夹中，创建一个`_photogrid.scss`和一个`_lazyimage.scss`文件。Sass 部分文件名有前导下划线，表示它们仅在导入时才被处理。

##### _photogrid.scss

```
ul.photogrid {
  margin: 0.5vw 0.5vw 0.5vw -0.5vw;
  font-size: 0;
  flex-flow: row wrap;
  display: flex;

  li {
    flex: auto;
    width: 200px;
    margin: 0.5vw;
  }
} 
```

##### _lazyimage.scss

```
img {
  opacity: 1;
  transition: opacity 0.3s;
}

img[data-src] {
  opacity: 0;
} 
```

更新`src/sass/style.scss`以导入 Sass 部分。

##### style.scss

```
...

@import "photogrid";
@import "lazyimage"; 
```

#### 运行构建

等一下，我们需要链接将要在主题页脚中构建的`dist/app.js`。在结束的`body`标签前添加一个脚本元素。

##### footer.php

```
...

<script async src="/wp-content/themes/mytwentyseventeen/dist/app.js"></script>

</body>
</html> 
```

在转移到 lightbox 和 Slick slider Sass 和 JavaScript 代码之前，运行构建以验证所有东西都已设计好并且工作正常。

```
npm run build 
```

如果清除缓存后一切正常，运行开发构建来观察变化，并在添加剩余的 lightbox 和 slider 代码时进行增量构建。

```
npm run dev 
```

### 光滑的滑块灯箱

安装[滑块](http://kenwheeler.github.io/slick/)

```
npm i slick-carousel --save 
```

在`src/js`内部，创建一个`lightbox.js`模块文件。

##### lightbox.js

```
import $ from 'jquery'
import 'slick-carousel'

export default class Lightbox {
  constructor(options) {
    this.settings = $.extend({/* defaults */}, options);
    this.init();
  }

  init() {
    let source = $(this.settings.source);

    if (source.length) {
      source.each((index, el) => {
        this.create(index, el);
      });
    }
  }

  create(index, el) {
    let lightbox = this.settings.name + '__' + index,
    opener = $(el).find(this.settings.opener);

    $('body').append('<div data-lightbox="' + lightbox + '" class="lightbox"><div></div></div>');
    if (this.settings.type === 'slider') {
      $('div[data-lightbox="' + lightbox + '"] > div')
        .append('<div class="lightbox-slider"></div>');

      var slider = $('div[data-lightbox="' + lightbox + '"] .lightbox-slider');
      slider.slick({
        dots: true
      });

      opener.each((index, el) => {
        this.popSlider(lightbox, slider, el);
      });
    }

    // close button
    $('div[data-lightbox="' + lightbox + '"] > div')
      .prepend('<a class="lightbox-close" href="javascript:void(0)">+</a>');

    $('.lightbox-close').on( 'click', function() {
      $('[data-lightbox="' + lightbox + '"]').removeClass('is-open');
    });

    //close on outside click
    window.onclick = function(evt) {
      if (evt.target.dataset.lightbox == lightbox) {
        $('[data-lightbox="' + lightbox + '"]').removeClass('is-open');
      }
    }

    // close on escape
    $(document).keyup(function(evt) {
      if (evt.which === 27) {
        $('[data-lightbox="' + lightbox + '"]').removeClass('is-open');
      }
    });
  }

  popSlider(lightbox, slider, el) {
    let img = $(el).find('img'),
        src = img.prop('src'),
        slide = document.createElement('div'),
        slideImg = document.createElement('img');

    slideImg.src = src;
    slide.appendChild(slideImg);

    if (img.attr('alt')) {
      let caption = document.createElement('p'),
      captionText = document.createTextNode(img.attr('alt'));
      caption.appendChild(captionText);
      slide.appendChild(caption);
    }

    slider.slick('slickAdd', slide);

    img.wrap('<a href="' + src + '"></a>').on( 'click', function(evt) {
      evt.preventDefault();
      $('[data-lightbox="' + lightbox + '"]').addClass('is-open');
      let index = $(this).closest(el).index();
      slider.slick('slickGoTo', index);
    });
  }
} 
```

更新`src/js/index.js`以从模块中导入`Lightbox`类。

##### index.js

```
import style from '../sass/style.scss'
import Lazyimage from './lazyimage'
import Lightbox from './lightbox'

new Lazyimage();

new Lightbox({
  name: 'lightbox',
  source: '.photogrid',
  opener: 'li',
  type: 'slider'
}); 
```

在`src/sass`文件夹中，创建一个`_lightbox.scss`

##### _lightbox.scss

```
/* overlay (background) */
.lightbox {
  max-height: 0; /* instead of `display: none` so slider can init properly. */

  position: fixed; /* stay in place */
  z-index: 9;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto; /* enable scroll if needed */
  background-color: rgb(0,0,0); /* fallback color */
  background-color: rgba(0,0,0,0.80); /* black w/ opacity */

  > div {
    position: relative;
    background-color: rgb(0,0,0);
    padding: 20px;
    color: #fff;
    //width: 100%; /* could be more or less, depending on screen size */
    width: 90vw;
    margin: 5vw auto;

    .slick-prev,
    .slick-next {
      z-index: 10;
    }
    .slick-prev {
      left: -20px;
    }
    .slick-next {
      right: -20px;
    }
    .slick-dots {
      li button:before {
        color: #fff;
      }
    }
  }

  &.is-open {
    max-height: 100%; /* unhide */
  }

  @media only screen and (max-width: 600px) {
    background-color: rgba(0,0,0,0.95); /* black w/ opacity */

    > div {
      padding: 40px 0 20px 0;
      width: 100vw;
      margin: 0 auto;

      .slick-slide p {
        padding: 0 15px;
      }

      .slick-next, .slick-prev {
        display: none;
      }
    }
  }

  @media only screen and (min-width: 1025px) {
    > div {
      max-width: 1024px;
    }
  }
}

/* close button */
.lightbox-close {
  position: absolute;
  top: 2px;
  right: 2px;
  text-align: center;
  line-height: 20px;
  font-size: 20px;
  font-weight: bold;
  color: rgba(255,255,255,0.75);
  width: 20px;
  height: 20px;
  transform: rotate(45deg);
  text-decoration: none;
  z-index: 10;

  &:hover {
    color: rgb(255,255,255);
  }

  @media only screen and (max-width: 600px) {
    top: 4px;
    right: 4px;
    line-height: 32px;
    font-size: 32px;
    width: 32px;
    height: 32px;
  }
} 
```

更新`src/sass/style.scss`添加了`lightbox`和 slick-carousel 导入。

##### style.scss

```
...

@import "photogrid";
@import "lazyimage";
@import "lightbox";

/* node_modules */
@import "~slick-carousel/slick/slick.scss";
@import "~slick-carousel/slick/slick-theme.scss"; 
```

要获得已经创建或更新的源文件的完整列表，我建议您查看 GitHub 上的[源代码库](https://github.com/jimfrenette/slickflickrshortcode)。

##### *[源代码](https://github.com/jimfrenette/slickflickrshortcode)*

 *最初发布于[jimfrenette . com/2018/11/how-to-create-WordPress-short code-for-Flickr-albums](https://jimfrenette.com/2018/11/how-to-create-wordpress-shortcode-for-flickr-albums/)*