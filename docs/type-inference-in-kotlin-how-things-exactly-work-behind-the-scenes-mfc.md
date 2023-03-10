# 科特林的类型推理。事情在幕后是如何运作的。

> 原文：<https://dev.to/jay_tillu/type-inference-in-kotlin-how-things-exactly-work-behind-the-scenes-mfc>

> ***类型—*** 数据类型
> ***推理—*** 自动识别某物。

*   kotlin 是一种强类型语言，支持*类型推理*。

*   Kotlin 编译器可以自动识别变量的数据类型。编译器通过初始化器知道这一点。

*   因此，如果在声明中初始化值，就不需要显式定义数据类型。

*   因为每个基本类型都有一个默认值。编译器将直接假定变量的大小和类型。

*   例如，如果你给一个变量赋值一个整数，编译器会直接假设这个变量的类型是 *Int* 。

*   但是请记住，如果您希望以后初始化变量的值，那么在这种情况下，您必须显式定义数据类型。在那种情况下，类型推断的概念就不起作用了。

所以，伙计们，这就是类型推断。所有的魔术都是这样在幕后进行的。随意问任何问题。

在那之前，继续编码，继续爱。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)