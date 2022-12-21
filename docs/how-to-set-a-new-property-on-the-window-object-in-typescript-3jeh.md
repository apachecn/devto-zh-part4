# TypeScript:如何在 window 对象上设置新属性

> 原文：<https://dev.to/cogoo/how-to-set-a-new-property-on-the-window-object-in-typescript-3jeh>

在 TypeScript 中合并全局接口时，必须在`global`范围内声明接口。

```
export declare global {
  interface Window {
    // add you custom properties and methods
    YT:(videoID: string, options?: {}) => Observable<YtResponse>
  }
} 
```

TypeScript 支持[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)，前提对于全局对象也是一样。区别在于将接口声明为全局范围的一部分。