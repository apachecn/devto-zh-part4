# 带角度的运行时配置

> 原文：<https://dev.to/beaussart/runtime-config-with-angular-2aj0>

在 angular 中，默认有一个构建时配置，它工作得很好，但是它有一些缺点:

*   要更改配置，您必须重新构建您的应用程序
*   不尊重“一次构建，处处部署”的 devops 理念

例如，如果您想要将 angular 项目构建到 docker 映像中，并且只使用构建时配置，那么您将需要在每次构建 docker 时指向另一个后端！

## 解决方案是运行时配置。

运行时配置是一个配置文件，通常在启动时获取，包含像服务器 URL 或其他细节的配置。

幸运的是，Angular 有一个钩子在启动时运行一些东西，`APP_INITIALIZER`我们可以用它来获取启动时的配置！

首先，让我们制作`config.json`文件，在`assets`文件夹中:

```
{  "API_URL":  "http://localhost:3000"  } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以创建获取配置的服务:

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root',
})
export class AppConfigService {
  private appConfig;

  constructor(private http: HttpClient) {}

  loadAppConfig() {
    return this.http
      .get('/assets/config.json')
      .toPromise()
      .then(data => {
        this.appConfig = data;
      });
  }

  getServerUrl(): string {
    return this.appConfig.API_URL;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，你必须返回一个承诺而不是一个可观察的，因为`APP_INITIALIZER`需要一个承诺而不是一个可观察的。

一旦我们有了这个服务，我们必须用一个函数来编辑`app.module.ts`以加载配置

```
const initializerConfigFn = (appConfig: AppConfigService) => {
  return () => {
    return appConfig.loadAppConfig();
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

并在主模块中提供:

```
@NgModule({
  imports: [
    ...
    HttpClientModule,
    ...
  ],
  providers: [
    ...
    {
      provide: APP_INITIALIZER,
      useFactory: initializerConfigFn,
      multi: true,
      deps: [AppConfigService],
    },
    ...
  ],
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

我们找到了！您的应用程序将在获取 config.json 后运行。

## 奖励:自动 http 前缀

这样，我们就可以使用我们之前获取的设置:
来创建一个自动 http 前缀混合器

```
import { Injectable } from '@angular/core';
import {
  HttpEvent,
  HttpInterceptor,
  HttpHandler,
  HttpRequest,
} from '@angular/common/http';
import { Observable } from 'rxjs';
import { AppConfigService } from '../services/app-config.service';

 /**
 * Prefixes all requests not starting with `http[s]` with the dynamic config.
 */
@Injectable()
export class ApiPrefixInterceptor implements HttpInterceptor {
  constructor(private readonly appConfig: AppConfigService) {}

   intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (!/^(http|https):/i.test(request.url) && !request.url.startsWith('/assets/')) {
      request = request.clone({ url: this.appConfig.getServerUrl() + request.url });
    }
    return next.handle(request);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的 AppModule 中提供:

```
@NgModule({
  imports: [
    ...
    HttpClientModule,
    ...
  ],
  providers: [
    ...
    {
      provide: APP_INITIALIZER,
      useFactory: initializerConfigFn,
      multi: true,
      deps: [AppConfigService],
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: ApiPrefixInterceptor,
      multi: true
    },
    ...
  ],
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

你有它！从一个动态 url 获取的自动 url 前缀。