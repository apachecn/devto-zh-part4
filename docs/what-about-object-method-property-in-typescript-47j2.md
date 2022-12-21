# 对象呢。方法()。Typescript 中的属性？

> 原文：<https://dev.to/bugmagnet/what-about-object-method-property-in-typescript-47j2>

CSEncoding。Default.GetString()是

```
declare namespace CSEncoding {
    declare namespace Default {
        function GetString(s: string, n1: number, n2: number): string;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是`CSProcess.GetCurrentProcess().Id`呢？GetCurrentProcess 是返回带有属性的对象的函数？是这么读的吗？？