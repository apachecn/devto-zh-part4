# JavaScript/TypeScript 异步提示

> 原文：<https://dev.to/akashkava/javascript-typescript-async-tips-108o>

请随意添加更多提示。

# 如果只有最后一条 return 语句有 await，则不创建异步

```
 public async fetchList(): Promise<T> {

   return await this.someService.fetchList(...);

} 
```

Enter fullscreen mode Exit fullscreen mode

这里可以省略`async/await`

```
 public fetchList(): Promise<T> {

   return this.someService.fetchList(...);

} 
```

Enter fullscreen mode Exit fullscreen mode

两者在逻辑上是相同的，除非编译器试图自动优化，否则您可以简单地避免`async/await`。

# 捕捉异常时不要省略`async/await`...

在上面的例子中，如果你想捕捉一个异常...以下代码是错误的...

```
 public fetchList(): Promise<T> {
   try {
      return this.someService.fetchList(...);
   } catch(e) {
       // report an error
      this.reportError(e);
      return Promise.resolve(null);
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

这将永远不会赶上一个网络相关的错误，以下是正确的方法。

```
 public async fetchList(): Promise<T> {
   try {
      return await this.someService.fetchList(...);
   } catch(e) {
       // report an error
      this.reportError(e);
      return null;
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

# 使用`Promise.all`

```
 public async fetchDetails(list: IModel): Promise<IDetail[]> {
       const details = [];
       for(const iterator of list) {
           const result = await this.someService.fetchDetails(iterator.id);
           details.push(result);
       }
       return details;
   } 
```

Enter fullscreen mode Exit fullscreen mode

有顺序操作，这将需要很长时间，而不是尝试这个..

```
 public fetchDetails(list: IModel): Promise<IDetail[]> {
       const details = list.map((item) => 
          this.someService.fetchDetails(item.id));
       return Promise.all(details);
   } 
```

Enter fullscreen mode Exit fullscreen mode

如果您想在任何一个失败时返回 null，

```
 public fetchDetails(list: IModel): Promise<IDetail[]> {
       const details = list.map(async (item) => {
           try {
              return await this.someService.fetchDetails(item.id); 
           } catch (e) {
               this.reportError(e);
               return null;
           }
       });
       return Promise.all(details);
   } 
```

Enter fullscreen mode Exit fullscreen mode

## 你也可以在没有数组的情况下使用`Promise.all`

```
 public async fetchDetails(): Promise<void> {
       this.userModel = await this.userService.fetchUser();
       this.billingModel = await this.billingService.fetchBilling();
       this.notifications = await this.notificationService.fetchRecent();
   } 
```

Enter fullscreen mode Exit fullscreen mode

你可以把它改写成，

```
 public fetchDetails(): Promise<void> {
       [this.userModel, 
          this.billingModel,
          this.notifications] = Promise.all(
              [this.userService.fetchUser(),
              this.billingService.fetchBilling(),
              this.notificationService.fetchRecent()]);
   } 
```

Enter fullscreen mode Exit fullscreen mode

# 原子缓存承诺

只要您希望缓存以前的承诺，您就可以保留对它们的引用，并且承诺的结果将可用于所有将来的调用，而无需调用实际的远程调用。

```
 private cache: { [key: number]: [number, Promise<IDetail>] } = {};

   public fetchDetails(id: number): Promise<IDetail> {
      let [timeout, result] = this.cache[id];
      const time = (new Date()).getTime();
      if (timeout !== undefined && timeout < time {
         timeout = undefined; 
      }
      if (timeout === undefined) {
         // cache result for 60 seconds
         timeout = time + 60 * 1000;
         result = this.someService.fetchDetails(id);
         this.cache[id] = [timeout, result];
      }
      return result;
   } 
```

Enter fullscreen mode Exit fullscreen mode

这个调用是原子性的，因此对于任何给定的 id，在 60 秒内只对远程服务器进行一次调用。

# 延迟

```
 public static delay(seconds: number): Promise<void> {
       return new Promise((r,c) => {
           setTimeout(r, seconds * 1000);
       });
   }

   // usage...

   await delay(0.5); 
```

Enter fullscreen mode Exit fullscreen mode

# 延迟与取消相结合

如果我们想在某人一输入字符就显示结果的时候提供交互式搜索，但是你想只在 500 毫秒的停顿时才启动搜索，这就是如何做到的。

```
 public cancelToken: { cancelled: boolean } = null;   

   public fetchResults(search: string): Promise<IModel[]> {
       if (this.cancelToken) {
           this.cancelToken.cancelled = true;
       }
       const t = this.cancelToken = { cancelled: false };
       const fetch = async () => {
           await delay(0.5);
           if(t.cancelled) {
              throw new Error("cancelled");
           }
           const r = await this.someService.fetchResults(search);
           if(t.cancelled) {
              throw new Error("cancelled");
           }
           return r;
       };
       return fetch();
   } 
```

Enter fullscreen mode Exit fullscreen mode

如果该方法将在 500 毫秒内被调用，则该方法不会调用远程 API。

但 500ms 后有被调用的可能。为了支持 rest API 取消，需要做更多的工作。

# 取消令牌类

```
export class CancelToken {

    private listeners: Array<() => void> = [];

    private mCancelled: boolean;
    get cancelled(): boolean {
        return this.mCancelled;
    }

    public cancel(): void {
        this.mCancelled = true;
        const existing = this.listeners.slice(0);
        this.listeners.length = 0;
        for (const fx of existing) {
            fx();
        }
    }

    public registerForCancel(f: () => void): void {
        if (this.mCancelled) {
            f();
            this.cancel();
            return;
        }
        this.listeners.push(f);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

## Api 调用需要一些工作...举一个 XmlHttpRequest 的例子

```
 public static delay(n: number, c: CancelToken): Promise<void> {
      return new Promise((resolve, reject) => {
         let timer = { id: null };
         timer.id = setTimeout(() => {
            timer.id = null;
            if(c.cancelled) {
                reject("cancelled");
                return;
            }
            resolve();
         }, n * 1000);
         c.registerForCancel(() => {
            if( timer.id) { 
               clearTimeout(timer.id);
               reject("cancelled");
            }
         });
      });
   }

   public async ajaxCall(options): Promise<IApiResult> {

      await delay(0.1, options.cancel);

      const xhr = new XMLHttpRequest();

      const result = await new Promise<IApiResult> ((resolve, reject)) => {

         if (options.cancel && options.cancel.cancelled) {
             reject("cancelled");
             return;
         }

         if (options.cancel) {
             options.cancel.registerForCancel(() => {
                xhr.abort();
                reject("cancelled");
                return;
             });
         }

         // make actual call with xhr...

      });

      if (options.cancel && options.cancel.cancelled) {
          throw new Error("cancelled");
      }
      return result;

   } 
```

Enter fullscreen mode Exit fullscreen mode

这可以取消用户在 500 毫秒后一输入新字符就取消的请求。

这将取消现有调用，浏览器停止处理输出，浏览器终止连接，如果服务器足够聪明能够理解，这也将取消服务器端处理。

# 为达到最佳使用效果，您可以组合所有提示并创建最佳 UX。