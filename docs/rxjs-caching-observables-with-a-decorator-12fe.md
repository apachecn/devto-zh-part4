# RxJS:用装饰器缓存观察值

> 原文：<https://dev.to/davidecavaliere/rxjs-caching-observables-with-a-decorator-12fe>

> 编辑:这里讨论的装饰器现在可以从 npm 获得。用`npm i @microphi/cache`或`yarn add @microphi/cache`安装

我从几个月前就开始追踪这个了。
下面这些你应该很熟悉:

```
@Injectable()
export class UserService {

  constructor(private _client: HttpClient) {}

  public findAll(id: number) {
    return this._client.get(`https://reqres.in/api/users?page=${id}`);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

出于某种原因，您希望缓存该请求的响应。如果你在网上寻找，你可能会找到一些关于如何做的提示，你可能最终会对所有你想缓存的请求做同样的事情。

不过，碰巧我是一名 java 开发人员，还记得以前的好时光，那时一个`@Cache`注释可以让我从大量重复的代码中解脱出来。

在 Typescript 中，我们有 decorator，所以一定有一种方法可以用简单的`@Cache`进行缓存，对吗？

我的直觉是:当然！

但是几次尝试都没有成功，我放弃了。

直到几天前，我发现了 Preston Lamb 写的这篇关于 angular 中的[缓存和刷新 observable 的文章，这重新点燃了我的好奇心。](https://www.prestonlamb.com/blog/rxjs-cache-and-refresh-in-angular)

从他的 stackbliz 例子开始，我做了一些实验，但还是没有运气。

直到我有了一个直觉:让我们赛跑吧。

```
@Injectable()
export class UserService {

  private cached$: ReplaySubject<any> = new ReplaySubject(1, 2500);

  constructor(private _client: HttpClient) {}

  public findAll(id): Observable<any> {

    const req = this._client.get(`https://reqres.in/api/users?page=${id}`).pipe(
      tap((data) => {
        this.cached$.next(data);
      })
    );

    return race(this.cached$, req);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

瞧吧。成功了！正是我喜欢的:简单而整洁。简单到我都不需要解释，对吧？

现在的问题是，如果您的服务中有许多需要缓存的方法，那么您将需要重复许多代码。雷姆:装修工！

### 让我们把所有东西都搬进一个装饰器里

```
export interface CacheOptions {
  ttl: number;
}

export function Cache(options: CacheOptions) {

  return (target: any, propertyKey: string, descriptor) => {

    const originalFunction = descriptor.value;

    target[`${propertyKey}_cached`] = new ReplaySubject(1, options.ttl);

    descriptor.value = function(...args) {

      const req = originalFunction.apply(this, args).pipe(
        tap((response) => {
          this[`${propertyKey}_cached`].next(response);
        })
      );

      return race(this[`${propertyKey}_cached`], req);

    };

    return descriptor;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里做的是用一个 replay subject 初始化一个名为`findAll_cached`的变量，然后用一个新函数替换原来的函数，这个新函数将调用原来的函数，应用我们之前看到的相同逻辑。

那么服务将如下所示:

```
@Injectable()
export class UserService {

  constructor(private _client: HttpClient) {}

  @Cache({
    ttl: 2500
  })
  public findAll(id): Observable<any> {
    return this._client.get(`https://reqres.in/api/users?page=${id}`)
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

太美了！

### 额外加分

现在我的朋友来了，他说:yo Davide，这很酷，但是如果你用不同的参数调用这个函数，你肯定需要再次调用 http。即:不同的输入不同的输出。对吗？

哦对，那很简单:

```
export function Cache(options: CacheOptions) {

  let lastCallArguments: any[] = [];

  return (target: any, propertyKey: string, descriptor) => {

    const originalFunction = descriptor.value;

    target[`${propertyKey}_cached`] = new ReplaySubject(1, options.ttl);

    descriptor.value = function(...args) {

      let argsNotChanged = true;

      for (let i = 0; i < lastCallArguments.length; i++) {
        argsNotChanged = argsNotChanged && lastCallArguments[i] == args[i];
      }

      if (!argsNotChanged) { // args change
        this[`${propertyKey}_cached`] = new ReplaySubject(1, options.ttl);
      }

      lastCallArguments = args;

      const req: Observable<any> = originalFunction.apply(this, args).pipe(
        tap((response) => {
          this[`${propertyKey}_cached`].next(response);
        })
      );

      // despite what the documentation says i can't find that the complete is ever called
      return race(this[`${propertyKey}_cached`], req);

    };

    return descriptor;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在下面的 [stackbliz](https://stackblitz.com/edit/rxjs-caching-decorator) 中玩这个代码，在我的 [github](https://github.com/davidecavaliere/drugo/tree/feat/create_library/portal/apps/portal/src/app/decorators/cache) 上找到完整的源代码。
请注意，github 上的代码将来可能会转移到另一个包中。

### 告诫

*   如果我们需要缓存的方法利用了 typescript 默认机制。即:

    ```
     public findAll(id: number = 1) {
            ...
        } 
    ```

    然后像调用`service.findAll()`一样调用它，碰巧`args`变量将会是`[]`一个空数组，因为只有当我们调用`.apply`时默认才会发生，所以在下面的例子中没有检测到参数的变化

    ```
    service.findAll()

    service.findAll(2) 
    ```

*   让我们看看前面提到的 stackbliz 示例的 home.component 中的示例

    ```
     setTimeout(() => {
          this.$log.d('starting subscriber');
          this.userService.findAll(1).subscribe((data) => {
            this.$log.d('starting subscribed');
            this.$log.d(data);
            this.users = data;

          })
        }, 0);

        setTimeout(() => {
          this.$log.d('first subscriber 1 sec later');
          this.userService.findAll(1).subscribe((data) => {
            this.$log.d('first subscribed');
            this.$log.d(data);

          })
        }, 1000);

        setTimeout(() => {
          this.$log.d('second subscriber 2 sec later');
          this.userService.findAll(1).subscribe((data) => {
            this.$log.d('second subscribed');
            this.$log.d(data);

          })
        }, 2000);

        setTimeout(() => {
          this.$log.d('third subscriber 3 sec later, ttl expired. shoult hit the endpoint');
          this.userService.findAll(1).subscribe((data) => {
            this.$log.d('third subscribed');

            this.$log.d(data);

          })
        }, 3000);

        setTimeout(() => {
          this.$log.d('fourth subscriber 4 sec later, argument changed. should hit the endpoint');
          this.userService.findAll(2).subscribe((data) => {

            this.$log.d(' fourth subscribed');

            this.$log.d(data);
          })
        }, 4000);

        setTimeout(() => {
          this.$log.d('fifth subscriber 5 sec later, argument changed. should hit the endpoint');
          this.userService.findAll(1).subscribe((data) => {

            this.$log.d(' fifth subscribed');

            this.$log.d(data);
          })
        }, 5000); 
    ```

    它在控制台
    上输出如下内容

    ```
     [...]
    third subscriber 3 sec later, ttl expired. shoult hit the endpoint
    arguments are
    [1]
    argsNotChanged
    true
    this actually hit the endpoint
    starting subscribed
    {page: 1, per_page: 6, total: 12, total_pages: 2…}
    first subscribed
    {page: 1, per_page: 6, total: 12, total_pages: 2…}
    second subscribed
    {page: 1, per_page: 6, total: 12, total_pages: 2…}
    third subscribed
    {page: 1, per_page: 6, total: 12, total_pages: 2…}
    [...] 
    ```

    如您所见，当我们在缓存过期后再次订阅时，所有以前的订阅都会再次收到通知。

感谢阅读到目前为止，我希望你喜欢并记住:如果你喜欢这篇文章与你的朋友分享，如果你不为自己保留它；)