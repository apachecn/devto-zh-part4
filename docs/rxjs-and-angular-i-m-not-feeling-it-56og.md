# RxJS 和 Angular，我没感觉

> 原文：<https://dev.to/vivainio/rxjs-and-angular-i-m-not-feeling-it-56og>

我读过一篇关于为 Angular 创建定制闭包构建器的文章，显然“架构师”(是的，这是系统的名字)非常依赖 RxJS 来获得一致的 API。

[![steveblue image](img/e80ee7f370892494e970a1fcc9fcd3e1.png)](/steveblue) [## 像建筑师一样塑造棱角(第 1 部分)

### 史蒂夫·别洛瓦里奇 4 月 2 日 1919 分钟阅读

#angular #javascript #devops #webdev](/steveblue/build-angular-like-an-architect-part-1-3ph2)

然而，我在想，如果我们(Angular 社区)可以在有意义的地方使用承诺，人们会轻松得多。下面是这篇文章的一个示例片段:

```
return of(context).pipe(
    concatMap( results => ngc(options, context)),
    concatMap( results => compileMain(options, context)),
    concatMap( results => closure(options, context) ),
    mapTo({ success: true }),
    catchError(error => {
      context.reportStatus('Error: ' + error);
      return [{ success: false }];
    }),
  ); 
```

这是承诺的样子(在“异步”函数中):

```
 try {
  await ngc(options, context);
  await compileMain(options, context));
  await closure(options, context);
  return { success: true };
} catch (error) {
  context.reportStatus('Error: ' + error);
  return { success: false };  
} 
```

(这只是一个异步函数，用于处理流中的一个元素，所以您需要在某个顶层使用 Promise.all 来进行循环)。

显而易见，日复一日做 RxJS 的高手可以在睡梦中抛出那些 concatMaps 和 mergeMaps，但是对于一个普通的开发人员来说——你能仔细看一下 RxJS 并判断它是否正确吗？是否使用了正确的操作员？如果您推测某个 bug 可能在代码的那个区域，那么您能以多快的速度在头脑中阅读和回放这个流程呢？

很明显，更广泛的 web 开发者社区已经选择了承诺和 async/await，Angular 社区打算走自己的路多久？

(在你问之前，是的，我知道 Angular 本身是建立在 RxJS 之上的——每当我遇到一个断点，调用堆栈中就会有三页这样的内容)。