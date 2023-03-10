# Angular:在任何执行 HTTP 请求的组件上显示一个微调器

> 原文：<https://dev.to/elasticrash/angular-display-a-spinner-on-any-component-that-does-an-http-request-2gm2>

从定义上来说，这是我写过的最奇怪、最有用、也有点丑陋的组件之一。

我们的目标是在任何依赖于 HTTP 请求的组件之上添加一个微调器

首先，我们需要创建一个简单的组件，它可以接受其父组件的大小，并在中间有一个微调器。我正在使用 Angular 素材库来简化事情。

这个组件使用一个名为`HttpStateService`的服务。我们马上会看到`HttpStateService`只有一个 [`BehaviorSubject`](http://reactivex.io/rxjs/manual/overview.html#behaviorsubject) 类型的属性。所以它基本上是用来来回传递信息的。

因此，我们的组件订阅来自该主题的任何消息。
spinner 组件还有一个`@Input()`属性，它应该对哪个 url 做出反应。

```
@Component({
  selector: 'http-spinner',
  templateUrl: './spinner.component.html',
  styleUrls: ['./spinner.component.scss']
})
export class SpinnerComponent implements OnInit {
  public loading = false;
  @Input() public filterBy: string | null = null;
  constructor(private httpStateService: HttpStateService) { }

  /**
   * receives all HTTP requests and filters them by the filterBy
   * values provided
   */
  ngOnInit() {
    this.httpStateService.state.subscribe((progress: IHttpState) => {
      if (progress && progress.url) {
        if (!this.filterBy) {
          this.loading = (progress.state === HttpProgressState.start) ? true : false;
        } else if (progress.url.indexOf(this.filterBy) !== -1) {
          this.loading = (progress.state === HttpProgressState.start) ? true : false;
        }
      }
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

css

```
.loading {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    background: rgba(0, 0, 0, 0.15);
    z-index: 1;
    display: flex;
    align-items: center;
    justify-content: center;
  } 
```

Enter fullscreen mode Exit fullscreen mode

在 html 中，我们要么 a)显示全部内容，要么 b)不显示

```
<div *ngIf="loading" class="loading">
  <mat-spinner></mat-spinner>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们非常简单的 HttpProgressState 表示
请求是开始还是结束

```
export enum HttpProgressState {
    start,
    end
} 
```

Enter fullscreen mode Exit fullscreen mode

单项`BehaviorSubject`物业服务

```
@Injectable({
  providedIn: 'root'
})
export class HttpStateService {
  public state = new BehaviorSubject<IHttpState>({} as IHttpState);

  constructor() { }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在最重要的一点是`HttpInterceptor`。一个`HttpInterceptor`基本上是一个`man in the middle`服务，它拦截你可能试图通过`HttpClientModule`做的所有请求，并在它们被解雇之前操纵它们或对它们做出反应。这里我有一个相对简单的`HttpInterceptor`实现。我添加了 take 和 delay 来强调一些`HttpInterceptor`可能拥有的强大功能。

除了采取和延迟，我还增加了一个，这是最终确定的。

所以基本上每次`InterceptorService`拦截请求时，它都会向`HttpStateService`发送一条包含 url 和开始状态的消息。
然后在完成时(请求完成后)向`HttpStateService`和
发送一个结束状态

```
@Injectable({
  providedIn: 'root'
})
export class InterceptorService implements HttpInterceptor {

  private exceptions: string[] = [
    'login'
  ];

  constructor(
    private httpStateService: HttpStateService) {

  }

  /**
   * Intercepts all requests
   * - in case of an error (network errors included) it repeats a request 3 times
   * - all other error can be handled an error specific case
   * and redirects into specific error pages if necessary
   *
   * There is an exception list for specific URL patterns that we don't want the application to act
   * automatically
   * 
   * The interceptor also reports back to the httpStateService when a certain requests started and ended 
   */
  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {

    if (!this.exceptions.every((term: string) => request.url.indexOf(term) === -1)) {
      return next.handle(request).pipe(tap((response: any) => {},
     (error) => {}));
    }

    this.httpStateService.state.next({
      url: request.url,
      state: HttpProgressState.start
    });

    return next.handle(request).pipe(retryWhen(
      error => {
        return error.pipe(take(3), delay(1500),
          tap((response: any) => {
             // ...logic based on response type
             // i.e redirect on 403
             // or feed the error on a toaster etc
          })
        );
      }
    ), finalize(() => {
      this.httpStateService.state.next({
        url: request.url,
        state: HttpProgressState.end
      });
    }));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

它的用法很简单:将它添加到任何需要微调器的组件中，并定义它需要监听哪个端点。

```
<http-spinner filterBy="data/products"></http-spinner> 
```

Enter fullscreen mode Exit fullscreen mode

最后，要在一个`Module`上添加一个拦截器，你只需要添加另一个提供者，如下例

```
 providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: InterceptorService,
      multi: true
    }
....
] 
```

Enter fullscreen mode Exit fullscreen mode

缺少接口(见注释)

```
export interface IHttpState {
    url: string;
    state: HttpProgressState;
} 
```

Enter fullscreen mode Exit fullscreen mode