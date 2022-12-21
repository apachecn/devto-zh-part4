# Golang 快速入门指南

> 原文：<https://dev.to/delph/quick-beginner-s-guide-to-golang-4bkj>

# 去吧

Golang，或 Golang，是一种静态类型的编译编程语言，最初于 2007 年由 Google 开发。它提供垃圾收集、类型安全、动态类型功能和其他高级内置类型，如可变长度数组和键值映射。

## 典型围棋文件的格式，`main.go`

```
package main // declare package name, this line is mandatory

import "fmt" // import packages

func main(){
    fmt.Println("Hello World")
}  // every Go program has at least one function, which is main(). 
```

要执行它，运行
`go run main.go`

或者`go build`制作一个可执行文件

## 变量

方法一:

```
func main(){
    var name string = "Mary"
    var age int = 40
} 
```

方法二(类型推断):

```
func main(){
    name := "Mary"
    age := 40
} 
```

## 如果...其他...

```
func main(){
    age := 50
    if age > 50 {
        fmt.Println("More than 50")
    } else if age < 50 {
        fmt.Println("Less than 50")
    } else {
        fmt.Println("50 Years Old")
    }
} 
```

## 数组

Go 中的数组是固定大小的，可以像这样创建`var variable_name [SIZE] variable_type`

```
func main(){
// declaring arrays
    var a [5] string // ["", "", "", "", ""]
// initializing arrays
    fruits := [5]string{"banana", "orange", "apple", "kiwi", "mango"} 
    // ["banana", "orange", "apple", "kiwi", "mango"]
} 
```

## 切片

对数组进行抽象以创建动态大小的数组

```
func main(){
    ages := []int{10, 20, 35, 40, 50} // [10, 20, 35, 40, 50]
    ages = append(ages, 60) // adds an element. original slice is not modified, a new one is returned
} 
```

## 地图

地图包含键值对，很像 Python 中的字典。要创建地图，使用内置的“制作”功能

```
func main(){
    // create map
    ages := make(map[string]int)

    ages["Mary"] = 40
    ages["Pete"] = 30
    ages["Nick"] = 25

   // to delete something from map, use delete function
   delete(ages, "Pete")
} 
```

## 循环往复

Golang 中唯一的循环类型是“for”循环

```
func main(){
    for i := 0; i < 5; i++ {
        fmt.Println(i)
    }   

    // using a 'while loop' style
    j := 0

    for j < 5 {
       fmt.Println(j)
       j++
   }

    // printing items in an array using range
    fruits := []string{"banana", "orange", "grape"}

    for index, value := range fruits {
        fmt.Println("index:", index, "value:", value)
        /* index: 0 value: banana
           index: 1 value: orange
           index: 2 value: grape */               
    }

    // printing items in a map using range
    ages := make(map[string]int)

    ages["Mary"] = 40
    ages["Pete"] = 30
    ages["Nick"] = 25

    for key, value := range fruits {
        fmt.Println("name:", key, "age:", value)
        /* name: Mary age: 40
           name: Pete age: 30
           name: Nick age: 35 */               
    }
} 
```

## 功能

```
func main(){
    result := sum(10, 5)
    fmt.Println(result) // 15
}

// simple function, return type is specified after parameter list
func sum(int a, int b) int {
    return a + b
} 
```

Go 函数可以返回多种类型。这对于错误处理很有用，因为 Go 不像其他语言那样有异常。

```
import (
    "fmt"
    "errors"
    "math"
)

func main(){
    result, err := sqrt(4)
    if err != nil {
        fmt.Println(err)
    } else {
        fmt.Println(result)
    }
}

func sqrt(x float64) (float64, error) {
    if x < 0 {
        return 0, errors.New("Undefined for negative number")
    }
    return math.Sqrt(x), nil
} 
```

## 结构

结构是字段的集合，因此您可以将事物组合在一起以创建更具逻辑性的类型。

```
type person struct {
    name string
    age int
}

func main(){
    p := person{name: "Nick", age: "25"}

    // use dot notation to access a member of a struct
    fmt.Println(p.age) // 25
} 
```

## 指针

每个变量都存储在内存中，我们可以通过使用&运算符来获取变量的内存地址。

```
func main(){
    i := 10
    fmt.Println(&i) // prints memory address
    inc(i)
    fmt.Println(i) // 10

}

func inc(x int) {
    x++
} 
```

在上面的例子中，`inc`函数不会做任何事情，因为`i`是通过值复制的，所以`inc`函数得到了`i`的副本，而原始版本没有被修改。

但是，如果我们传递一个指向变量的指针，那么这个函数就能够查看内存引用的值，并修改原始版本。

我们可以通过在类型前面加一个星号(*)来接受指针，并使用一个&符号来发送指针。在`inc`函数中，我们需要添加另一个星号来取消对指针
的引用

```
func main(){
    i := 10
    inc(&i)
    fmt.Println(i) // 11

}

func inc(x *int) {
    *x++
} 
```