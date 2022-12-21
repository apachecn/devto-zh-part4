# 类型脚本类型声明

> 原文：<https://dev.to/bugmagnet/typescript-type-declarations-447h>

嘿，哇，您可以在名称空间中嵌入名称空间！

```
 var resp = CSEncoding.Default.GetString(rBody, 0, rBody.Length); 
```

Enter fullscreen mode Exit fullscreen mode

我在想如何把它放到`external.d.ts`中，这是有效的:

```
declare namespace CSEncoding {
    declare namespace Default {
        function GetString(s:string,n1:number,n2:number):string;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**后来**

哦，也许你不能。刚刚重新加载文件，现在我得到以下错误:

```
A 'declare' modifier cannot be used in an already ambient context. 
```

Enter fullscreen mode Exit fullscreen mode