# String charAt()方法

> 原文：<https://dev.to/harbolaez/string-charat-method-3e63>

# String charAt()方法

了解`charAt()`字符串方法。它将返回一个由基于其索引定位的单个字符组成的新字符串。

* * *

> 注意:如果没有索引被证明到`charAt()`，它将退回到`0`

使用无索引
的`charAt()`

```
const city = 'Miami';
console.log(city.charAt());
// charAt() using default index => 0
/**
 * @return 'M'
 */ 
```

Enter fullscreen mode Exit fullscreen mode

使用`charAt()`索引

```
const country = 'USA';
country.charAt(1);
/**
 * @return 'U'
 */ 
```

Enter fullscreen mode Exit fullscreen mode

使用带有出站索引
的`charAt()`

```
// note: if the index pass to chartAt(999) is not in the string length
// this will return empty string

const country = 'USA';
country.charAt(999);
/**
 * @return ""
 */ 
```

Enter fullscreen mode Exit fullscreen mode