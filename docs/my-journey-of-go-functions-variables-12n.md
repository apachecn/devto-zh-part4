# 我的围棋之旅(函数、变量)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-functions-variables-12n>

# 套餐

每个围棋程序都由`packages`组成。每个包需要首先被导入以使用其导出的标识符。
T3 你可以像下面的代码一样导入`packages`。

```
package main

import (
  "fmt" // Imported Package
  "math/rand" // Imported Package
)

func main(){
  fmt.Println("My journey of Go", rand.Intn(10)) //=>My journey of Go 1
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以单独导入每个包。

```
import "fmt"
import "math/rand" 
```

Enter fullscreen mode Exit fullscreen mode

# 导出名称

在 Go 中，如果一个名字以大写字母开头，它就会被导出。例如，`Pi`是从`'math'`包中导出的名称。

```
package main

import "math"

func main(){
  fmt.Println(math.Pi) //=> 3.141592653589793
} 
```

Enter fullscreen mode Exit fullscreen mode

# 功能

一个函数可以有零个或多个参数。您可以像下面的代码一样定义一个函数。

```
func name_of_the_function(arguments) type {
  [content of the function]
} 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记写参数的类型和函数的返回值。

```
package main

import "fmt"

func main(){
  fmt.Println(greetings("John")) //=> Hello John
}

func greetings(name string) string {
  return "Hello" + " " + name
} 
```

Enter fullscreen mode Exit fullscreen mode

### 省略论证的类型

当有两个或多个参数共享同一个类型时，只需编写一次类型。

```
package main

import "fmt"

func main(){
  fmt.Println(add(2, 4)) //=> 6
}

func add(x, y int) int {
  return x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

### 多个结果

一个函数可以返回任意数量的结果。

```
package main

import "fmt"

func main(){
  a, b := multipleArgs("Hello", "World")
  fmt.Println(a, b) //=> World Hello
}

func multipleArgs(arg1, arg2 string)(string, string) {
  return arg2, arg1
} 
```

Enter fullscreen mode Exit fullscreen mode

# 变量

`var`语句声明了一个变量列表。与函数参数列表一样，变量的类型写在变量名之后。

声明时没有初始值的变量被赋予零值。例如，`0`代表数字类型，`false`代表布尔类型，`""`(空字符串)代表字符串。

```
package main

import "fmt"

var var1, var2, var3 bool

func main() {
    var num int
    fmt.Println(num, var1, var2, var3) //=> 0 false false false
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面的代码中看到的，一个`var`语句可以在包或者函数级别使用。

### 带有初始值设定项的变量

一个`var`声明可以包含一个初始化式。

如果有初始化式，变量的类型可以省略。变量将采用初始化器的类型。

```
package main

import "fmt"

func main() {
    var str1 = "ruby"
    var str2, num1, num2 = "go", 2, 4
    fmt.Println(str1,num1, str2, num2) //=>ruby 2 go 4
} 
```

Enter fullscreen mode Exit fullscreen mode

## 短变量声明

你可以使用`:=`在函数中声明一个变量。如果您试图在函数外声明一个带有`:=`的变量，Go 将引发一个错误。

```
package main

import "fmt"

func main() {
    str := "Js"
    num := 4
    boole := true

    fmt.Println(str, num, boole) //=> Js 4 true
} 
```

Enter fullscreen mode Exit fullscreen mode

### 基本类型

下面的代码列出了 Go 的基本类型。

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128 
```

Enter fullscreen mode Exit fullscreen mode

### 类型转换

在 Go 中不能隐式更改变量的类型。如果您尝试重新分配一个类型不同于原始值类型的值，Go 将引发一个错误，如下所示。

```
var i int = 100
var f float64 = i //=> // cannot use i (type int) as type float64 
```

Enter fullscreen mode Exit fullscreen mode

如果你想改变一个变量的类型，你可以像下面这样做。

```
var a uint32 = 1234567890
var b uint8 = uint8(a)
fmt.Println(b)  // 210 
```

Enter fullscreen mode Exit fullscreen mode

# 常数

常量是用关键字`const`声明的。

Go 支持字符型、字符串型、布尔型、数值型常量。

**常量不能使用`:=`语法**声明。

```
package main

import "fmt"

const Name = "Go"

func main() {
    const Num = 1
    const Truth = true
    fmt.Println(Name, Num, Truth) //=> Go 1 true
} 
```

Enter fullscreen mode Exit fullscreen mode