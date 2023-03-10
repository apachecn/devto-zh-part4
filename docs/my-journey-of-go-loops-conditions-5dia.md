# 我的围棋之旅(循环、条件)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-loops-conditions-5dia>

# 为

`For`是 Go 唯一的循环构造。基本`for loop`由下面列出的三个部分组成。

*   `init statement`:在第一次迭代之前执行。(它通常用于定义一个只能在`for`语句范围内使用的变量。)

*   `condition`:每次迭代前评估。(如果为真，循环体执行。否则，循环终止。)

*   `post statement`:在每次迭代结束时执行。

Go 的`for loop`的基本语法是这样的。

```
 for init statement; condition; post statement {
    //content of the loop
  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦条件评估为`false`，循环终止迭代。

```
package main

import "fmt"

func main(){
  var num = 0

  for i := 0; i < 5; i++ {
      num += i
  }

  fmt.Println(num) //=> 10
} 
```

Enter fullscreen mode Exit fullscreen mode

`init`和`post`语句可以省略。当这些语句被省略时，Go 的`for loop`的行为就像 Javascript/Java/C 的`while loop`。

```
package main

import "fmt"

func main() {
    num := 1
    for num < 100 {
        num += num
    }
    fmt.Println(num) //=> 128
} 
```

Enter fullscreen mode Exit fullscreen mode

如果省略`condition`，它会无限循环。

```
package main

func main() {
    for {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 如果

Go 的`if`语句的基本语法与其`for`语句类似。

```
package main

import "fmt"

func main(){
  var num = 0

  fmt.Println(if_statement(num)) //=> The value is 0
}

func if_statement(arg int) string{
  if arg == 0 {
    return "The value is 0"
  } else {
    return "The value is not 0"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像`for`语句一样，`if`语句可以从一个在条件之前执行的短语句开始。

```
package main

import "fmt"

func condition(arg string) string {
    if v := "Go"; arg == v {
        return "This is Go"
    } else {
        return "This is not Go"
    }
}

func main() {
    lang := "Ruby"
    fmt.Println(condition(lang)) ///=> This is not Go
} 
```

Enter fullscreen mode Exit fullscreen mode

# 开关

`switch`语句是编写一系列`if - else`语句的一种更短的方式。一个`switch`语句只运行满足条件的第一个案例，而不是后面的所有案例。

```
package main

import "fmt"

func main(){
  lang := "Go"

  switch lang {
    case "Ruby":
        fmt.Println("This is Ruby")
    case "Go":
        fmt.Println("This is Go")
    default:
        fmt.Println("This is something else")
    }
    //=> This is Go
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以将`init statement`与`switch`语句一起使用。

```
package main

import "fmt"

func main(){

  switch lang:= "Go"; lang {
    case "Ruby":
        fmt.Println("This is Ruby")
    case "Go":
        fmt.Println("This is Go")
    default:
        fmt.Println("This is something else")
    }
    //=> This is Go
} 
```

Enter fullscreen mode Exit fullscreen mode

`switch`无条件同`switch true`。

```
package main

import (
    "fmt"
    "time"
)

func main() {
    t := time.Now()
    switch {
    case t.Hour() < 12:
        fmt.Println("Good morning!")
    case t.Hour() < 17:
        fmt.Println("Good afternoon.")
    default:
        fmt.Println("Good evening.")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 延期

一个`defer`语句延迟一个函数的执行，直到周围的函数返回。

例如，由于下面例子中给出的`differ`语句，`fmt.Println("Hello")`在`fmt.Println("World")`之后执行。

```
package main

import "fmt"

func main(){
    defer fmt.Println("Hello")

    fmt.Println("World")
    //=> World
    //   Hello
} 
```

Enter fullscreen mode Exit fullscreen mode

延迟的函数调用按`last-in-first-out`顺序执行。这意味着给定的第一个函数`defer`将最后执行。

```
package main

import "fmt"

func main(){
    defer fmt.Println(1)
    defer fmt.Println(2)
    defer fmt.Println(3)
    //=> 3
    //   2
    //   1
} 
```

Enter fullscreen mode Exit fullscreen mode