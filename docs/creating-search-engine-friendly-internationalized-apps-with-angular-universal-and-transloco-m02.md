# 使用 Angular Universal 和 Transloco 创建搜索引擎友好的国际化应用程序🌐

> 原文：<https://dev.to/shaharkazaz/creating-search-engine-friendly-internationalized-apps-with-angular-universal-and-transloco-m02>

最初发布于 [shahar.kazaz](https://medium.com/@shahar.kazaz/creating-search-engine-friendly-internationalized-apps-with-angular-universal-and-transloco-ab9583cfb5ac)

在本文中，我将向您展示使用下一代 Angular internationalization library trans loco，我们可以多么容易地将国际化(i18n)支持添加到 Angular SSR 中。让我们开始吧。

首先，让我们创建一个全新的 Angular CLI 项目:

```
npx @angular/cli new transloco-ssr 
```

现在，通过使用`@nguniversal/express-engine`原理图:
添加 SSR 支持

```
ng add @nguniversal/express-engine --clientProject <PROJECT-NAME> 
```

使用 schematics:
将 Transloco 添加到您的项目中

```
ng add @ngneat/transloco 
```

选择您支持的语言，并回答 SSR 问题的`Yes`。现在您可以看到它创建了翻译文件、加载程序，并向`AppModule`添加了所有需要的东西。

当使用 Angular SSR 时，我们需要将加载器的基本路径改为绝对路径，而不是相对路径，这样它才能工作。已经为你做好了。

您唯一需要做的事情就是根据您的应用程序
修改每个`environment`文件中的新`baseUrl`属性

```
export const environment = {
  production: false,
  baseUrl: 'http://localhost:4200' <====
};

// environment.prod.ts
export const environment = {
  production: true,
  baseUrl: 'http://localhost:4000' <====
}; 
```

现在让我们给我们的翻译文件添加一个新的键:

```
{  "title":  "Angular SSR with Transloco english"  } 
```

让我们在模板中使用它:

```
<ng-template transloco let-t>
  {{ t.title }}
</ng-template> 
```

让我们使用以下命令构建我们的生产应用程序:

```
npm run build:ssr
npm run serve:ssr 
```

打开浏览器，导航至`http://localhost:4000`和 Viola！我们做到了！

让我们看看 HTML 源文件:
图片

我们在 5 分钟内得到了一个支持 SSR 和国际化(i18n)的工作应用程序。

## 奖金

我们可以使用`accept-language`标题来自动设置首选的用户语言。Accept-Language 请求 HTTP 头通告了客户端可以理解的语言，以及首选的语言环境变量。

```
import { Component, Inject, Optional } from '@angular/core';
import { Request } from 'express';
import { REQUEST } from '@nguniversal/express-engine/tokens';
import { TranslocoService } from '@ngneat/transloco';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  constructor(@Optional() @Inject(REQUEST) private request: Request, private translocoService: TranslocoService) {
    if (request.headers) {
      translocoService.setActiveLang(normalize(request.headers['accept-language']));
    }
  }
} 
```

`accept-language`头返回以下字符串:

```
 'accept-language': 'en-US,en;q=0.9,he;q=0.8,ru;q=0.7,fr;q=0.6,pt;q=0.5', 
```

您需要提取语言并将其设置为活动的。

* * *

以下是关于 Transloco 的更多精彩内容:

[![🚀 Introducing Transloco: Angular Internationalization Done Right](img/d1a1f18e232107ba7d6e0cc9dc037735.png)](https://netbasal.com/introducing-transloco-angular-internationalization-done-right-54710337630c)

[![🎉 Good Things Come to Those Who Wait: What’s new in Transloco](img/a60a7a630208b78339ae42814d74fd63.png)](https://netbasal.com/good-things-come-to-those-who-wait-whats-new-in-transloco-5dadf886b485)