# Go 中的模板文件处理

> 原文：<https://dev.to/kirklewis/template-file-processing-in-go-1ida>

在[之前的一篇文章](https://dev.to/kirklewis/go-text-template-processing-181d)中，我演示了如何获得 Go 模板的插值字符串结果。然而，如果你只是想插值，然后将字符串结果直接写入文件，这篇文章将演示如何做。

## 模板文件

以下文本可以保存到文件`templates/greeting.tmpl`中。

```
{{.Greeting}} {{.Name}}! 
```

Enter fullscreen mode Exit fullscreen mode

上面的模板字符串包含两个注释`.Greeting`和`.Name`，这对于本演示来说已经足够了。

## 执行并写入

`text/template`包的`Execute`函数的第一个参数使用了一个 [io。作家](https://golang.org/pkg/io/#Writer)接口道。因此，如果我们将这个参数传递给一个`os.File`，它的 [write](https://github.com/golang/go/blob/fa02af4238a3d41e23bc945e7d48e50fe4429405/src/os/file.go#L145) 函数将被用来将处理后的模板字节直接写入文件。

将以下代码添加到名为`process-file.go`的文件中。

```
package main

import (
    "os"
    "text/template"
)

func main() {
    // variables
    vars := make(map[string]interface{})
    vars["Greeting"] = "Hello"
    vars["Name"] = "Dev"

    // parse the template
    tmpl, _ := template.ParseFiles("templates/greeting.tmpl")

    // create a new file
    file, _ := os.Create("greeting.txt")
    defer file.Close()

    // apply the template to the vars map and write the result to file.
    tmpl.Execute(file, vars)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了简洁，我用下划线省略了上面代码中的错误检查

**运行文件**

```
go run process-file.go 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的代码应该会创建一个名为`greeting.txt`的文件。检查该文件的内容。

```
cat greeting.txt

Hello Dev! 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读！这篇文章中使用的代码可以在 Github gist [这里](https://gist.github.com/kirklewis/e502c234fe6e88137544762dc419c644)找到。