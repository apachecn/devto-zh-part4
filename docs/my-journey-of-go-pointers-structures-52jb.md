# 我的围棋之旅(指针、结构)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-pointers-structures-52jb>

# 指针

### 如何声明一个指针

简而言之，`pointer`是指向另一个值的存储器地址的值。可以使用`*`后跟 Go 中存储值的类型来声明`pointer`。

在下面的代码中，变量`pointer1`是指向`int`类型值的指针，`pointer2`是指向`string`类型值的指针。

```
package main

import "fmt"

func main() {
  var pointer1 *int
  var pointer2 *string

  fmt.Println(pointer1, pointer2) //=> nil nil // A pointer's zero value is `nil`
} 
```

Enter fullscreen mode Exit fullscreen mode

### 如何检索变量的内存地址

在 Go 中，可以使用`&`操作符来检索变量的内存地址。在下面的代码中，`&str`代表变量`str`的内存地址。

```
package main

import "fmt"

func main() {
  var str = "Go"
  fmt.Println(&str) //=> 0xc42000e1e0
} 
```

Enter fullscreen mode Exit fullscreen mode

### 如何使用指针

您可以将变量的内存地址赋给一个`pointer`，就像下面的代码一样。

```
package main

import "fmt"

func main() {
  var pointer1 *int
  var pointer2 *string

  var str = "Go"
  var num = 10

  pointer1 = &num
  pointer2 = &str

  fmt.Println(pointer1, pointer2) //=> 0xc420016078 0xc42000e1e0
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以像下面的代码一样更简洁地声明一个`pointer`。

```
package main

import "fmt"

func main() {
  var num2 = 20
  pointer3 := &num2

  fmt.Println(pointer3) //=> 0xc420016080
} 
```

Enter fullscreen mode Exit fullscreen mode

`pointer1`和`pointer3`只能存储`int`类型值的内存地址，`pointer2`只能存储`string`类型值的内存地址。

```
package main

import "fmt"

func main() {
    var pointer1 *int
    var str = "Go"

    pointer1 = &str //=> cannot use &str (type *string) as type *int in assignment
} 
```

Enter fullscreen mode Exit fullscreen mode

### 通过指针访问变量的值

`*`操作符表示指针的基础值。通过使用此功能，您可以通过指针设置、读取和更改变量的值。

```
package main

import "fmt"

func main() {
  var pointer1 *int
  var num = 10

  pointer1 = &num

  fmt.Println(*pointer1) //=> 10 //read var1 through the pointer.
  *pointer1 = 20 //change the value of var1 through the pointer.
  fmt.Println(num) //=> 20 //the new value of num
} 
```

Enter fullscreen mode Exit fullscreen mode

# 结构

Go 中的`structs`概念有点类似于面向对象编程语言中的`classes`概念。一个`struct`是一个类型化的字段集合。它们对于将数据组合在一起形成记录很有用。

### 如何声明一个结构体

你可以使用`type`和`struct`关键字来声明一个`struct`。

```
package main

import  "fmt"

type person struct {
  first_name string
  age int
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`person` struct 包含`first_name`和`age`作为它的`fields`。

### 结构实例化的不同方式

实例化一个`struct`有多种方法，我将在这里演示以下 4 种方法。

#### `var`关键字和点符号

一个`struct`使用一个`.`来访问存储在字段中的值。

```
package main

import "fmt"

type person struct {
  first_name string
  age int
}

func main() {
  var mike person
  mike.first_name = "Mike"
  mike.age = 20

  fmt.Println(mike.first_name, mike.age) //=> Mike 20
} 
```

Enter fullscreen mode Exit fullscreen mode

#### [T3`var`关键字和`:=`运算符](#the-raw-var-endraw-keyword-and-raw-endraw-operator)

下面两组代码使用`var`和`:=`说明了结构实例化。

```
package main

import "fmt"

type person struct {
  first_name string
  age int
}

func main() {
  bob := person{ "Bob", 30 }

  fmt.Println(bob.first_name, bob.age) //=> Bob 30
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以显式引用某个字段并为其赋值。

```
package main

import "fmt"

type person struct {
  first_name string
  age int
}

func main() {
  sam := person{ age: 40, first_name: "Sam" }

  fmt.Println(sam.first_name, sam.age) //=> Sam 40
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用`new`关键字

以下代码显示了使用`new`关键字的结构实例化。

```
package main

import "fmt"

type person struct {
  first_name string
  age int
}

func main() {
  jen := new(person)

  jen.first_name = "Jennifer"
  jen.age = 10

  fmt.Println(jen.first_name, jen.age) //=> Jennifer 10
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结构和指针

`Struct fields`可以通过结构指针访问。

```
package main

import "fmt"

type person struct {
  first_name string
  age int
}

func main() {
  var mike person

  s_pointer := &mike

  s_pointer.first_name = "Mike"
  s_pointer.age = 20
  fmt.Println(mike) //=> {Mike 20}
} 
```

Enter fullscreen mode Exit fullscreen mode

# 方法

前面提到过，围棋没有`classes`。但是，您可以在结构类型上定义方法。

A `method`是带有特殊`receiver`自变量的函数。

### 如何定义一个方法

您可以像下面的代码片段一样声明一个方法。

```
func(receiver receiver_type) name_of_function(arg) type_of_return_value {
   //content of the function
} 
```

Enter fullscreen mode Exit fullscreen mode

在下面的例子中，`intro`方法有一个名为`one_person`的`person`类型的`receiver`。

```
package main

import "fmt"

type person struct {
    first_name string
    age int
}

func(one_person person) intro(arg string) string {
    return "Hello I'm" + " " + one_person.first_name + " " + arg     
}

func main() {
    mike := person{ "Mike", 20 }
    fmt.Println(mike.intro("!")) //=>Hello I'm Mike !
} 
```

Enter fullscreen mode Exit fullscreen mode

### 指针接收器

你可以用指针接收器声明`methods`。这意味着`receiver_type`拥有某种类型`Type`的字面语法`*Type`。

`pointer receivers`和正常的`value reveivers`有几个区别。你应该使用`pointer receivers`而不是`value receivers`的一种情况是当你想改变方法中`receiver`的状态时。

使用`pointer receivers`，你可以像下面的代码一样修改(读/写)`receiver`。

```
package main

import "fmt"

type Num struct {
    x, y int
}

func (p Num) stay() {
    p.x = 10
    p.y = 20
}

func (p *Num) modify() {
    p.x = 10
    p.y = 20
}

func main() {
    num_one := Num {0, 0}
    num_one.stay()

    fmt.Println(num_one) //=> {0 0}

    num_one.modify()

    fmt.Println(num_one) //=> {10 20}
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面的代码中看到的，方法`stay()`没有改变`Num` struct 的值。另一方面，方法`modify()`改变了`Num`结构的值。

如果你想了解更多关于`pointer receivers`和`value receivers`的区别，可以查看[这里](https://flaviocopes.com/golang-methods-receivers/)

# 类型嵌入

Go 不提供典型的、类型驱动的子类化概念，但是它有能力通过在结构或接口中嵌入类型来“借用”部分实现。

```
package main

import "fmt"

type Person struct {
   first_name string
}

func (a Person) name() string{ //Person typed method
    return a.first_name
}

type User struct {
     Person
}

func (a User) name() string { //User typed method
    return a.first_name
}

func main(){
  bob := Person{"Bob"}
  mike := User{}
  mike.first_name = "Mike"

  fmt.Println(bob.name()) //=> Bob
  fmt.Println(mike.name()) //=> Mike
} 
```

Enter fullscreen mode Exit fullscreen mode