# 使用反转字符串。从

> 原文：<https://dev.to/bugmagnet/reversing-a-string-using-from-95g>

我很希望这个系列能以`.some`结束，但这里我们再次颠倒了一个字符串。这一次，我们使用的是`Array.from()`。

这是 ES6 版本

```
const Bruce_ArrayFrom = (str, buf = []) => {
  Array.from(str, (itm) =>
    buf.unshift(itm)
  );
  return buf.join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

还有 ES3 版本，感谢[巴别塔](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=MYewdgzgLgBAQgJwK7AKYH0CCCEEMCeAYgiALYwC8MAFNAgDQwBGSAZpTANoC6AlJQD4YAbwBQMGNjz4AdKxKlaUBjQCWUUvwoDxE5mxlJIAC1Wso1dZt28A3LoSooSBGH2sZAKxCqw1AET-dqIAvkA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Ces2016%2Cstage-2%2Cflow&prettier=true&targets=&version=7.5.5&externalPlugins=)

```
var Bruce_ArrayFromES3 = function Bruce_ArrayFromES3(str) {
  var buf =
    arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : [];
  Array.from(str, function(itm) {
    return buf.unshift(itm);
  });
  return buf.join("");
}; 
```

Enter fullscreen mode Exit fullscreen mode

就速度而言，两者都很糟糕，都在榜单上垫底。只有 RegExp 版本较慢。有趣的是，ES3 版本比 ES6 慢(原因不明。)

希望这就是用 JavaScript 反转字符串的结束。像 Perl 一样，[有不止一种方法可以做到这一点](https://en.wikipedia.org/wiki/There%27s_more_than_one_way_to_do_it)，尽管有些方法比其他方法更好。