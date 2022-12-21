# GoLang 中没有整数的最大/最小函数

> 原文：<https://dev.to/petercour/no-max-min-function-for-integer-in-golang-3m97>

[![](img/b8f19247ba34a783ef47948193435477.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wpABNXi4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cobjirtkqvv6qyilq5un.jpg)

核心 golang 模块中没有最小值/最大值功能。为什么？因为浮点数不好比较。

为了避免麻烦，**最小/最大函数**在 **GoLang 数学包**中作为内置函数提供。

```
math.Min(float64, float64) float64
math.Max(float64, float64) float64 
```

这是针对浮点的。如果要比较整数呢？然后你必须自己写函数，

```
func Min(x, y int64) int64 {
 if x < y {
   return x
 }    
 return y
}

func Max(x, y int64) int64 {
 if x > y {
   return x
 }
 return y
} 
```

更多关于 golang:[https://golangr.com](https://golangr.com)