# 我的围棋之旅(界面)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-interfaces-5hnm>

# 界面在走

在 Go 中，`interface`是一组方法签名。接口类型的变量可以保存实现这些方法的任何值。

# 基础知识

您可以像下面的代码一样声明一个`interface`。它基本上只是一个方法列表。

```
type NAME_OF_INTERFACE interface {
    method_name1(return_type1)    
    method_name2(return_type2)
    ..........
    method_namex(return_typex)
} 
```

Enter fullscreen mode Exit fullscreen mode

任何提供在接口中命名的方法的类型都被视为该接口的实现。不需要显式声明。

# 清空界面

不指定任何方法的接口类型被称为`empty interface`。它可以定义为下面的代码。

```
interface {} 
```

Enter fullscreen mode Exit fullscreen mode

空接口类型的变量可以保存任何类型的值。

```
package main

import "fmt"

func main(){
  var intface interface{}
  intface = 1
  fmt.Println(intface) //=> 1

  intface = "string" //=> string
  fmt.Println(intface)

  intface = []string{"Go", "Ruby", "JS"}
  fmt.Println(intface) //=>[Go Ruby JS]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 界面值

一个`interface value`被表示为一对**具体值**和一个**动态类型**。

```
[Value, Type] 
```

Enter fullscreen mode Exit fullscreen mode

可以用`%v`打印具体值，用`%T`打印动态类型。

```
package main

import "fmt"

func main(){
  var intface interface{}
  intface = 1
  fmt.Printf("%v %T\n", intface, intface) //=>1 int

  intface = "string"
  fmt.Printf("%v %T\n", intface, intface) //=> string string

  intface = []string{"Go", "Ruby", "JS"}
  fmt.Printf("%v %T\n", intface, intface) //=> [Go Ruby JS] []string
} 
```

Enter fullscreen mode Exit fullscreen mode

# 式断言

`type assertion`提供了对接口值的底层具体值的访问。

```
concrete_value := Interface_value.(TYPE) 
```

Enter fullscreen mode Exit fullscreen mode

上面的语句断言`Interface_value`持有具体类型`TYPE`，并将底层的`TYPE`值赋给`variable`。

检查一个接口值是否持有特定类型，`type assertion`可以返回两个值。`underlying value`和报告断言是否成功的`boolean value`。

```
concrete_value, ok := Interface_value.(TYPE) 
```

Enter fullscreen mode Exit fullscreen mode

```
package main

import "fmt"

func main(){
  var intface interface{} = "Hello World"

  t := intface.(string)
  fmt.Println(t) //=> Hello World

  t2, ok := intface.(string)
  fmt.Println(t2, ok) //=> Hello World true

  t3, ok := intface.(float64)
  fmt.Println(t3, ok) //=> 0 false
} 
```

Enter fullscreen mode Exit fullscreen mode

# 型开关

`type switch`是一个允许几个类型断言串联的结构。

你可以像下面的代码一样声明一个`type switch`。

```
switch v := x.(type) {
    case TYPE1:
    //here v has TYPE1
    case TYPE2:
    //here: v has TYPE2
    ...
    default: ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码中，switch 语句测试接口值`i`是否保存类型为`int`或`string`的值。在`int`和`string`的每一种情况下，变量`v`将分别为`int`或`string`类型，并保存由`i`保存的值。

在默认情况下(没有匹配)，变量`v`与`i`具有相同的接口类型和值。

```
package main

import "fmt"

func typeSwitch(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("Twice %v is %v\n", v, v*2)
    case string:
        fmt.Printf("%q is %v bytes long\n", v, len(v))
    default:
        fmt.Printf("I don't know about type %T!\n", v)
    }
}

func main() {
    typeSwitch(21) //=> Twice 21 is 42
    typeSwitch("Hello World") //=> "Hello World" is 11 bytes long
    typeSwitch(true)//=> I don't know about type bool!
} 
```

Enter fullscreen mode Exit fullscreen mode

# 实现接口

`Interfaces`可以实现为结构上的方法，如下面的代码。

```
package main

import "fmt"

 type People interface {
  intro()
 }

 type Person struct {
   name string
 }

func (rarg Person) intro() string{
  return "Hello" + " " + "I'm" + " " + rarg.name
}

func main() {
  bob := Person{"Bob"}
  fmt.Println(bob.intro()) //=> Hello I'm Bob
} 
```

Enter fullscreen mode Exit fullscreen mode