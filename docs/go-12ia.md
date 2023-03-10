# 在 Go 中设置本地时区

> 原文：<https://dev.to/shiena/go-12ia>

想通过 Go 改变时区时，使用[func (Time ) In](http://golang.org/pkg/time/#Time.In) 。

```
package main

import "time"

const location = "Asia/Tokyo"

func main() {
    loc, err := time.LoadLocation(location)
    if err != nil {
        loc = time.FixedZone(location, 9*60*60)
    }
    now := time.Now().In(loc)
} 
```

像这样想变更时区的处理只要有一个就可以了，但是如果有多个的话就麻烦了，而且如果有变更遗漏的话，很难找到错误的原因。 那个时候会用`time.Local`更改本地的时区。

[http://golang.org/pkg/time/#Location](http://golang.org/pkg/time/#Location)

> ```
> var Local *Location = &localLoc 
> ```
> 
> Local 表示系统的本地时区。

```
package main

import "time"

const location = "Asia/Tokyo"

func init() {
    loc, err := time.LoadLocation(location)
    if err != nil {
        loc = time.FixedZone(location, 9*60*60)
    }
    time.Local = loc
}

func main() {
    now := time.Now()
} 
```

但是，正如[从 time.Time 只取出日期的](http://qiita.com/umisama/items/b50df4888665fc36346e)中指出的那样，[func (Time ) Truncate](http://golang.org/pkg/time/#Time.Truncate) 没有考虑时区。 因为可能还有其他同样的方法，所以请确保确认时间正确。