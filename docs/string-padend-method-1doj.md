# 字符串 padEnd()方法

> 原文：<https://dev.to/harbolaez/string-padend-method-1doj>

字符串 padEnd()

了解`padEnd()`字符串方法。这将对当前字符串应用一些填充。填充将从字符串的末尾开始应用。

* * *

```
/**
 * targetLength => The length of the current string that will include the padding.
 *
 * paddingString` => The string to add to the current string as padding.
 * By default it will be empty string.
 */

str.padEnd(targetLength, paddingString); 
```

Enter fullscreen mode Exit fullscreen mode

```
const name = 'My name is Henry';

// We want to add to the end of the name three dots ...
// for that we need to get the length of the string plus dynamic and add 3
const nameLen = name.length;
name.padEnd(nameLen + 3, '.');

/**
 * @return "My name is Henry..."
 */ 
```

Enter fullscreen mode Exit fullscreen mode

添加端板动态的功能:

```
/**
 * @param {String} string
 * @param {Number} extraPad=0
 * @param {String} delimeter=""
 * @return string
 */

const endPad = (str, extraPad = 0, delimeter = '') => {
  if (!str) return '';

  // get string length dynamic to add end pad
  const strLen = str.length;

  // return padded string with the extra pad and it delimeter
  return str.padEnd(strLen + extraPad, delimeter);
};

// example using endPad():
endPad('123', 3, '.');
/**
 * @return "123..."
 */ 
```

Enter fullscreen mode Exit fullscreen mode