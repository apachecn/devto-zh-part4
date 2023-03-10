# 我的围棋之旅(地图、山脉)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-maps-ranges-blh>

# 地图

`map`是一个无序的键值对集合，其中每个键都是唯一的。

### 宣告地图

您可以使用以下语法定义一个`map`。

```
① make(map[key_type]value_type)
② map[key_type]value_type { key1: value1, key2: value2, ......., keyX: valueX} 
```

Enter fullscreen mode Exit fullscreen mode

### ① make(映射[key_type]value_type)

您可以使用内置的`make()`功能初始化地图。函数`make()`返回一个给定类型的`map`,已经初始化并准备好使用。`Keys`和相应的`values`可以像下面的代码一样添加到地图中。

```
package main

import "fmt"

func main() {
  var map1 = make(map[int]string)

  fmt.Println(map1) //=> map[]

  //Add keys and values to 'map1'
  map1[1] = "Go"
  map1[2] = "Ruby"
  fmt.Println(map1) //=> map[1:Go 2:Ruby]
} 
```

Enter fullscreen mode Exit fullscreen mode

### ②map[key _ type]value _ type { key 1:value 1，key2: value2，.......，keyX: valueX}

通过使用`map literal`，你可以用一些初始数据初始化一个`map`。

```
package main

import "fmt"

func main() {
  var map2 = map[int]string { 1: "Go", 2:"Ruby"}

  fmt.Println(map2) //=> map[1:Go 2:Ruby]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 零地图

如果你用没有初始数据的`map literal`声明一个`map`，就会产生一个`nil-map`。不用说，一个`nil-map`不包含任何数据。此外，任何向`nil-map`添加数据的尝试都会导致运行时错误。

```
package main

import "fmt"

func main() {
  var nil_map map[int]string

  if nil_map == nil {
    fmt.Println("nil") //=> nil
  }

  nil_map[1] = "GO" //=>  assignment to entry in nil map
} 
```

Enter fullscreen mode Exit fullscreen mode

### 修改地图

您可以向`map`添加数据，并像下面的代码一样修改`map`的数据。

```
package main

import "fmt"

func main(){
  var map_ex = make(map[int]string)

  //add keys and  values to a map
  map_ex[1] = "GO"
  map_ex[2] = "Ruby"

  fmt.Println(map_ex) //=> map[1:GO 2:Ruby]

  //modify the data of a map
  map_ex[1] = "Python"
  map_ex[2] = "Java"

  fmt.Println(map_ex) //=> map[1:Python 2:Java]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 从地图中检索值

您可以使用语法`map[key]`检索分配给映射中某个键的值。

```
package main

import "fmt"

func main(){
  var map_ex = make(map[int]string)

  map_ex[1] = "GO"
  map_ex[2] = "Ruby"

  fmt.Println(map_ex[1]) //=> GO 
```

Enter fullscreen mode Exit fullscreen mode

### 检查地图中是否存在关键字

当您使用语法`map[key]`检索分配给给定键的值时，它还会返回一个额外的布尔值。如果键存在于映射中，它返回`true`,如果不存在于映射中，它返回`false`。

```
package main

import "fmt"

func main(){
  var map_ex = make(map[int]string)

  map_ex[1] = "GO"
  map_ex[2] = "Ruby"

  //Trying to retrieve a key that exists in map_ex.
  lang, ok := map_ex[1]

 //Trying to retrieve a key that does not exist in map_ex.
  lang2, ok2 := map_ex[4]

  fmt.Println(lang, ok) //=> GO true
  fmt.Println(lang2, ok2) //=>  false
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您只想检查某个键是否存在，而不检索与该键相关联的值，那么您可以使用一个`_ (underscore)`来代替第一个值。

```
package main

import "fmt"

func main(){
  var map_ex = make(map[int]string)

  map_ex[1] = "GO"
  map_ex[2] = "Ruby"

  _, ok := map_ex[1]
  _, ok2 := map_ex[4]

  fmt.Println(ok) //=> true
  fmt.Println(ok2) //=> false
} 
```

Enter fullscreen mode Exit fullscreen mode

### 从地图中删除一个键

您可以使用内置的`delete(map, key)`功能从地图中删除一个键。如果键不在映射中，那么`delete()`函数不做任何事情。

```
package main

import "fmt"

func main(){
  var map_ex = make(map[int]string)

  map_ex[1] = "GO"
  map_ex[2] = "Ruby"

  delete(map_ex, 1)
  fmt.Println(map_ex) //=> map[2:Ruby]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 范围

`range`关键字在`for loop`中用于迭代数组、切片、通道或映射的项目。当您在一个`array`或一个`slice`上移动时，它返回索引和该索引处的元素。当您在地图上进行测距时，它会返回键和键值。

### 阵列

```
package main

import "fmt"

var arry_ex  = [3]string { "Go", "Ruby", "Python" }

func main(){
  for index, value := range arry_ex {
    fmt.Println(index, value)
    //=> 0 Go
    //=> 1 Ruby
    //=> 2 Python
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 切片

```
package main

import "fmt"

var slice_ex = []string { "Go", "Ruby", "Python" }

  for index, ele := range slice_ex {
    fmt.Println(index, ele)
    //=> 0 Go
    //=> 1 Ruby
    //=> 2 Python
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 地图

```
package main

import "fmt"

var map_ex = map[string]string{ "Name":"Sam", "Gender":"Male" }

  for key, value := range map_ex {
    fmt.Println(key, value)
    //=> Name Sam
    //=> Gender Male
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 跳过索引、键或值

你可以通过分配给`_`来跳过`index`或`value`。如果你只想要索引，完全放下`value`。

### 仅索引

```
package main

import "fmt"

var arry_ex  = [3]string { "Go", "Ruby", "Python" }

func main(){
  for index, _ := range arry_ex {
    fmt.Println(index)
    //=> 0
    //=> 1
    //=> 2
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 仅数值

```
package main

import "fmt"

var arry_ex  = [3]string { "Go", "Ruby", "Python" }

func main(){
  for _, value := range arry_ex {
    fmt.Println(value)
    //=> Go
    //=> Ruby
    //=> Python
  }
} 
```

Enter fullscreen mode Exit fullscreen mode