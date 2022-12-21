# 通过 AngularCLI 使用离子

> 原文：<https://dev.to/studioteatwo/using-ionic-via-angularcli-1col>

# 简介

ionic 一直是使用 Angular 和 Cordova UI 框架。但最新版本是基于 webComponents 的，即使没有 ionic CLI，也可以在 react、vue 或 vanilla js 上获得。

太棒了。但是配置模式的数量增加了，我们可能会迷路。即使在角度的基础上，至少有 3 种模式可用。

1.  使用 ionic CLI(像往常一样)
2.  在脚本标签中包含`@ionic/core`并用作纯 WebComponent
3.  在 AngularCLI 项目中安装并使用`@ionic/angular`

在这篇文章中，我将讨论第三个问题。在将它合并为 NPM 包之后，它就像用通常的 ng 命令开发一样。

注:本文是 2019 年 6 月日本人写的翻译。

# 安装

先安装。这是安古拉克利创作这个项目的前提。

```
$ npm install @ionic/angular 
```

可以看到`ng add @ionic/angular`看似可以，但用 Anuglar v8 不行的描述。就我看到的代码而言，我认为赶上 angular.json 的模式变化是可以的。但我认为我们应该等待一段时间，因为 ionic v4 是紧张的更新。

好像在 ionicCLI 里面有一层`* .page.ts`之类的原创，但是这样想感觉好像不是那么想的。因为目的是通过 AngularCLI 开发。

还有另外一个包`@ionic/angular-toolkit`提供了 cordva 和 schematics 插件，但目前看来没必要。cordva 不需要成为一个简单的 web 应用程序，schematics 是一个如上所述再等等的决定。也许它不仅能生成 ng add，还能生成`* .page.ts`等。

# 配置

正常导入 NgModule 就可以了。既然是 WebComponent，就加`CUSTOM_ELEMENTS_SCHEMA`取消选中 Angular 的模板编译器。

app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';
import { IonicModule } from '@ionic/angular';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    IonicModule.forRoot(),
  ],
  providers: [],
  bootstrap: [AppComponent],
  schemas: [CUSTOM_ELEMENTS_SCHEMA],
})
export class AppModule {} 
```

将样式导入到根 css 文件中。作者提出了 scss 中的角度 CLI。请更换每一个。

```
/* Core CSS required for Ionic components to work properly */
@import "~@ionic/angular/css/core.css";

/* Basic CSS for apps built with Ionic */
@import "~@ionic/angular/css/normalize.css";
@import "~@ionic/angular/css/structure.css";
@import "~@ionic/angular/css/typography.css";

/* Optional CSS utils that can be commented out */
@import "~@ionic/angular/css/padding.css";
@import "~@ionic/angular/css/float-elements.css";
@import "~@ionic/angular/css/text-alignment.css";
@import "~@ionic/angular/css/text-transformation.css";
@import "~@ionic/angular/css/flex-utils.css";
@import "~@ionic/angular/css/display.css"; 
```

图标的实现需要一些技巧。由于引用没有解决，我复制到资产和使用。
这里是[的原理图代码](https://github.com/ionic-team/ionic/blob/master/angular/src/schematics/add/index.ts)，对实现`ng add @ionic/angular`很有帮助。我会拿起来手动复制。

angular.json

```
{  "projects":  {  "architect":  {  "build":  {  "options":  {  "assets":  [  "src/favicon.ico",  "src/assets",  {  "glob":  "**/*.svg",  "input":  "node_modules/ionicons/dist/ionicons/svg",  "output":  "./svg"  }  ],  }  }  }  }  } 
```

在图标标签中描述相对 URL。

```
<ion-item button routerLink="/home">
    <ion-icon slot="start" src="/assets/svg/md-home.svg"></ion-icon>
    <ion-label> Home </ion-label>
</ion-item> 
```

就是这样！基本上，把原理图的 [`ng add`代码做成教科书就好了。`ng add @ ionic/angular`命令本身不起作用，但最好参考。](https://github.com/ionic-team/ionic/blob/master/angular/src/schematics/add/index.ts)