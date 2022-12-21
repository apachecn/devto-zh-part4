# 在 JavaScript 中将函数作为参数传递

> 原文：<https://dev.to/erikwhiting88/passing-functions-as-parameters-in-javascript-339a>

# 第一类函数

在 JavaScript 中，函数被认为是“一级”或“高阶”函数。这基本上只是一种奇特的说法，我们可以将函数作为参数传递给其他函数，甚至从其他函数返回函数。

[![First class](img/0769edf4126a7236d823f4c56f8cfbb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0kGBdAnZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.picslyrics.nimg/458/224861/thumb.jpg%3F1463657347)

这实际上是函数式编程的核心概念之一(如果时间紧迫，也可以称为`fp`),可以带来一些非常强大的特性。不过，我们将通过一个非常简单的例子，让您能够更好地理解这个非常棒的特性。

# 我们的例子

在这个例子中，我们将建立一个联系人列表。我们将做一个`Person`班。每个人都有姓名、联系信息和首选的联系方式。换句话说，有些人希望收到电子邮件，有些人希望接到电话。

为了便于创建这些对象，我们将使用函数作为参数传递的功能。首先，让我们实际编写几个函数`callPerson`和`emailPerson` :

```
let callPerson = function(phoneNumber) {
  console.log("Dialing " + phoneNumber);
}

let emailPerson = function(emailAddress) {
  console.log("Emailing " + emailAddress);
} 
```

Enter fullscreen mode Exit fullscreen mode

这些函数将向控制台写入它们正在呼叫一个电话号码或发送一封电子邮件到一个特定的地址。我们将使用这些函数来构建我们的联系人列表。我们来写 Person 类:

```
class Person {
  constructor(name, contactInfo, preferredContact) {
    this.name = name;
    this.contactInfo = contactInfo;
    this.preferredContact = preferredContact;
  }

  makeContact() {
    this.preferredContact(this.contactInfo);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 person 类是通过传递人名、联系信息和首选联系方式来构造的。首选的联系方法实际上将是一个函数，您可以在 person 类中看到,`makeContact`函数使用传递给构造函数的首选方法进行联系。让我们创建一个人，看看会发生什么:

```
let erik = new Person("Erik", "555-444-3030", callPerson); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我正在向构造函数传递这个人的姓名、电话号码和电话呼叫函数的*名*(即末尾没有`()`)。现在，如果我试着联系这个人会怎么样？看一看:

```
> erik.makeContact();
Dialing 555-444-3030 
```

Enter fullscreen mode Exit fullscreen mode

注意 person 类的`makeContact`函数如何使用传递给它的构造函数的任何函数，将名称作为参数传入。让我们看看如果使用 emailing 函数会发生什么:

```
let lina = new Person("Lina", "smoochiebear@sweetheart.com", emailPerson); 
```

Enter fullscreen mode Exit fullscreen mode

我们对名为 lina:
的对象运行 make contact 方法

```
> lina.makeContact()
Emailng smoochiebear@sweetheart.com 
```

Enter fullscreen mode Exit fullscreen mode

同样，你看到我们传递给`Person`类的构造函数的不同函数是如何定义`makeContact`方法是如何实现的了吗？哦对了，你还可以通过匿名函数:

```
> let sonya = new Person("Sonya", "Mom", ((x) => console.log("Hi " + x)))
> sonya.makeContact()
Hi Mom 
```

Enter fullscreen mode Exit fullscreen mode

太疯狂了吧。

# 有什么用？

这不仅仅是客厅里的把戏，这个功能有一些真正的用途。假设我有一个所有联系人的数组:

```
let people = [erik, lina, sonya] 
```

Enter fullscreen mode Exit fullscreen mode

我们需要同时联系他们。我们没有时间去弄清楚他们首选的联系方式是什么，我们只需要告诉我们的程序现在就联系他们。因为我们创建了我们的类来接受一个函数作为首选的联系方法，所以我们可以很简单地做到这一点:

```
> people.forEach(person => person.makeContact())
Dialing 555-444-3030
Emailng smoochiebear@sweetheart.com
Hi Mom 
```

Enter fullscreen mode Exit fullscreen mode

由于我们将 person 类的`makeContact`定义为提供给构造函数的方法，我们可以简单地遍历这个`Person`对象数组，并告诉循环运行`makeContact`方法。我们已经提供了首选的联系方式，所以我们不需要为类似于
这样的事情而烦恼

```
// This is an example of what we're trying to avoid

if (Person.preferred contact === "email") {
  console.log("Emailing " person.emailAddress);
else if (Person.preferred contact === "phone call" {
// and so on and so on 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

对于这样一个超级棒的概念来说，这是一篇相当快的文章。我强烈建议您尝试一下 JavaScript 的这个特性，看看它能为您做些什么。