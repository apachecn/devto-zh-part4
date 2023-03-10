# 关于 Grok 的一些事情

> 原文：<https://dev.to/zex/something-about-grok-4ih4>

这里是 Grok，一个在 ELK stack 中使用的过滤器插件。它将非结构化日志转换为结构化日志。

Grok 是建立在正则表达式之上的。语法是`%{SYNTAX:SEMANTIC}`。例如:

一条日志线

```
10.0.1.13 GET /home HTTP/2.0 200 13969 
```

在 logstash 配置文件中定义的过滤器如下

```
filter {
  grok {
    match => {
      "message" => '%{IPORHOST:ip} \"%{WORD:method} %{DATA:request} HTTP/%{NUMBER:httpversion}" %{NUMBER:response:int} (?:-|%{NUMBER:bytes:int})'
    }
  }
} 
```

对数线通过滤波器，产生结果

```
ip: 10.0.1.13
method: GET
request: /home
response: 200
bytes: 13969 
```

通过结构化数据，您可以在使用 Elasticsearch 时按特定领域进行搜索。

## 调试器的威胁

如果你不确定这个模式在你的日志中是否有效，这里有一些 grok 调试器可以帮助你验证这个模式。

*   [调试器的威胁](https://grokdebug.herokuapp.com)
*   [测试裂缝模式](https://grokconstructor.appspot.com/do/match)

使日志处理更容易。:D

## 亦见

5 个日志过滤插件
[Oniguruma](https://github.com/kkos/oniguruma)

* * *

> 这个行话文件将自己描述为“黑客的字典”,并以这个名字发表了三次，它将 grok 置于一个编程环境中:
> 
> 当你声称“搜寻”某种知识或技术时，你是在断言，你不仅仅是以一种超然的工具性方式学到了它，它已经成为你的一部分，你身份的一部分。例如，说你“知道”LISP 仅仅是断言如果必要的话你可以用它来编码——但是说你“精通”Lisp 是声称你已经深深地融入了这种语言的世界观和精神，暗示着它已经改变了你对编程的看法。相比之下，禅宗是一种类似的超自然的理解，被体验为一个短暂的闪光。
> -维基百科