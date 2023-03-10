# 不一致的 json 解组

> 原文：<https://dev.to/leofigy/inconsistent-json-unmarshal-37m1>

# 普通日常任务

Go 在标准库中提供了一个 json 包。然而，我们每天在工作中面临的一个常见情况是我们的输入不一致，这意味着一个 json 条目可能有不同的数据类型。

考虑下面的输入 jsons

```
# good input
{
    "name": "Sam smith",
    "age" : 34
}
# lazy input 
{
    "name": "Sam smith stringer",
    "age" : "41"
} 
```

可以看到,“年龄”字段有两种不同的类型:字符串和数字。
Go 提供了处理这种情况的窍门；所以食谱是

*   用正确的类型编写 go 结构
*   为您的结构创建一个别名，以避免递归解组
*   用您嵌入的别名解组到您的中间结构
*   选择结构的属性，并将其解析为预期的类型

### 完整代码片段

```
package main

import (
    "fmt"
    "encoding/json"
    "strconv"
)

type InputJSON struct {
    Name string `json:"name,omitempty"`
    Age  int    `json:"age,omitempty"`
}

/* Alias to avoid calling implicit UnmarshalJSON of your original 
   struct 
*/
type Alias InputJSON

/* write your Unmarshal function using an intermediate struct to use interface to handle any type */
func (i *InputJSON) UnmarshalJSON(input []byte) error {
    // intermediate step
    middle := struct {
        Alias 
        Age interface{} `json:"age,omitempty"`
    }{}

    if err := json.Unmarshal(input, &middle); err != nil {
        return err
    }

    *i = InputJSON(middle.Alias)

    if middle.Age != nil {
        switch realValue := (middle.Age).(type) {
        case string:
            intValue, err := strconv.Atoi(realValue)
            if err != nil {
                return err
            }
            i.Age = intValue
        case float64:
            // by default uses a float64 for number representation
            i.Age = int(realValue)
        }
    }
    return nil
}

func main() {
    // use cases 
    expectedCase := []byte(`
        {
            "name": "Sam smith",
            "age" : 34
        }
    `)

    awfulCase := []byte(`
        {
            "name": "Sam smith stringer",
            "age" : "41"
        }
    `)

    goodInput := InputJSON{}

    if err := json.Unmarshal(expectedCase, &goodInput); err != nil {
        fmt.Println(err)
    }
    fmt.Printf("%+v\n", goodInput)

    badInput := InputJSON{}

    if err := json.Unmarshal(awfulCase, &badInput); err != nil {
        fmt.Println(err)
    }
    fmt.Printf("%+v\n", badInput)

} 
```

这不是最优雅的方式，但确实有效:)

[https://play.golang.org/p/dwAPmGtHzZi](https://play.golang.org/p/dwAPmGtHzZi)