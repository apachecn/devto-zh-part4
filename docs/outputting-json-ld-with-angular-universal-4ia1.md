# 用角度通用输出 JSON-LD

> 原文：<https://dev.to/angular/outputting-json-ld-with-angular-universal-4ia1>

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/outputting-json-ld-with-angular-universal)2019 年 3 月 12 日*

## 目标受众

这篇关于使用 Angular Universal 生成 JSON-LD 的文章和指南是面向那些希望生成表示链接数据对象的 JSON 并将其正确注入到服务器上生成的 HTML 中的开发人员的。我们将重用和解释一些技术，`TransferState`键值缓存服务使用这些技术将服务器状态传输到客户端的应用程序。

## 首先；JSON-LD 是什么？

JSON-LD 是一种轻量级的链接数据格式。对于人类和机器来说，读和写都很容易。它基于已经成功的 JSON 格式，并提供了一种方法来帮助 JSON 数据在 Web 范围内进行互操作。

[链接数据](https://en.wikipedia.org/wiki/Linked_data)赋予人们在网络上发布和使用信息的权利。这是一种创建跨网站的基于标准的机器可读数据网络的方法。它允许应用程序从一个链接数据开始，并沿着嵌入的链接到 Web 上不同站点上托管的其他链接数据。

JSON-LD 和关联数据的这两个**定义简单地取自 JSON-LD 的网站。本文没有深入研究 JSON-LD 的完整规范，也没有教您如何正确地生成链接数据结构。JSON-ld 网站的[工具，例如](https://json-ld.org/)[游乐场](https://json-ld.org/playground/)，给你验证链接数据结构所需的一切。**

### 一个基本例子

下面是一个 JSON-LD 数据对象的典型例子，它被注入到 HTML 中。它通过[描述一个`LocalBusiness`，遵循它的定义](https://schema.org/LocalBusiness)，并提供标识实体的属性。例如，地址，它的地理位置，营业时间等都给了。如你所见，地址有自己的类型`PostalAddress`。大多数类型可以指定一个外部链接，这就是数据如何在同一个网站或资源，甚至在其他网站上交织在一起。

```
<!-- json-ld-example.html -->

<script type="application/ld+json">
{
  "@context": "http://schema.org",
  "@type": "LocalBusiness",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Manhattan",
    "addressRegion": "NY",
    "postalCode":"10036",
    "streetAddress": "400 Broadway"
  },
  "description": "This is your business description.",
  "name": "Craig's Car Repair",
  "telephone": "555-111-2345",
  "openingHours": "Mo,Tu,We,Th,Fr 09:00-17:00",
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "20.75",
    "longitude": "13.98"
  },
  "sameAs" : [ "http://www.facebook.com/example",
    "http://www.twitter.com/example",
    "http://plus.google.com/example"]
}
</script> 
```

### 为什么要关心 JSON-LD？

出于几个原因，您绝对应该关心 JSON-LD:

*   与[微数据](https://schema.org/docs/gs.html)相比，它更容易生成，更有条理，也更容易阅读
*   它链接或允许您的数据与万维网上的其他实体/数据主体链接

总的来说；它通过为你在页面上显示的内容提供上下文，为你的 HTML 标记增加了更多的语义价值，并使这种上下文机器可读，使它更容易被搜索引擎和网络上的其他爬虫解析。

## 生成你的 JSON 链接数据结构

在简单的博客或不太复杂的网站中，生成 JSON 链接数据可以使用与设置社交共享和 SEO 元标签相同的基础数据。你最关心的是建立正确的结构。你的结构看起来如何完全取决于你的用例。作为一个基本的例子，我们将使用这个网站，更具体地说是关于页面的[。](https://samvloeberghs.be/about)

使用路由器，我们首先定义与我们的路由相关的正确的 SEO 数据。就像我们之前做的一样，[设置正确的社交分享元标签](https://samvloeberghs.be/posts/better-sharing-on-social-media-platforms-with-angular-universal)。

```
// about-routing.module.ts

RouterModule.forChild([{
    path: '',
    component: AboutComponent,
    data: {
      seo: {
        title: `About Sam - ${environment.seo.title}`,
        description: `I'm a 30 year old software engineer living in Belgium.`,
        shareImg: '/assets/share/about.png',
      }
    }
}]) 
```

使用服务，我们可以订阅路由更改来提取数据，并将数据传递给服务来解析 JSON-LD 对象。

```
// route-helper.service.ts

@Injectable({
    providedIn: 'root',
})
export class RouteHelper {

  constructor(
    private readonly router: Router,
    private readonly activatedRoute: ActivatedRoute,
    private readonly jsonLdService: JsonLdService
  ) {
    this.setupRouting();
  }

  private setupRouting() {
    this.router.events.pipe(
      filter(event => event instanceof NavigationEnd),
      map(() => this.activatedRoute),
      map(route => {
        while (route.firstChild) {
          route = route.firstChild;
        }
        return route;
      }),
      filter(route => route.outlet === 'primary')
    ).subscribe((route: ActivatedRoute) => {
      const seo = route.snapshot.data['seo'];
      // generate your JSON-LD object here
      const jsonLd = {
        name: seo.title,
        url: environment.url + this.router.routerState.snapshot.url,
      };
      this.jsonLdService.setData('Website', jsonLd);
    });
  }

} 
```

上面注入的`JsonLdService`是一个简单的服务，缓存并更新我们需要输出的数据结构。该服务的一个基本实现如下，其中作为开发人员，您仍然负责正确构造您的对象。

```
// ngx-seo/json-ld.service.ts

@Injectable({
  providedIn: 'root',
})
export class JsonLdService {

  private jsonLd: any = {};

  setData(type: string, rawData: any) {
    this.jsonLd = this.getObject(type, rawData);
  }

  getObject(type: string, rawData?: any) {
    let object = {
      '@context': 'http://schema.org',
      '@type': type,
    };
    if (rawData) {
      object = Object.assign({}, object, rawData);
    }
    return object;
  }

  toJson() {
    return JSON.stringify(this.jsonLd);
  }
} 
```

使用上面解释的方法，我们当前在内存中的 JSON-LD 数据对象将看起来像这样:

```
{  "@context":  "http://schema.org",  "@type":  "Website",  "name":  "About Sam - Sam Vloeberghs - Freelance Webdeveloper & Software Engineer",  "url":  "https://samvloeberghs.be/about"  } 
```

这个基本的例子就是我们想要实现的。下一步是在静态 HTML 中输出这个对象。

## 在静态 HTML 中注入 JSON

### 在棱角分明的源代码中获取灵感

只有当我们在服务器上生成 HTML 时，才真正需要在 DOM 中注入 JSON-LD 数据对象。为了得到这个结果，我查看了通过`@angular/platform-browser`模块公开的`BrowserTransferStateModule`([https://github . com/angular/angular/blob/master/packages/platform-browser/src/browser/transfer _ state . ts](https://github.com/angular/angular/blob/master/packages/platform-browser/src/browser/transfer_state.ts))。

本质上，这个模块，更具体地说，它提供的`TransferState`([https://github . com/angular/angular/blob/master/packages/platform-browser/src/browser/transfer _ state . ts](https://github.com/angular/angular/blob/master/packages/platform-browser/src/browser/transfer_state.ts))服务，做了我们想要实现的事情。它将数据(例如 HTTP 调用的结果)缓存在一个对象中，并在应用程序生命周期中将其保存在内存中。除了 HTTP 调用，这正是我们的`JsonLdService`所做的。

通过`@angular/platform-server`暴露的服务器计数器部分`ServerTransferStateModule`([https://github . com/angular/angular/blob/master/packages/platform-server/src/transfer _ state . ts](https://github.com/angular/angular/blob/master/packages/platform-server/src/transfer_state.ts))在服务器上生成 HTML 并通过网络将其发送回浏览器之前，将数据序列化并将其注入 DOM。同样，这正是我们想要实现的目标。这是通过向`BEFORE_APP_SERIALIZED`令牌提供一个额外的工厂函数来实现的。在应用程序变得稳定之后，所有附属于`BEFORE_APP_SERIALIZED`的工厂都被执行。

```
// server: transfer_state.ts

export function serializeTransferStateFactory(
  doc: Document, appId: string, transferStore: TransferState
) {
  return () => {
    const script = doc.createElement('script');
    script.id = appId + '-state';
    script.setAttribute('type', 'application/json');
    script.textContent = escapeHtml(transferStore.toJson());
    doc.body.appendChild(script);
  };
}
@NgModule({
  providers: [
    TransferState,
    {
      provide: BEFORE_APP_SERIALIZED,
      useFactory: serializeTransferStateFactory,
      deps: [DOCUMENT, APP_ID, TransferState],
      multi: true,
    }
  ]
})
export class ServerTransferStateModule {
} 
```

当 Angular 应用程序在浏览器中启动时，`TransferState`服务在静态 HTML 中获取序列化状态，并将其解序列化，使其可以作为直接缓存使用。通过这种方式，我们避免了应用程序向服务器发出对相同数据的类似请求，而服务器端呈现的版本已经对此进行了调用。我们不需要这一部分，但知道这一点很好。

```
// browser: transfer_state.ts

export function initTransferState(
  doc: Document, appId: string
) {
  const script = doc.getElementById(appId + '-state');
  let initialState = {};
  if (script && script.textContent) {
    try {
      initialState = JSON.parse(unescapeHtml(script.textContent));
    } catch (e) {
      console.warn('Exception while restoring TransferState for app ' + appId, e);
    }
  }
  return TransferState.init(initialState);
}
@NgModule({
  providers: [{
    provide: TransferState,
    useFactory: initTransferState,
    deps: [DOCUMENT, APP_ID]
  }s],
})
export class BrowserTransferStateModule {
} 
```

### 我们简单的 BrowserJsonLdModule 和 ServerJsonLdModule

遵循我们从`ServerTransferStateModule`和`BrowserTransferStateModule`中学到的概念和想法，我们创造了自己的`ServerJsonLdModule`和`BrowserJsonLdModule`。唯一的小区别是改变了`<script>`元素的类型，并将其注入到`<head>`中，而不是正好在`</body>`标签之前。我们也不需要像在`BrowserTransferStateModule`中那样从静态 HTML 中获取任何状态。

```
// ngx-seo/json-ld.module.ts

import { NgModule } from '@angular/core';
import { JsonLdService } from 'ngx-seo/json-ld.service';

@NgModule({
  providers: [
    JsonLdService,
  ]
})
export class BrowserJsonLdModule {
}
ngx-seo/json-ld.server.module.ts
import { NgModule } from '@angular/core';
import { DOCUMENT } from '@angular/common';
import { BEFORE_APP_SERIALIZED } from '@angular/platform-server';
import { JsonLdService } from 'ngx-seo/json-ld.service';

export function serializeJsonLdFactory(doc: Document, jsonLdService: JsonLdService) {
  const serializeAndInject = function () {
    const script = doc.createElement('script');
    script.setAttribute('type', 'application/ld+json');
    script.textContent = jsonLdService.toJson();
    doc.head.appendChild(script);
  };
  return serializeAndInject;
}

@NgModule({
  providers: [
    JsonLdService, {
    provide: BEFORE_APP_SERIALIZED,
      useFactory: serializeJsonLdFactory,
      deps: [DOCUMENT, JsonLdService],
      multi: true,
    },
  ],
})
export class ServerJsonLdModule {
} 
```

## 在服务器上区分执行

为了区分我们的应用在服务器和浏览器上的执行，在 Angular Universal 中，我们通常在`app.module.ts`旁边创建一个新的服务器模块`app.server.module.ts`，它将直接导入由`app.module.ts`导出的 AppModule。这个版本的应用程序用于服务器端配置的 HTML 渲染器，在大多数情况下作为[渲染引擎](https://expressjs.com/en/guide/using-template-engines.html)。

如果我们回到`TransferState`服务的例子，我们会看到`app.server.module.ts`正在导入`ServerTransferStateModule`。这将覆盖在`app.module.ts`中导入的`JsonLdService`的提供者，并提供额外的序列化功能，接下来还提供`TransferState`服务。

```
// app.module.ts
`
@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule.withServerTransition({appId: 'samvloeberghs'}),
    BrowserTransferStateModule,
    BrowserJsonLdModule,
    CommonModule,
    AppRoutingModule
  ],
  exports: [
    AppComponent,
  ],
  bootstrap: [AppComponent],
})
export class AppModule {
} 
```

```
// app.server.module.ts

@NgModule({
  imports: [
    AppModule,
    ServerModule,
    ModuleMapLoaderModule,
    ServerTransferStateModule,
    ServerJsonLdModule,
  ],
  bootstrap: [AppComponent],
})
export class AppServerModule {
} 
```

我们用我们的`BrowserJsonLdModule`和`ServerJsonLdModule`做同样的事情。浏览器模块必须在`app.module.ts`导入，而服务器模块必须在`app.server.module.ts`导入。

## 结论

使用 Angular Universal 生成 JSON-LD 非常简单。通过借鉴 Angular 源代码中的概念，我们可以创建自己的`JsonLd`模块和服务，使我们能够在服务器上以静态生成的 HTML 格式输出 JSON-LD。这段代码的结果可以在这个网站上找到，只需查看源代码并进行硬刷新。你可能会问，为什么要硬刷新？因为服务工作者将默认的`index.html`缓存为应用外壳。

## 进一步阅读

*   [初学者 JSON-LD 指南](https://moz.com/blog/json-ld-for-beginners)
*   [为什么 JSON-LD 对企业很重要？](https://www.forbes.com/sites/forbestechcouncil/2019/02/25/why-is-json-ld-important-to-businesses/amp/)
*   [链接数据的 JSON 官网](https://json-ld.org/)
*   [使用微数据开始使用 schema.org](https://schema.org/docs/gs.html)
*   [谷歌的结构化数据测试工具](https://search.google.com/structured-data/testing-tool/u/0/)
*   窃取我们的 JSON-LD 结构化数据。变得简单。
*   [使用 schema 和 JSON-LD 的 Angular SEO 一种不同的方法](https://twitter.com/0594CF)

## 特别感谢

*   [影相王子](https://twitter.com/0594CF)

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/outputting-json-ld-with-angular-universal)2019 年 3 月 12 日*