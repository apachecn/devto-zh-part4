# 介绍 go-tprof

> 原文：<https://dev.to/gokultp/introducing-go-tprof-1b38>

## 它有什么作用

tprof 是 golang 软件包的测试报告生成器。它解析 go 测试的输出，并生成一个可显示的 html 文档。

[![IMAGE ALT TEXT HERE](img/17355ce2f76fd1e29018d6cdb873edc4.png)](https://www.youtube.com/watch?v=4lzqb0Nic2k)

## 如何使用

执行

```
 go test <pkg name> -v | tprof 
```

例:

```
 go test encoding/... -v | tprof 
```

参观[https://github.com/gokultp/go-tprof](https://github.com/gokultp/go-tprof)获得 tprof