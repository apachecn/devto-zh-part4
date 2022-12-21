# 使用 Nginx/Apache/Firebase 和 Angular CLI 的无痛角度 SSR

> 原文：<https://dev.to/daudr/painless-angular-ssr-with-nginx-apache-firebase-angular-cli-4jno>

这篇文章之前已经发表在我的主博客上

因此，你正在建立一个新的、变幻莫测的网站(比方说一个全新的电子商务，针对那些你认为写得很漂亮的完全不知名的书籍)，你需要你未来的客户在谷歌的第一个搜索页面上找到它。

[![Serve your Angular app right](img/07e0458db8e7488c2ff2fa9da8bcfd29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GKKJYQkA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1544986581-efac024faf62%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1950%26q%3D80)

> [Alev Takil](https://unsplash.com/@alevtakil) 在 [Unsplash](https://unsplash.com) 上拍摄的照片

经过大量研究后，你决定使用 Angular 作为 FE 框架，因为你认为它最适合电子商务，但当你最终将它部署到生产中时，你发现即使已经发布了一段时间，也找不到它，这是怎么回事？

谷歌最近更新了它的爬虫，以预呈现 javascript 单页应用程序，如 Angular 应用程序，但这项技术并不总是工作得很好(你需要知道，不是每个人都使用谷歌上网)。

## 棱角万能来救援

幸运的是，Angular 团队建立了一个很好的(在一些最初的问题之后)工具来帮助我们，这个工具叫做 [Angular Universal](https://angular.io/guide/universal) 。

### 设置事物

设置 Angular Universal up 相对容易，你只需在终端中键入一个命令

`ng add @nguniversal/express-engine --clientProject "your project that neeeds SSR"`

这个小命令会在您的`src/`文件夹中生成一些文件:

*   `main.server.ts` -服务器应用的引导程序
*   `app/app.server.module.ts` -服务器端 app 模块
*   `server.ts` -快速网络服务器
*   `ts.config.server.json` -为您的服务器应用程序键入脚本配置
*   `webpack.server.config.js` - Webpack 服务器配置

### 验证 SSR 应用程序是否工作

要验证你的全新服务器端渲染应用是否可以工作，请在你的终端中键入

```
npm run build:ssr
npm run serve:ssr 
```

如果一切正常(你真的很幸运)你应该看到应用程序在`localhost:4000`上工作，否则继续阅读了解一些错误捕捉。

## 捕捉 SSR 错误

在尝试构建我们的 SSR 应用程序时，可能会出现大量错误，让我们尝试了解最常见的错误。

### 仅使用浏览器命名空间

当应用程序在我们的服务器上运行时，有一些我们无法使用的标准对象，即`window`，因此我们可以做两件事:

1.  注入虚假的对象来“欺骗”服务器，让我们的应用程序甚至可以在服务器端工作，要做到这一点，只需在`server.ts`文件中的`express()`初始化之前添加这些行:

    ```
    ...
    const domino = require('domino');
    const fs = require('fs');
    const path = require('path');
    const templateA = fs.readFileSync(path.join('dist/browser', 'index.html')).toString();
    const win = domino.createWindow(templateA);
    win.Object = Object;
    win.Math = Math;
    global['window'] = win;
    global['document'] = win.document;
    global['branch'] = null;
    global['object'] = win.object;
    const app = express();
    ... 
    ```

2.  检测您使用这些对象的部分，并使它们仅在您的浏览器应用程序中运行。
    在这种情况下 Angular 为我们提供了`@angular/common`暴露的两种方法`isPlatformBrowser`和`isPlatformServer`。
    这里有一个小例子:

    ```
    import { Component, Inject, PLATFORM_ID } from '@angular/core';
    import { isPlatformBrowser } from '@angular/common';

    @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
        constructor(@Inject(PLATFORM_ID) private platformId: any) {}

        onActivate(event: any) {
            if (isPlatformBrowser(this.platformId)) {
                const scrollToTop = window.setInterval(() => {
                    const pos = window.pageYOffset;
                    if (pos > 0) {
                        window.scrollTo(0, pos - 50);
                    } else {
                        window.clearInterval(scrollToTop);
                    }
                }, 16);
            }
        }
    } 
    ```

    仅当此代码在浏览器上运行时，此代码片段才运行 scrollTop。

## 完成这一切:部署到生产

最精彩的部分来了，我将带您完成服务器配置，这不是很棒吗？

### 阿帕奇配置

如果您使用 Apache 作为 web 服务器，您需要创建或修改您的`.htaccess`文件:

```
<IfModule mod_rewrite.c>
 RewriteEngine On
 # If an existing asset or directory is requested go to it as it is
 RewriteCond %{DOCUMENT_ROOT}%{REQUEST_URI} -f [OR]
 RewriteCond %{DOCUMENT_ROOT}%{REQUEST_URI} -d
 RewriteRule ^ - [L]

 RewriteRule ^ /dist/browser/index.html
</IfModule>
<VirtualHost *:80>
 ServerName example.com # <-- modify here
 ServerAlias www.example.com # <-- modiy here
 <Proxy *>
  Order allow,deny
  Allow from all
 </Proxy>
 ProxyPreserveHost On
 ProxyRequests Off
 ProxyPass / http://localhost:4000/
 ProxyPassReverse / http://localhost:4000/
</VirtualHost>

<VirtualHost *:443>
 ServerName example.com # <-- modify here
 ServerAlias www.example.com # <-- modify here
 <Proxy *>
  Order allow,deny
  Allow from all
 </Proxy>
 ProxyPreserveHost On
 ProxyRequests Off
 ProxyPass / https://localhost:4000/
 ProxyPassReverse / https://localhost:4000/
</VirtualHost> 
```

### Nginx 配置

如果您使用 Nginx，您需要在`/etc/nginx/sites-available/www.example.com`文件下配置配置:

```
upstream your_upstream_config {
 server 127.0.0.1:4000;
}

server {
 listen 443 ssl http2;
 server_name www.example.com; # <--- modify here
 root /home/"your user"/path/to/dist/browser;
 location / {
  try_files $uri $uri @backend;
 }

 location @backend {
  proxy_pass http://your_upstream_config;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header Host $http_host;
  proxy_http_version 1.1;
  proxy_set_header X-NginX-Proxy true;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  proxy_cache_bypass $http_upgrade;
  proxy_redirect off;
  proxy_set_header X-Forwarded-Proto $scheme;
 }
}

server {
 listen 80;
 server_name www.example.com # <-- modify here
 return 301 https://$server_name$request_uri?;
} 
```

## 奖励-火基配置🤩🤩🤩

如果你正在使用 Firebase 托管，我们必须添加一些东西，并且有两种方法可以完成你已经用 Apache 或 Nginx 完成的工作:AppEngine 或云函数。

### 聚合填充您的 firebase 项目

Firebase 使用的 Websockets 和 XHR 不包括在 Angular 中，我们需要填充。所以我们运行在我们的终端:

```
npm install ws xhr2 bufferutil utf-8-validate -D 
```

我们将这两行添加到我们的`server.ts`文件中:

```
(global as any).WebSocket = require('ws');
(global as any).XMLHttpRequest = require('xhr2');
... 
```

### 部署在 AppEngine 上

在项目的根位置添加一个`app.yaml`，并在下面的代码行添加一个

```
runtime: nodejs10 
```

然后，在你安装了 Google Cloud CLI 之后，在你的终端上运行:

```
gcloud app deploy 
```

我们还需要修改我们的`package.json` :
中的`start`脚本

```
//  ...  "scripts":  {  "start":  "npm run serve:ssr",  //  ...  },  //  ... 
```

仅此而已，你应该看到你的应用程序运行在谷歌云！👌

### 使用 Firebase 云功能

为了使用 Firebase 云功能部署我们的应用程序，我们需要初始化它并修改一些文件:

初始化 Firebase 运行

```
firebase init 
```

并选择`hosting`和`functions`

然后修改你的`funtions/package.json`文件:

```
{  "hosting":  {  //  ...  "rewrites":  [  {  "source":  "**",  "funtion":  "ssr"  }  ]  },  //  ...  } 
```

移除您在`server.ts` :
中的快速监听器

```
// Start up the Node server
// app.listen(PORT, () => {
//   console.log(`Node Express server listening on http://localhost:${PORT}`);
// }) 
```

更新你的`webpack.server.config.js` :

```
output: {
  // Puts the output at the root of the dist folder
  path: path.join(__dirname, 'dist'),
  library: 'app',
  libraryTarget: 'umd',
  filename: '[name].js',
}, 
```

确保重建您的应用程序运行`npm run build:ssr`

然后我们移动到我们的`functions`目录来安装`fs-extra`

```
cd functions
npm install fs-extra --save 
```

Firebase 函数需要访问我们构建的应用程序，所以让我们创建一个名为`cp-angular,js`的简单节点脚本，它将我们的应用程序复制到`functions`文件夹:

```
const fs = require('fs-extra');

(async() => {

    const src = '../dist';
    const copy = './dist';

    await fs.remove(copy);
    await fs.copy(src, copy);

})(); 
```

然后在我们的`functions/package.json`中，我们添加了一个`build`脚本:

```
{  "name":  "functions",  "engines":  {  "node":  "10"  },  "scripts":  {  "build":  "node cp-angular && tsc"  }  } 
```

然后我们终于可以编写我们的函数了，所以我们的`functions/index.js`文件看起来会像这样:

```
import * as functions from 'firebase-functions';
const universal = require(`${process.cwd()}/dist/server`).app;

export const ssr = functions.https.onRequest(universal); 
```

要验证一切正常，您可以使用这些命令:

```
cd functions
npm run build
firebase serve 
```

如果一切正常，就部署它:

```
firebase deploy 
```

## 搞定！😎

就这样，你现在是角宇宙的大师了！