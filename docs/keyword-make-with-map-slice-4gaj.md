# 使用地图和切片制作关键字

> 原文：<https://dev.to/harbolaez/keyword-make-with-map-slice-4gaj>

Go 提供了一个实现哈希表的内置映射类型。

使用关键字`make`有几个好处

*   可以创建*切片*、*地图*和*频道*
*   创建一个预先分配了空间的*地图*或*切片*
*   *切片*可以带镜头！=容量
*   地图的零值是`nil`

### **CRU** 一张地图用使

```
 // instantiate a map implicit
  // this will allocated and initialize a hash map
  myMap := make(map[string]string)

  // adding key and value to map
  myMap["city"] = "Miami"

  // retrieving value base on key
  city := myMap["city"]
  // fmt.Println(city) // == "Miami" 
```

Enter fullscreen mode Exit fullscreen mode

### **CRU** 一片同令

```
 // instantiate a slice implicit
  // this will allocated and initialize a slice with 5 spaces
  mySlice := make([]int, 5) // len(mySlice) == 5

  // you can specify a capacity, pass a third argument to make:
  myCapSlice := make([]int, 3, 5) // len(myCapSlice) == 3, cap(myCapSlice) == 5 
```

Enter fullscreen mode Exit fullscreen mode