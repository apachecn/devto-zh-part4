# 使用 Object.keys()和反转字符串。减少？

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-object-keys-and-reduce-1h21>

我们都用`Object.keys()`来获取一个对象的键。标准的东西。那么当你拿到其他东西的钥匙时会发生什么呢？以下是出自[李晨](https://github.com/axtens/Lychen)笔下的 REPL。

```
Lychen>let obj = {word:"bravo",translation:"shabaash"}
[undefined]
Lychen>Object.keys(obj).join()
word,translation 
```

Enter fullscreen mode Exit fullscreen mode

是的，正如我们所料。数组呢？

```
Lychen>let arr = 'aap ka nam kya hai?'.split("  ")
[undefined]
Lychen>Object.keys(arr).join()
0,1,2,3,4 
```

Enter fullscreen mode Exit fullscreen mode

嗯（表示踌躇等）...指数？大概吧。

```
Lychen>const chabian = Object.keys(arr)
[undefined]
Lychen>arr[chabian[3]]
kya 
```

Enter fullscreen mode Exit fullscreen mode

绝对的。

好吧，那绳子呢？

```
Lychen>let str = "aap ki tabiyat kaisi hai?"
[undefined]
Lychen>Object.keys(str).join()
0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24 
```

Enter fullscreen mode Exit fullscreen mode

那段乌尔都语有 24 个字符长。让我们看看它是否真的是指数。

```
Lychen>const indices = Object.keys(str)
[undefined]
Lychen>str[indices[1]]
a
Lychen>str[indices[2]]
p 
```

Enter fullscreen mode Exit fullscreen mode

看起来是这样。

那么数字的 Object.keys 呢？

```
Lychen>const num = 58
[undefined]
Lychen>Object.keys(num).join()

Lychen>typeof Object.keys(num)
object
Lychen>Object.keys(num) === null
False 
```

Enter fullscreen mode Exit fullscreen mode

嗯，所以不管一个数的 Object.keys 是什么，join 返回一个空字符串，typeof 是 Object，它不是 null。

```
Lychen>Object.keys(num) instanceof Array
True 
```

Enter fullscreen mode Exit fullscreen mode

没错。一个空数组。有道理。我想。

所以套路。我把它留给其他人去测试(我已经测试过了，它不是特别有性能):

```
function Bruce_ObjectKeysReduce(string) {
  return Object.keys(string).reduce(function (acc, cur) {
    acc = string[cur] + acc;
    return acc;
  }, "");
} 
```

Enter fullscreen mode Exit fullscreen mode

(稍后)

使用 ES6 表单，可以更进一步简化为

```
const Bruce_ObjectKeysReduce = (string) =>
    Object.keys(string).reduce((acc, cur) => {
      acc = string[cur] + acc;
      return acc;
    }, ""); 
```

Enter fullscreen mode Exit fullscreen mode