# 你真的了解 JS 吗？

> 原文：<https://dev.to/jgaguiar16/do-you-really-know-js-3j6b>

嗨！这是我的第一篇帖子，我想做一个简单的 javascript 测试，但我不会给你答案，想法是你把你的答案放在评论里，讨论哪些是正确的，哪些是错误的，所以让我们开始吧！

1.-这个表达式的结果是什么？(或多个)

```
 ["1", "2", "3"].map(parseInt) 
```

a) ["1 "，" 2 "，" 3 "]
b)【1，2，3】
c)【1，NaN，NaN】
d)其他(给出你的答案)

2.-这个表达式的结果是什么？(或多个)

```
 [typeof null, null instanceof Object] 
```

a) ["object "，true]
b) ["object "，false]
c) ["object "，null]
d)其他(给出你的答案)

3.-这个表达式的结果是什么？(或多个)

```
 var x = [].reverse;
                  x(); 
```

a)窗口
b)其他
c)未定义
d)真

4.-这个表达式的结果是什么？(或多个)

```
 2 == [[[2]]] 
```

a)假
b)真
c)错误
d)未定义

5.-这个表达式的结果是什么？(或多个)

```
 var a = /123/,
                   b = /123/;
                   a == b
                   a === b 
```

a)真，真
b)真，假
c)假，真
d)其他

6.-这个表达式的结果是什么？(或多个)

```
 var a = {}, b = Object.prototype;
          [a.prototype === b, Object.getPrototypeOf(a) === b] 
```

a)【真，假】
b)【真，真】
c)【假，假】
d)以上都不是