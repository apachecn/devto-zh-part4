# 使用 angular-cli 部署 i18n Angular 应用程序

> 原文：<https://dev.to/angular/deploying-an-i18n-angular-app-with-angular-cli-2fb9>

在本文中，我将解释如何使用 Angular CLI 从头开始创建一个国际化(i18n) Angular 应用程序，以及如何将其部署在 Apache 或 NGINX web 服务器上。

使用以下版本:

*   角度-cli: 8.0.4
*   角度:8.0.4
*   Apache 2.4
*   NGINX 1.17

所描述的示例应用程序可在:[https://github.com/feloy/angular-cli-i18n-sample](https://github.com/feloy/angular-cli-i18n-sample)获得

## 一个新鲜的 i18n app

我们首先借助 Angular CLI 创建一个全新的 Angular 应用:

```
$ ng new angular-cli-i18n-sample 
```

Enter fullscreen mode Exit fullscreen mode

我们做了一些修改，在
`app.component.html` :
中添加了一些可翻译的文本

```
<h1 i18n>Hello world!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要创建一个包含可翻译字符串的 xlf 文件。我们可以用下面的命令生成文件`src/i18n/messages.xlf`:

```
$ ng xi18n --output-path src/i18n 
```

Enter fullscreen mode Exit fullscreen mode

我们现在为不同的语言创建翻译，在这里用一个从`src/i18n/messages.xlf` :
复制来的新文件`src/i18n/messages.en.xlf`

```
[...]
      <trans-unit id="[...]" datatype="html">
        <source>Hello World!</source>
        <target>Hello World!</target>
        [...]
      </trans-unit>
[...] 
```

Enter fullscreen mode Exit fullscreen mode

法语中带有`src/i18n/messages.fr.xlf` :

```
[...]
      <trans-unit id="[...]" datatype="html">
        <source>Hello World!</source>
        <target>Salut la foule !</target>
        [...]
      </trans-unit>
[...] 
```

Enter fullscreen mode Exit fullscreen mode

在西班牙语中有`src/i18n/messages.es.xlf` :

```
[...]
      <trans-unit id="[...]" datatype="html">
        <source>Hello World!</source>
        <target>¿hola, qué tal?</target>
        [...]
      </trans-unit>
[...] 
```

Enter fullscreen mode Exit fullscreen mode

现在可以让 Angular CLI 使用您选择的语言构建应用程序，这里是西班牙语:

```
$ ng build --aot \
           --i18n-file=src/i18n/messages.es.xlf \
           --i18n-locale=es \
           --i18n-format=xlf 
```

Enter fullscreen mode Exit fullscreen mode

## 准备 app 投入生产

在生产中，我们希望应用程序可以在不同的子目录中访问，这取决于语言；例如，西班牙语版本将在 http://myapp.com/es/的[和 http://myapp.com/fr/的](http://myapp.com/es/)[分别获得。我们也希望从基本网址【http://myapp.com/ T4】](http://myapp.com/fr/)被重定向到我们首选语言的网址。

为此，我们猜测我们需要根据目标语言将基本 href 改为 **es** 、 **en** 或 **fr** 。Angular CLI 为此提供了一个特殊的命令行选项，`--base-href`允许在编译时从命令行声明基本 href。

### Linux/macOS 用户

下面是我们可以用来为不同语言创建不同包的 shell 命令:

```
$ for lang in es en fr; do \
    ng build --output-path=dist/$lang \
             --aot \
             --prod \
             --base-href /$lang/ \
             --i18n-file=src/i18n/messages.$lang.xlf \
             --i18n-format=xlf \
             --i18n-locale=$lang; \
  done 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 package.json 中为这个命令创建一个脚本定义，并使用 npm run build-i18n:
执行它

```
{
  [...]
  "scripts": {
    [...]
    "build-i18n": "for lang in en es fr; do ng build --output-path=dist/$lang --aot --prod --base-href /$lang/ --i18n-file=src/i18n/messages.$lang.xlf --i18n-format=xlf --i18n-locale=$lang; done"
  }
  [...]
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们将三个目录`en/`、`es/`和`fr/`放入`dist/`目录，包含不同的包。

### Windows 用户

作为 Windows 用户，您可以使用这些命令为不同的语言构建不同的包:

```
> ng build --output-path=dist/fr --aot --prod --base-href /fr/ --i18n-file=src/i18n/messages.fr.xlf --i18n-format=xlf --i18n-locale=fr

> ng build --output-path=dist/es --aot --prod --base-href /es/ --i18n-file=src/i18n/messages.es.xlf --i18n-format=xlf --i18n-locale=es

> ng build --output-path=dist/en --aot --prod --base-href /en/ --i18n-file=src/i18n/messages.en.xlf --i18n-format=xlf --i18n-locale=en 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在`package.json`中为这些命令创建脚本定义，并创建一个补充脚本来一次运行所有这些命令，并使用`npm run build-i18n` :
执行最后一个命令

```
"scripts": {
    "build-i18n:fr": "ng build --output-path=dist/fr --aot --prod --base-href /fr/ --i18n-file=src/i18n/messages.fr.xlf --i18n-format=xlf --i18n-locale=fr",
    "build-i18n:es": "ng build --output-path=dist/es --aot --prod --base-href /es/ --i18n-file=src/i18n/messages.es.xlf --i18n-format=xlf --i18n-locale=es",
    "build-i18n:en": "ng build --output-path=dist/en --aot --prod --base-href /en/ --i18n-file=src/i18n/messages.en.xlf --i18n-format=xlf --i18n-locale=en",
    "build-i18n": "npm run build-i18n:en && npm run build-i18n:es && npm run build-i18n:fr"
  } 
```

Enter fullscreen mode Exit fullscreen mode

## Apache2 配置

这里是一个虚拟主机配置，它将为您来自`/var/www`目录的不同包提供服务:您必须将之前生成的三个目录`en/`、`es/`和`fr/`复制到这个目录中。

使用这种配置，URL[http://www.myapp.com](http://www.myapp.com)被重定向到您的浏览器配置中定义的首选语言的子目录(或者`en`如果没有找到您的首选语言)，并且您仍然可以通过访问其他子目录来访问其他语言。

```
<VirtualHost *:80>
  ServerName www.myapp.com
  DocumentRoot /var/www
  <Directory "/var/www">
    RewriteEngine on
    RewriteBase /
    RewriteRule ^../index\.html$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule (..) $1/index.html [L]
    RewriteCond %{HTTP:Accept-Language} ^fr [NC]
    RewriteRule ^$ /fr/ [R]
    RewriteCond %{HTTP:Accept-Language} ^es [NC]
    RewriteRule ^$ /es/ [R]
    RewriteCond %{HTTP:Accept-Language} !^es [NC]
    RewriteCond %{HTTP:Accept-Language} !^fr [NC]
    RewriteRule ^$ /en/ [R]
  </Directory>
</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

## NGINX 配置

这里有一个 NGINX 配置，它会给你同样的行为:访问[http://www.myapp.com](http://www.myapp.com)会重定向到浏览器中定义的首选语言(或者`en`，如果你的首选语言没有找到)，其他语言仍然可以访问。

```
server {
    listen       80;
    server_name  localhost;

    location /en/ {
        alias   /usr/share/nginx/html/en/;
        try_files $uri$args $uri$args/ /en/index.html;
    }
    location /es/ {
        alias   /usr/share/nginx/html/es/;
        try_files $uri$args $uri$args/ /es/index.html;
    }
    location /fr/ {
        alias   /usr/share/nginx/html/fr/;
        try_files $uri$args $uri$args/ /fr/index.html;
    }

    set $first_language $http_accept_language;
    if ($http_accept_language ~* '^(.+?),') {
        set $first_language $1;
    }

    set $language_suffix 'en';
    if ($first_language ~* 'es') {
        set $language_suffix 'es';
    }
    if ($first_language ~* 'fr') {
        set $language_suffix 'fr';
    }

    location / {
        rewrite ^/$ http://localhost:4200/$language_suffix/index.html permanent;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励:添加不同语言的链接

这将是有趣的应用程序中有一些链接，所以用户可以通过点击这些链接导航到其他语言。链接将指向`/en/`、`/es/`和`/fr/`。

需要知道的一个技巧是，当前语言在`LOCALE_ID`标记中可用。

下面是如何获取`LOCALE_ID`值并显示语言列表，区分当前语言:

```
// app.component.ts
import { Component, LOCALE_ID, Inject } from '@angular/core';
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  languages = [
    { code: 'en', label: 'English'},
    { code: 'es', label: 'Español'},
    { code: 'fr', label: 'Français'}
  ];
  constructor(@Inject(LOCALE_ID) protected localeId: string) {}
}
<!-- app.component.html -->
<h1 i18n>Hello World!</h1>
<ng-template ngFor let-lang [ngForOf]="languages">
  <span *ngIf="lang.code !== localeId">
    <a href="/{{lang.code}}/">{{lang.label}}</a> </span>
  <span *ngIf="lang.code === localeId">{{lang.label}} </span>
</ng-template> 
```

Enter fullscreen mode Exit fullscreen mode

### 加成 2:角度翻译器应用

对于 2017 AngularAttack，我创建了一个应用程序，绝对可以帮助您翻译您的角度应用程序。它仍在开发中，欢迎反馈:[http://angular-translator.elol.fr/](http://angular-translator.elol.fr/)。

好翻译！