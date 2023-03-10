# Angular 应用程序中的日期处理(第 2 部分——Angular Http 客户端和 Ngx 日期选择器)

> 原文：<https://dev.to/imben1109/date-handling-in-angular-application-part-2-angular-http-client-and-ngx-datepicker-3fna>

**原帖**:[https://medium . com/self-learning/date-handling-in-angular-application-part-2-angular-http-client-interceptor-and-ngx-date picker-BF 32231010 F8](https://medium.com/self-learning/date-handling-in-angular-application-part-2-angular-http-client-interceptor-and-ngx-datepicker-bf32231010f8)

# 简介

下面是之前关于 JavaScript Date 对象和 JSON Date 的[帖子](https://dev.to/imben1109/date-handling-in-angular-application-part-1-json-and-javascript-date-object-study-2p54)的总结。

*   JSON 是服务器端和客户端之间的通用通信格式
*   JavaScript 日期对象依赖于时区和区域设置
*   ISO 8601 日期格式是 JSON 日期表示的通用协议
*   JavaScript 不知道 JSON 日期类型。需要 JSON 日期字符串和 JavaScript 日期对象之间的转换。

# 无缝集成的方法

在 Angular 应用中，如何才能实现 JavaScript 日期对象、JSON 和 UI 组件的无缝集成？

无缝集成有两个重要部分。

*   客户端和服务器端之间的通信
*   日期选择器处理

# 客户端和服务器端之间的通信

在 Angular 应用中，它将提供 HttpClientModule 来简化客户端和服务器端之间的 HTTP 通信。

```
this.httpClient.get<DataModel>("/api/getData").subscribe(
  (data: DataModel) => {
    console.log(data);
  }
); 
```

虽然上面定义了数据模型(接口)，但是 JSON 没有日期对象。日期对象将被表示为 ISO 格式的数据字符串。**即使在 TypeScript 中，数据字符串在接口中被定义为日期，它也会简单地分配给日期。请注意，该接口将被编译为空，并且仅用于在 typescript 上下文中进行绑定检查。**

```
interface DataModel {
  data1: string; 
  data2: string; 
  data3: string
  date1: Date; 
  date2: Date; 
  date3: Date
} 
```

有两种方法可以转换

*   Http 客户端管道
*   角度 Http 拦截器

## Http 客户端管道

Http 客户端将返回各种提供的方法的可观察值。pipe 中的 map 运算符可用于将日期字符串转换为日期对象。

```
this.httpClient.get<DataModel>("/api/getData")
  .pipe(
    map((data)=>{
      data.date1 = new Date(data.date1);
      data.date2 = new Date(data.date2);
      data.date3 = new Date(data.date3);
      return data;
    })
  ).subscribe(
    (data: DataModel) => {
      console.log(data);
    }
  ); 
```

## 角形 Http 拦截器

由于日期字符串在 ISO 8601，我们希望有一个通用的方法来完成所有的转换。一个定制的 HttpInterceptor 将被添加到 Angular 应用程序中，用于拦截请求和响应。

```
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent, HttpResponse } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
export class JsonDateInterceptor implements HttpInterceptor {

  private _isoDateFormat = /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}(?:\.\d*)?Z$/;

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(map( (val: HttpEvent<any>) => {
      if (val instanceof HttpResponse){
        const body = val.body;
        this.convert(body);
      }
      return val;
    }));
  }

  isIsoDateString(value: any): boolean {
    if (value === null || value === undefined) {
      return false;
    }
    if (typeof value === 'string'){
      return this._isoDateFormat.test(value);
    }    return false;
  }
  convert(body: any){
    if (body === null || body === undefined ) {
      return body;
    }
    if (typeof body !== 'object' ){
      return body;
    }
    for (const key of Object.keys(body)) {
      const value = body[key];
      if (this.isIsoDateString(value)) {
        body[key] = new Date(value);
      } else if (typeof value === 'object') {
        this.convert(value);
      }
    }
  }
} 
```

```
this._httpClient.post<Value>("/api/posts", this.value)
  .subscribe((val: Value) => {
    console.log(val.date1);
  }); 
```

# 角度 UI 日期选择器

浏览器中的日期对象依赖于时区和区域设置。有时，我们希望在 web 应用程序中定义自定义的日期显示。我们不想关心浏览器的设置。

**未完待续**

[https://medium . com/self-learning/ngx-date picker-utc-date picker-design-77e 33789 e9d 7](https://medium.com/self-learning/ngx-datepicker-utc-datepicker-design-77e33789e9d7)

# 引用

*   [https://angular.io/guide/http](https://angular.io/guide/http)
*   [https://angular . io/guide/http #截取请求和响应](https://angular.io/guide/http#intercepting-requests-and-responses)