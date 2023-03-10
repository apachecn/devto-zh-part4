# 作为 Rubyist 学习 Javascript

> 原文：<https://dev.to/mindyzwan/learning-javascript-as-a-rubyist-4cbn>

一些简化的比较使进入 Javascript 的最初几步变得更加容易。

<figure>[![](img/48bf0557011382a202d9e33963d5b413.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--whYPn5H_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFyWmdsQEnvWsCh_fEvZIFA.jpeg) 

<figcaption>照片由[经纬线](https://www.pexels.com/@gratisography?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[像素](https://www.pexels.com/photo/man-person-legs-grass-539/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)</figcaption>

</figure>

我爱露比。

我最近还发现，有些人非常不喜欢它。

所以我在这里，学习 Javascript。

我交谈过的人可能会对他们的评论对我产生的影响感到震惊。这里有一个秘密——我仍然喜欢 Ruby，Javascript 是我正在参加的在线学校的下一部分课程( [*【启动学校】*](https://launchschool.com/) *)，所以无论如何我都会学习它，但这让这个故事变得不那么有趣了。*

所以我在这里，学习 Javascript。

开始学习一门新语言的头几天，感觉有点像一个信息消防站对准了你的脸。作为一名前高中教师，我在这里告诉你如何处理，给你一个建议——我们通过将新信息与旧信息联系起来学习得最好。

所以让我们建立一些联系吧！

### 书写语法

#### 正在评论

在 Ruby 中，我们使用#进行单行注释，而=begin with =end 进行多行注释。

一个 Ruby 示例:

`# Comments are great!`

```
=begin
Honestly, I find this a weird
syntax for comments. 
=end 
```

要用 Javascript 编写注释，可以使用//作为单行注释，使用/* with */作为多行注释。

一个 Javascript 示例:

`// I'm a wonderfully useful comment!`

```
/* 
Me
Too! 
*/ 
```

#### 案例

在 Ruby 中，我们用 snake_case 来表示变量、文件、目录等，用 CamelCase 来表示类名，用 squing _ SNAKE 来表示常量。

Javascript 大多使用 camelCase。您将看到带有构造函数的 PascalCase(很快会有更多关于它们的内容)，以及为常量尖叫的 _SNAKE，就像在 Ruby 中一样。下划线(_)仅用于常量。

#### 方法与函数

在 Ruby 中，我们使用术语“方法”来表示函数。在 Javascript 中，我们更经常看到术语“函数”，因为它是方法/函数定义的一部分。

一个 Ruby 的例子:

```
def method_name(parameters)
 # some code
end 
```

一个 Javascript 例子:

```
function methodName(parameters) {
 // some code
} 
```

我发现这句话有助于进一步澄清今年早些时候从 [dev 到文章](https://dev.to/tiffany/what-is-the-difference-between-a-function-and-a-method-in-javascript-3mkj)的 [Tiffany White](https://dev.to/tiffany) 的描述:

> 简而言之:一个*方法*就是一个属于一个类的**函数**。然而，在 JavaScript 中，*方法*是属于 ***对象*** 的**函数**。

#### 字符串插值

两种语言都提供了将变量值插入字符串的语法。

一个 Ruby 的例子:

```
site_name = "Medium"

"Welcome to #{site_name}" 
```

Javascript 使用反斜杠(`` ` ``)和美元符号(`$`)来创建所谓的“模板字符串文字”，它允许字符串插值。

一个 Javascript 例子:

```
var siteName = 'Medium';

`Welcome to ${siteName}!` 
```

### 方法等…

Mozilla 开发者网络(MDN)是一个很好的参考点。下面是 [Javascript 参考链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)。我建议从看一些熟悉的东西开始，比如[数组文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)。

让我们从熟悉的东西开始——实例方法！

#### 实例方法

幸运的是，Ruby 和 Javascript 都使用实例方法。

一个 Ruby 例子:`[4, 3, 2].sort`
T3 一个 Javascript 例子:`[4, 3, 2].sort // Oh hey, they're the same — NICE.`

在我们方便的 MDN 参考列表中，如果一个方法包含单词 prototype，您就可以判断它是否是一个实例方法。

#### 静态法

这些类似于 Ruby 的类方法。

一个 Ruby 例子:`Hash.new`
一个 Javascript 例子:`String.fromCharCode(65, 66 ,67) // Returns 'ABC'`

#### 构造函数

作为一个介绍性的参考点，构造函数可以与 Ruby 的类相比较。虽然 Javascript 构造函数和 Ruby 类在本质上非常不同，但它们都是创建新对象的工具，在这点上，它们是相似的。

一个 Ruby 的例子:

```
class Dog
 def initialize(name, age, breed)
 @name = name
 @age = age
 @breed = breed
 end
end

new_dog_object = Dog.new('Coco', 8, 'Mixed') 
```

一个 Javascript 例子:

```
function Dog(name, age, breed) {
 this.name = name;
 this.age = age;
 this.breed = breed;
}

newDogObject = new Dog('Coco', 8, 'Mixed'); 
```

当然，也可以进行其他比较，但这将为您提供一个很好的起点！好好编码，我的朋友们！

`article.stop();`