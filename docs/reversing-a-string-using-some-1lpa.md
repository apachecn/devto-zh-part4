# 使用反转字符串。一些

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-some-1lpa>

我在看我的一些使用`.some()`的 Google Apps 脚本代码，我想(正如一个人所做的那样)，我想知道这是否可以用来反转一个字符串。

这是我能得到的最纯粹的 ES6 了。

```
const Bruce_SomeReverse = (s, rev = "") => {
  s.split("").some((itm, idx, arr) => {
    rev = rev + arr[arr.length - 1 - idx];
  });
  return rev;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 [Babel](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=FAYw9gdgzgLgBAIQE4FcQFMD6BlMBbdAJXQDd0kp04BeOACigBo4lSa4AiDgShoD44Ab2Bw4UAHRQADgBsAljDpduk_Ojp0FeZnIAmAD2YBDJEl7UBw0aNYl2tuAGo4JpAG1X4meggBzGAAWcAC0cACMIXB6-gC6ANwicAC-3Ak26DAoSBAspAlJQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=) 我已经将其转换为 ES3，如果有人想在那里使用它(就像在 Google Apps 脚本中一样)。

```
"use strict";

var Bruce_SomeReverse = function Bruce_SomeReverse(s) {
  var rev =
    arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : "";
  s.split("").some(function(itm, idx, arr) {
    rev = rev + arr[arr.length - 1 - idx];
  });
  return rev;
}; 
```

Enter fullscreen mode Exit fullscreen mode

性能方面，这种方法非常快速，在前 5 名(使用我的[速度测试器](https://dev.to/bugmagnet/testing-string-reversal-functions-8m6) ):

```
Sarah_ForOf                 986.973 ticks
Bruce_Recursive2            2664.535 ticks
Bruce_SomeReverse_ES3       3085.19 ticks
Bruce_Recursive1            3209.047 ticks
Bruce_SomeReverse           3312.393 ticks 
```

Enter fullscreen mode Exit fullscreen mode

情况似乎经常如此，至少在我的 V8 实例中，ES3 版本更快。