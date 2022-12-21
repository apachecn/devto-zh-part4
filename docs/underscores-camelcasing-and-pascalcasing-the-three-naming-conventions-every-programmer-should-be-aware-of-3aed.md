# Pascal Case under_scores、camelCase 和 Pascal Case 每个程序员都应该知道的三个命名约定

> 原文：<https://dev.to/prahladyeri/underscores-camelcasing-and-pascalcasing-the-three-naming-conventions-every-programmer-should-be-aware-of-3aed>

代码中的各种标记(变量、类、函数、名称空间等。)可以使用以下三种风格中的一种来命名，概括地说:

1.  骆驼案(ex: *someVar* 、 *someClass* 、 *somePackage.xyz* )。
2.  Pascal Case(例如: *SomeVar* 、 *SomeClass* 、 *SomePackage.xyz* )。
3.  下划线(例如: *some_var* 、 *some_class* 、 *some_package.xyz* )。

在 camel case 中，名称以小写开始，但是名称中的每个适当的单词都是大写的，缩写也是如此。比如很多语言中常用的 toString、checkValidity、lineHeight、timestampToLocalDateTime 等。都是骆驼肠衣的例子。

Pascal 大小写类似于 camel 大小写，只是第一个字母也以大写字母开始(SomeClass 而不是 someClass)。

在下划线大小写中，所有内容都是小写(甚至是首字母缩写)，单词由下划线分隔(some_class、some_func、some_var 等)。这个惯例也就是俗称的*蛇案*。

一般的想法是，只要你在任何地方都坚持使用它，你就可以在你的项目中使用任何约定。但是当你为一个大型项目或团队编码时，你应该遵守那里使用的规范。因此，了解各种编程语言中的典型约定是很有帮助的。

对于像 Java 或 JS 这样的 C 风格语言，通常的做法是对所有变量和对象成员(属性和方法)使用 camelCase，对类名和构造函数使用 PascalCase。名称空间(或 Java 世界中的包)通常在 camelCase 中。

但是有些语言例外。例如，C#对名称空间甚至公共方法使用 PascalCase。因此，主函数(或入口点)在 java 中始终是`static void main()`，但在 C#中是`static void Main()`(注意单词“main”的大写)。

一些不是从 C 语言派生语法的语言(比如 Python 和 Ruby)除了类名之外，几乎所有东西都使用下划线。因此，在 python 中总是用`sys.base_prefix`代替`sys.basePrefix`，`datetime`代替`DateTime`，`str.splitlines()`代替`str.splitLines()`。

在 python 的标准库的情况下，我注意到甚至类有时也使用下划线，这是不一致的。例如，`datetime.datetime`是一个类，`csv.excel_tab`也是。但是流行的框架和库(比如 django 和 flask)使用 camel case 作为类。

类似的不一致在 PHP 中。近年来，这种语言从下划线演变成了骆驼大小写，但一些古老的符号仍然困扰着这种语言。对于 ex，`mysqli::set_local_infile_default` vs `PDOStatement::debugDumpParams`。

所以，归根结底，当你开始一个项目时，这取决于你自己的偏好。但是，了解在流行的开源项目中使用您喜欢的语言通常遵循的惯例是有帮助的。

**更新**

还有 [@ovais](https://dev.to/ovais) 指出的第四种情况，即*肉串案*。除了下划线被连字符(破折号)取代之外，它与下划线大小写非常相似。因此，`some_func`变成了`some-func`,这显然是不允许的，因为 dash 不用于命名标记，因为它已经是大多数编程语言中减号运算符的内置部分。kebab case 最常用的地方是在 css 样式表中创建类！像`main-div`、`main-navbar`和`article-footer`这样的名字是 web 开发人员在编写 HTML/CSS 时经常使用的。这个大会基本上就是烤肉串的案子。

**更新**

正如 [@patrykrudnicki](https://dev.to/patrykrudnicki) 所说，常量的处理方式不同。根据我的经验，完整的下划线(`SOME_CONST`)是许多语言(包括 Java、PHP 和 Python)中常量的流行约定。

**更新**

总而言之，这是最常用的开源编程语言中典型的或普遍遵循的约定:

| 代币 | 大蟒 | Java/JS | 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） |
| --- | --- | --- | --- |
| 可变的 | 低于分数 | 茶包 | 混合(向驼箱移动) |
| 功能 | under_score() | 茶包() | mix(向 camelCase()移动) |
| 常数 | 低于分数 | 低于分数 | 低于分数 |
| 班级 | 帕斯卡尔·凯斯 | 帕斯卡尔·凯斯 | 混合(向 PascalCase 移动) |
| 命名空间 | 低于分数 | 茶包 | 混合(向 PascalCase 移动) |

一些有用的链接:

1.  [https://software engineering . stack exchange . com/questions/196416/whats-the-dominant-naming-conventi on-for-variables-in-PHP-camel case-or-undersc](https://softwareengineering.stackexchange.com/questions/196416/whats-the-dominant-naming-convention-for-variables-in-php-camelcase-or-undersc)
2.  [https://stack overflow . com/questions/149491/Pascal-casing-or-camel-casing-for-c-sharp-code](https://stackoverflow.com/questions/149491/pascal-casing-or-camel-casing-for-c-sharp-code)
3.  [https://www . c-sharp corner . com/forums/when-to-use-camel-case-and-Pascal-case-c-sharp](https://www.c-sharpcorner.com/forums/when-to-use-camel-case-and-pascal-case-c-sharp)
4.  [https://software engineering . stack exchange . com/questions/53498/cs-Pascal-casing-method-names 背后的哲学推理是什么](https://softwareengineering.stackexchange.com/questions/53498/what-is-the-philosophy-reasoning-behind-cs-pascal-casing-method-names)