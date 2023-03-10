# 我的围棋之旅(数组、切片)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-arrays-slices-3mj0>

# 数组

在 Go 中，`array`是固定长度的单一类型元素的编号序列(在 Go 中不能调整`arrays`的大小)。在 Go 中声明一个`array`有几种方法。这里有一些例子。

```
① var name[num]Type
② var name[num]Type = [num]Type { ele1, ele2, elen.... }
③ var name = [...] Type { ele1, ele2, elen... }

//name: The name of the array.
//num: The number of elements that array can contain.
//Type: The type of elements that the array contains. 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码中，我将演示如何用我上面介绍的三种方式声明一个由 3 个字符串组成的`arr`。

### ①var name【num】Type

```
package main

import "fmt"

func main(){
  var arr [3]string
  arr[0] = "Go"
  arr[1] = "Ruby"
  arr[2] = "Javascript"

  fmt.Println(arr) //=> [Go Ruby Javascript]
} 
```

Enter fullscreen mode Exit fullscreen mode

### [t1】②var name[num]type =【num]type { he 1，ele 2，len...我...}](#%E2%91%A1-var-namenumtype-numtype-ele1-ele2-elen-)

```
package main

import "fmt"

func main(){
  var arr [3]string = [3]string { "Go", "Ruby", "Javascript"}

  fmt.Println(arr) //=> [Go Ruby Javascript]
} 
```

Enter fullscreen mode Exit fullscreen mode

### ③ var name = [...类型{ ele1，ele2，elen...}

```
package main

import "fmt"

func main(){
  arr3 := [...]string { "Go", "Ruby", "Javascript" }

  fmt.Println(arr3) //=> [Go Ruby Javascript]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 切片

在 Go 中，`slice`是数组的一段。`Slices`建立在`arrays`的基础上，与`arrays`相比，提供了更多的动力、灵活性和便利性。就像`arrays`，`Slices`是可转位的，也是有长度的。但与`arrays`不同的是，它们可以调整大小。

### 声明一个切片

一个`Slice`可以通过以下方式声明。与声明一个`array`不同，您不必指定`slice`可以包含的元素数量。

```
① var name[]Type
② var name[]Type = []Type { ele1, ele2, elen.... }
③ name :=  Array[start:end]
④ name := make( []Type, len, cap)

//name: The name of the array.
//Type: The type of elements that the array contains.
//Array: An array.
//make: The built-in make function. 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码中，我将演示如何使用我上面介绍的定义`slice`的四种不同方式来声明一个`slice`。

### ① var 名称【类型】

```
package main

import "fmt"

func main() {
  var slice1 []string
  var slice2 = []int { 1, 2, 3 }

  fmt.Println(slice1) //=> []
  fmt.Println(slice2) //=> [1 2 3]
} 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】②var name[]Type =[]Type { ele 1，ele2，elen....}](#%E2%91%A1-var-nametype-type-ele1-ele2-elen-)

```
package main

import "fmt"

func main() {
  var slice2 []string = []string { "Go", "Ruby" }

  fmt.Println(slice2) //=> [Go Ruby]
} 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】③名称:=数组[start:end]](#%E2%91%A2-name-arraystartend)

如上所述，`slice`是`array`的一部分。这意味着我们可以从数组中创建一个切片。下表解释了如何控制从名为`Array`的数组中创建的`slice`元素。

| operate | meaning |
| --- | --- |
| `Array[low:high]` | 从`low`到`high - 1`。 |
| `Array[low:]` | 从`low`到最后一个元素。 |
| `Array[:high]` | 从第一个 elemet 到`high - 1`。 |
| `Array[:]` | 从第一个元素到最后一个元素。 |

```
package main

import "fmt"

func main() {
  arry := [6] int { 1, 2, 3, 4, 5, 6 }

  slice3 := arry[0:2]
  slice4 := arry[0:]
  slice5 := arry[:4]
  slice6 := arry[:]

  fmt.Println(slice3) //=> [1 2]
  fmt.Println(slice4) //=> [1 2 3 4 5 6]
  fmt.Println(slice5) //=> [1 2 3 4]
  fmt.Println(slice6) //=> [1 2 3 4 5 6]
} 
```

Enter fullscreen mode Exit fullscreen mode

### ④名称:=制造([]类型，长度，帽)

```
package main

import "fmt"

func main() {
  slice7 := make([]string, 2, 2)

  fmt.Println(slice7) //=> [ ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 修改切片

正如我多次提到的，一个`slice`是一个`array`的一段，它指向一个底层数组。因此，修改一个`slice`的元素也会修改被引用数组中相应的元素。

```
package main

import "fmt"

func main(){
  arry := [...]string { "Go", "Ruby", "Javascript" }

  slice := arry[:]

  fmt.Println(slice) //=> [Go Ruby Javacript]

  slice[0] = "Python"

  fmt.Println(slice) //=> [Python Ruby Javascript]
  fmt.Println(arry) //=> [Python Ruby Javascript]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 切片的长度和容量

一个`slice`既有一个`length`又有一个`capacity`。

*   切片的长度是它包含的元素数。
*   切片的容量是基础数组中的元素数量，从切片中的第一个元素开始计数。

您可以使用内置函数`len()`和`cap()`找到切片的长度和容量。

```
package main

import "fmt"

func main(){
  arry := [...]string { "Go", "Ruby", "Javascript" }

  slice := arry[:1]

  fmt.Println(len(slice), cap(slice)) //=> 1 3
} 
```

Enter fullscreen mode Exit fullscreen mode

### 追加到切片

向切片添加新元素是很常见的，因此 Go 提供了一个内置的`append`函数。它接受一个切片和您想要添加到`slice`的新元素。然后，它返回一个新的切片，其中包含给定切片中的所有元素以及新元素。

下面的代码展示了`append`函数的结构。

```
func append(slice []Type, new_elements) []Type

//Type: The type of elements that the array contains. 
```

Enter fullscreen mode Exit fullscreen mode

```
package main

import "fmt"

func main(){
  arry := [...]string { "Go", "Ruby", "Javascript" }

  slice := arry[:]
  var new_slice = append(slice, "Java", "Swift", "C")

  fmt.Println(new_slice) //=> [[Go Ruby Javascript Java Swift C]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 无切片

切片的零值为`nil`。一个`nil slice`有一个为 0 的`length`和`capacity`，并且没有底层数组。

```
package main

import "fmt"

func main(){
  var nil_slice []int

  if nil_slice == nil {
    fmt.Println("nil") //=> nil
  } 
```

Enter fullscreen mode Exit fullscreen mode