# 以 FP 方式在 Scala 中编辑 XML

> 原文：<https://dev.to/geirolz/edit-xml-in-scala-21mg>

嗨，伙计们，我是新来的，这是我的第一个帖子！

我来这里是因为我想和你分享一个我为我的企业实现的小项目，这是由于难懂的样板 Scala XML 库的 API。

这个项目非常年轻和简单。它基于 Cats-core，使用 MonadError 来处理错误。

希望有人能有所贡献。

github:[https://github.com/geirolz/advxml](https://github.com/geirolz/advxml)

特点:

*   追加、删除、替换 XML 节点
*   编辑节点的属性
*   读取 XML 处理可选性和强制性节点和属性
*   将 XML 转换成对象，反之亦然
*   规范化 XML
*   将 Java 文档转换为 Scala 节点序列

示例:

```
import com.github.geirolz.advxml.all._
import scala.xml._
import scala.util._

//import MonadError instance for Try
import cats.instances.try_._

val doc: Elem =
<Persons>
 <Person Name="Mimmo">
  <Cars>
   <Car Brand="Fiat"/>
  </Cars>
 </Person>
</Persons>

val rule = $(_ \ "Person" \ "Cars")
 ==> Append(<Car Brand="Lamborghini"/>)
 ==> Append(<Car Brand="Ferrari"/>)
 ==> Append(<Car Brand="Bmw"/>)

val result: Try[NodeSeq] = doc.transform[Try](rule) 
```

Enter fullscreen mode Exit fullscreen mode

我希望这个小项目可以帮助绝望中的人使用 Scala XML 库

让我知道你对它的看法:)