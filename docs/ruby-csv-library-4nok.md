# Ruby CSV 库

> 原文：<https://dev.to/andershornor/ruby-csv-library-4nok>

我最近完成了一项带回家的代码挑战，并且在将 CSV 文件转换成 JSON 时获得了一些乐趣，但是在面试官审查了我的代码之后，我意识到我让我的生活变得比需要的更艰难。我用 Ruby 做了这个挑战，因为我对它最熟悉，但在开始之前没有做足够的功课。也许是我的紧张，也许是我从头开始做一切的动力，但是我没有研究任何可能对我的任务有帮助的 ruby 库。我想我应该在这里说，在我的编程之旅中，我认为我应该自己做所有的事情。我认为我应该了解一门语言的每一个细节，用它最原始的形式去做我需要做的一切。尽管了解您使用的任何东西在“幕后”是如何工作的很好，并且完成一门语言的开发有助于学习这门语言，但是没有人期望您重新发明轮子(除非另有说明)，尤其是在面临编码挑战时。

> 后退一步，想想今天某人在任何领域或科学上所做的任何事情(实际上是任何事情)，都是建立在其他人在该领域或围绕该领域所做的所有工作之上的。我的意思是这些高级语言不就是这样吗？数百万(数十亿)的顶点？)的工作时间，把一些原始的东西提炼为一个强大的工具，让工作变得更容易一点，让生活变得更艰难一点，把界限推得更远一点？T3】

于是在那次 ***的切线人生课之后*** 又回到了手头的事情上。在完成了这个代码挑战之后，我意识到这个名为 ruby 的轮子车提供了许多库。我开始意识到内置于语言中的一个工具，我自信我知道并且可能应该假设由于语言的本质而存在；我开始意识到 Ruby 内置的 CSV 库和类。因此，稍后查看[文档](https://ruby-doc.org/stdlib-2.6.1/libdoc/csv/rdoc/CSV.html)以获得关于这个主题的更多信息，但是请阅读这里来体验这个神奇的工具。

### 基本访问方法

像所有内置在 Ruby 中的库一样，要访问提供的类方法，你需要有一个当代版本的 Ruby，看起来像 2.6.1 或更新的版本，并且在你的文件顶部有 require library 语句`require 'csv'`。

通过访问 CSV 类方法，现在您可以使用两种强大的方法来破解您想要访问的文件或值，

```
CSV.read("input_filename_or_path_here.csv")
CSV.parse("1,Comma,Separated\n2,Values,Are\n3,The,Best") 
```

举例来说，假设 input _ filename _ or _ path _ here . CSV 文件中有三行类似于

```
id, first_word, second_word
1,Comma,Separated
2,Values,Are
3,The,Best 
```

上面两种方法的输出是一个 CSV::table(基本上是一个或多个数组)，如下所示:

```
[
[id, first_word, second_word],
[1,Comma,Separated],
[2,Values,Are],
[3,The,Best]
] 
```

如果您只需要一个或两个值，或者利用类方法中内置的选项，您可以像处理任何其他数组一样处理这个 CSV::table 对象，这样就可以在创建输出表时对其进行整形。

下面的选项显示了在没有任何给定选项的情况下使用 CSV 方法时假定的默认值。

*   `headers: false`
*   `col_sep: ","`
*   `quote_char: '"'`
*   `field_size_limit: nil`
*   `unconverted_fields: nil`
*   `return_headers: false`
*   `row_sep: auto`
*   `skip_blanks: false`
*   `force_quotes: false`
*   `skip_lines: nil`
*   `liberal_parsing: false`
*   `quote_empty: true`
*   `header_converters: nil`
*   `converters: nil`

值得注意的是`headers`和`converters`选项，我将在这里深入探讨一下。

#### 标题

headers 选项`CSV.read("filepath.csv", headers: true)`通知 read 方法头的存在，将它们添加到该方法输出的 CSV::表的顶部。在上面的测试用例中，将 headers 设置为 true 将组织表，以便共享相同嵌套索引的标题可以访问每一列。例如，

```
table = CSV.read("input_filename_or_path_here.csv", headers: true)
table["id"] #=> ["1", "2", "3"]
table.first #=> CSV::Row "id":"1", "first_word":"Comma", "second_word":"Separated" 
```

#### 转换器

转换器对于控制要添加到表中的值的质量非常有用。我的意思是，读取方法 sans 转换器会将每个值作为字符串添加到输出表或新的 CSV 文件中。所以上面的 id 都以字符串的形式保存在表中。为了使我们的表格更具代表性，在我们第一次读取文件时使用转换器会给数据增加味道、纹理、重量、感觉、细微差别、亮度、质量、独创性、形状、容量和不可避免的力量。
“内置”转换器有`integer`、`float`、`numeric`、`date`、`date_time`等。`numeric`是`integer`和`float`的组合(不太确定...)和`all`是日期时间和数字的组合。
转换器是另一种`option`并如此使用，例如

```
table = CSV.read("filepath.csv", headers: true, converters: numeric)
table.first[0].class #=> Integer 
```

还有一个 converter creator，它为 CSV 方法增加了更多功能。它允许你像这样创建一个新的转换器:

```
CSV::Converters[:boolean] = ->(value) { value.downcase.to_s == "true" rescue value }
table = CSV.parse("1,true\n2,false\n3,false", converters: :boolean)
table[0][1] #=> TrueClass 
```

### 奖励:经典的写文件

所以现在你可以读一些文件，但是写它们呢？有一个 libD.
就像读取一个 CSV 文件来创建一个文件一样，你显然使用了内置方法`open`...一个奇怪的 ruby 东西(或者可能是一般的编程语言，我对这个没什么经验)是内置的类方法，它们有一百万种用途和晦涩的通用名称。像`CSV.open`一样，有`CSV.generate`和`CSV.new`方法，但是当`CSV.open`为您提供所有 CSV 需求的一站式商店时，为什么还要使用这些方法。等等，那不是 CVS 还是它！！！！？！是的。
快速高效地运行一个新的 CSV 文件，比如:

```
CSV.open('filename.csv', "wb") { |csv| csv << [1,"yes","yes"]; csv << [2,"no","no"] } 
```

`CSV.open`方法接受几个参数，即文件名，然后是一个写文件而不是读文件的选项。它还可以访问上面提到的选项，这些选项可以提高您正在编译的值的质量。

### 结论

一些想法坐在一起。

*不要重新发明轮子...这通常是不必要的、费时的，并且由于前两个原因而被低估了(有时这是一个很好的学习练习，但是也要表扬那些不知道是否可能做到的人)。Ruby 有大量的内置类方法，比如 CSV 库！生活如此艰难。