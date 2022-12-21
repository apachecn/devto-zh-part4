# 以奇怪的方式使用数组原型#1

> 原文：<https://dev.to/bugmagnet/using-array-prototypes-in-strange-ways-26k9>

Greg Bulmash 最近写了一篇名为[JavaScript Bubble Sort in the less lines](https://dev.to/yiddishninja/javascript-bubble-sort-in-the-fewest-lines-5e5n)的帖子。这让我开始思考如何绕过`.forEach()`坚持迭代每一项的做法。我仍然没有找到解决方案，但是我发现了一个有趣的方法来使用`.reduce()`对数组进行排序！

这里的代码是 V8 JavaScript，但是有一些由 C#使用我的 [Lychen](https://github.com/axtens/Lychen) 项目提供的基础设施。

第一步是从命令行提取测试数组的长度，默认为 10。

```
const aLen = CSSettings.ContainsKey("/LENGTH") ? parseInt(CSSettings("/LENGTH"), 10) : 10; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们初始化数组:分配空间，用零填充，然后映射一些随机数。

```
const array = Array(aLen).fill(0).map(function (item, index) {
  return Math.floor(Math.random() * (index * aLen))
}); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，主要代码块:

首先检查累加器(`acc`)，看看它有多少项。如果为零，则推入当前值(`cur`)。

如果为 1，则将 acc 中的值与 cur 进行比较，如果 cur 小于或等于，则将它移至 acc。否则就推。

如果 acc 大于 1，则检查 cur 是否小于或等于 acc 中的第一项，在这种情况下，unshift 进入 acc，或者如果大于 acc 中的最后一项，则将其推入 acc。

如果该值既没有被压入也没有被移出，那么通过 acc 向后迭代，直到 cur 大于 acc 值。向偏移加 1，并在该点将曲线拼接到 acc 中。

```
const sorted = array.reduce(function (acc, cur, idx, src) {
    if (acc.length === 0) {
      acc.push(cur);
    } else {
      if (acc.length === 1) {
        if (cur <= acc[0]) {
          acc.unshift(cur)
        } else {
          acc.push(cur);
        }
      } else {
        if (cur <= acc[0]) {
          acc.unshift(cur);
        } else {
          if (cur > acc[acc.length - 1]) {
            acc.push(cur);
          } else {
            for (let i = acc.length - 2; i >= 0; i--) {
              if (cur > acc[i]) {
                acc.splice(i + 1, 0, cur);
                break;
              }
            }
          }
        }
      }
    }
    return acc;
  },
    []); 
```

Enter fullscreen mode Exit fullscreen mode

最后，显示结果。

```
CS.System.Console.WriteLine(JSON.stringify(array));
CS.System.Console.WriteLine(JSON.stringify(sorted)); 
```

Enter fullscreen mode Exit fullscreen mode

这不是对`.sort()`的替代。我没有做任何计时，但我不指望它赢得任何速度奖。只是...良好的...有意思。