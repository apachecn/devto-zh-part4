# Golang 的 In_array 替代。在“映射，数组，切片”中搜索值。

> 原文：<https://dev.to/ashkan90/inarray-alternative-for-golang-searching-value-in-map-array-slice-56c7>

嗨，我现在开始打高尔夫了。我从 PHP 转到了 Golang，我可以说 Golang 太美了。直到我决定写一个包，我才知道没有搜索功能存在。基本上在 PHP 中，我们可能会像这样实现我们“in_array”替换；

```
if (is_array($x)) {
   foreach ($x as $v) {
      if ($v == $searchVal) { return true; }
   }
} 
```

当然，这种替代方法也可以用于非多维领域。在 Golang 中，我们不能轻易决定变量的类型。有一个叫“反射”的包。
Reflect 包不仅仅用于获取任何变量的类型。它的文档是这里的。

如果我们知道我们要做什么，我们就能做到！
我们来写 func 原型；

```
func In_array(search interface{}, array interface{}) bool 
```

没有人想限制搜索值的类型，因为我们想搜索任何类型，任何时间。因此，我使用了“接口{}”。
和搜索一样，传入值(数组)也必须是接口{}。

```
import (
    "reflect"
)

func In_array(search interface{}, array interface{}) bool {

    val := reflect.ValueOf(array)
    val = val.Convert(val.Type())

    typ := reflect.TypeOf(array).Kind()

    switch typ {
    case reflect.Map:
        s := val.MapRange()

        for s.Next() {
            s.Value().Convert(s.Value().Type())
            for i := 0; i < s.Value().Len(); i++ {
                if deep {
                    if reflect.DeepEqual(search, s.Value().Index(i).Interface()) {
                        return true
                    }
                } else {
                    str := s.Value().Index(i).String()
                    if strings.Contains(str, search.(string)) {
                        return true
                    }
                }
            }
        }
    case reflect.Slice, reflect.Array:
        for i := 0; i < val.Len(); i++ {
            if reflect.DeepEqual(search, val.Index(i).Interface()) {
                return true
            }
        }
    }

    return false
} 
```

我们用这行代码检测值的类型；

```
reflect.TypeOf(array).Kind() 
```

这段代码，准备‘反映’包的常量，它们可能是；

```
Map,
Array,
Slice,
String,
.... 
```

如果值是 Map
，我们应该通过 MapRange()迭代该值(别忘了，我们不是在做递归的事情。这只是一个想法)
然后我们循环当前索引的值，并检查给定的搜索值是否等于 map 的值。
和嵌套循环一样。

我使用 DeepEqual()是因为，
Docs: `DeepEqual reports whether x and y are “deeply equal,” defined as follows. Two values of identical type are deeply equal if one of the following cases applies. Values of distinct types are never deeply equal.` 

还有流浪汉！这样既简单又高效。我们可以用这个来测试。

```
rules := map[string][]string{
    "name":    {"must-be-string", "min-len-20"},
    "surname": {"must-be-string", "min-len-15"},
}
in_array("must-be-string", rules) // true 
```

因为深层的逻辑问题，做了一些改变。我更新了代码，所以你可以轻松地使用它。

下面是源代码，

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿什坎 90 ](https://github.com/ashkan90) / [戈兰阵](https://github.com/ashkan90/golang-in_array)

### golang 的 PHP in_array 替代方案

<article class="markdown-body entry-content container-lg" itemprop="text">

# 阵列中的 golang

golang 的 PHP in_array 替代方案

它经过了测试

*   "搜索地图数组中的任何类型"
*   []"递归映射中的任何类型(无深度)"
*   "数组中的任何类型"
*   "切片中的任何类型"

</article>

[View on GitHub](https://github.com/ashkan90/golang-in_array)