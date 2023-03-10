# 在 JavaScript 中析构对象

> 原文：<https://dev.to/marcoslooten/destructuring-objects-in-javascript-4lb6>

*本帖也发表在我的网站-[https://Marcos looten . com/blog/destructing-objects-in-JavaScript/](https://marcoslooten.com/blog/destructuring-objects-in-javascript/)*

就像数组析构一样，对象析构是一种更干净、更简洁的将值从对象赋给变量的方式。如果你还没有，我推荐你看看我之前关于[数组析构](https://dev.to/marcoslooten/destructuring-arrays-in-javascript-2dih)的文章(但没必要跟着看)。让我们探索对象析构。

## 给变量赋值

```
const lunch = {
  starter: 'Soup',
  main: 'Avocado toast', // I'm a millenial so I kinda have to =)
  drink: 'Beer'
} 
```

Enter fullscreen mode Exit fullscreen mode

我们从一家餐馆订了午餐。这些项目需要保存在它们自己的变量中，以便以后使用。您可以使用点或括号语法:

```
const starter = lunch.starter;
const main = lunch['main']; 
```

Enter fullscreen mode Exit fullscreen mode

为此，析构是一种更简洁的语法。在下一个例子中，我将整个对象分解成单独的变量。因为它是一个对象，所以声明的左边也需要类似于一个对象，就像这样:

```
const { starter, main, drink } = lunch;
// starter => 'Soup'
// main => 'Avocado toast'
// drink => 'Beer' 
```

Enter fullscreen mode Exit fullscreen mode

你实际上并没有声明一个对象，这只是一个析构语法。上面的例子是对象析构的典型用法，但也有点令人困惑。那是因为它使用了*对象简写*符号，这意味着你正在使用的对象的**键**也将是变量的**名**。示例中的情况是，我们从键“starter”中获取值(即`order.starter`或`order['starter']`)，并将该值赋给一个也称为“starter”的变量。这有助于避免重名(`const { starter: starter } = order;`)，这很方便，但并不总是如此。

## 给不同的命名变量赋值

现在这可能有点混乱，因为语法如下:

```
const { keyFromObject: newVariableName } = object; 
```

Enter fullscreen mode Exit fullscreen mode

我想这可能需要一段时间才能明白，至少对我来说是这样的。您实际上需要将对象的键作为赋值的键，并将新名称作为值。我们大多数人习惯于事物的左边是一个变量名(想想声明新的变量，你会有`const name = 'Marco'`)。但是在析构一个对象时，你首先需要目标对象的键(在冒号之前)来获得你想要的值。然后将变量名指定为该键的值(在冒号后面)。

```
const { main: mainMeal, drink: beverage, starter: starterMeal } = lunch;
// starterMeal => 'Soup'
// mainMeal => 'Avocado toast'
// beverage => 'Beer' 
```

Enter fullscreen mode Exit fullscreen mode

对象优于数组的一个优点是顺序无关紧要。这同样适用于析构，只要键与对象中的键匹配。在上面的例子中，我改变了顺序，它工作得很好。跳过项目很简单，省略就好！

## 只分配一些值，保留其余的

像数组析构一样，对象析构支持 rest 操作符(...)使您能够存储不想一次析构的所有内容。

```
const { starter: starterMeal, ...restOfMeal } = lunch;
// starterMeal => 'Soup'
// restOfMeal => { main: 'Avocado Toast', drink: 'Beer'} 
```

Enter fullscreen mode Exit fullscreen mode

然后，rest 变量将包含一个具有所有剩余键值对的对象。如果您需要来自对象的一些值，但希望保留所有未分配的值以备后用，这将非常有用。如果你必须使用原来的对象，你仍然会有你已经在其中析构的旧值。这让你很难跟踪对你重要的价值观。使用 rest 操作符可以解决这个问题，返回一个只包含未被析构的值的对象。

## 嵌套对象

有了对象析构，就有了更好的语法来从对象中获取特定的值。那么如何从更复杂的嵌套对象中析构呢？看看下面的午餐顺序。它是一个包含嵌套对象(“食物”)和数组(“饮料”)的对象。

```
const lunch = { 
  food: {
    starter: 'Soup',
    main: 'Avocado toast'
  },
  drinks: [
    'Beer',
    'Water'
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

记住对象析构语法需要遵循对象的结构，让我们试着在左边创建:

```
const { food: { starter, main }, drinks } = lunch;
// starter => 'Soup'
// main => 'Avocado toast'
// drinks => ['Beer', 'Water'] 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的是`food:`在对象中找到关键的“食物”。现在，我们可以访问我们的子项“starter”和“main”。然后你可以像访问一个简单的一维对象一样访问它们。不要忘记结束花括号！不过，这种语法可能很快就会变得有点混乱。总有点语法(或括号语法)作为备份:

```
const { starter, main } = lunch.food;
const { drinks } = lunch;
// starter => 'Soup'
// main => 'Avocado toast'
// drinks => ['Beer', 'Water'] 
```

Enter fullscreen mode Exit fullscreen mode

假设我们只想从一个特定的子对象中析构，我们可以像上面的例子那样做。虽然它结合了析构和旧的点语法，但对于复杂的对象，我更喜欢它，而不是 100%的析构。我个人觉得可读性更强一点。但是两者都没有错，所以请随意选择您最熟悉的语法(或者您的团队规定的语法)。如果你发现你进入了多个层次，这可能是你试图一次析构太多东西的信号。

## 备忘单

对象析构很酷，我认为它可能比数组析构简单一点。尽管如此，我还是想简单回顾一下各种可能性:

```
// Using shorthand notation
const {a, b, c} = {a: 1, b: 2, c: 3};
// a => 1, b => 2, c => 3

// Using named variables
const {a: first, b: second, c: third} = {a: 1, b: 2, c: 3};
// first => 1, second => 2, third => 3

// Storing the rest
const {a, ...others} = {a: 1, b: 2, c: 3};
// a => 1, others => {b: 2, c: 3}

// Nested objects
const obj = { parent: { a: 1, b: 2, c: 3 }, anotherParent: { d: 4, e: 5 } };
const {parent: { a, b, c }, anotherParent: { d, e } } = obj;
// a => 1, b => 2, c => 3, d => 4, e => 5

// Combining shorthand, naming, rest and nesting:
const obj = { parent: { a: 1, b: 2, c: 3 }, anotherParent: { d: 4, e: 5 } };
const { parent: { a: newName, b}, anotherParent: { ...anotherParentValues} } = obj;
// newName => 1
// b => 2
// anotherParentValues => { d: 4, e: 5} 
```

Enter fullscreen mode Exit fullscreen mode

PS。如果你想及时了解我关于前端(HTML，CSS，JS)和职业建议的帖子，你可以订阅我的简讯:[在这里订阅我的简讯](https://marcoslooten.com/)