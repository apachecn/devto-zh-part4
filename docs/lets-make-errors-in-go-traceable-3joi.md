# 让错误变得可追踪

> 原文：<https://dev.to/5anthosh/lets-make-errors-in-go-traceable-3joi>

我写了一个模块让 Go 可追踪

只是在这里附上自述:-)
[哎呀](https://www.github.com/5anthosh/oops)

# 哎呀🙊

oops 使 Go 中的错误可追踪。当你从函数中返回错误时，它提供回溯函数来获得更多信息

> 约定说错误要么被处理(不管那意味着什么)，要么被返回给调用者。但是随着抽象程度的提高，很难找到错误是从哪里产生的
> ,所以可追踪函数返回了包含更多信息的错误

## 举例

```
package main

import (
    "errors"

    "github.com/5anthosh/oops"
)

func main() {
    err := func1().(*oops.Error)
    println(err.Skip(1).Error())
}

func func1() error {
    return func2()
}

func func2() error {
    return func3()
}

func func3() error {
    return oops.T(errors.New("dummy one")).Info("this is just testing")
} 
```

### 运行程序

```
$ go run test.go
🔴  Error : dummy one
ℹ️   Info  : this is just testing
         at main.func3 line 23 Desktop/Files/test.go
         at main.func2 line 19 Desktop/Files/test.go
         at main.func1 line 15 Desktop/Files/test.go
         at main.main line 10 Desktop/Files/test.go 
```