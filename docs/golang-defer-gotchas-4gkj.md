# Golang:推迟抓到你并攻击

> 原文：<https://dev.to/bzon/golang-defer-gotchas-4gkj>

## 样本程序

```
package main

import "fmt"

func main() {
    p := person{"john", 15}
    defer printPerson(p)
    updateAge(&p, 20)
}

type person struct {
    name string
    age  int
}

func printPerson(p person) {
    fmt.Printf("Name: %s, Age: %d", p.name, p.age)
}

func updateAge(p *person, newAge int) {
    p.age = newAge
} 
```

运行程序。

```
$  go run main.go
 Name: john, Age: 15 
```

我以为是 20，但是我得到了 15。这是因为*“延迟函数的参数(如果函数是方法，则包括接收者)是在延迟执行时计算的，而不是在调用执行时计算的。”*。参见[有效的过站延迟](https://golang.org/doc/effective_go.html#defer)。

这意味着`defer printPerson(p)`被调用的时间，即`p`已经被*确定为*。

## 工作区

现在，将`printPerson(p)`放在一个匿名函数下。

```
 func main() {
    p := person{"john", 15}
    // defer printPerson(p)
    defer func() {
        printPerson(p)
    }()
    updateAge(&p, 20)
} 
```

运行程序。

```
$  go run main.go
 Name: john, Age: 20 
```

为什么？文档只说*当延迟执行*时，延迟函数的参数被求值。在上面修改过的代码中，这个延迟函数是匿名函数，而不是函数`printPerson`。黑！