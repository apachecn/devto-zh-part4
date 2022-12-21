# Ruby 中 Yield 的介绍，以及过滤类实例

> 原文：<https://dev.to/nwdunlap17/an-introduction-to-yield-in-ruby-and-filtering-class-instances-549j>

## 避免潮湿，让你的代码保持干燥

每个编程过一段时间的人都知道保持代码干燥的价值(不要重复)。你最不想做的事情就是编写代码。没错，这是一个如此重要的概念，以至于它有两个缩写*！*

尽可能保持代码干燥有很多好处。

*   将一大块重复的代码转换成一个方法通常会更快，这样下次需要调用它时，可以在一行中完成。

*   如果你需要改变你的代码，你只需要在一个地方这样做。

*   如果每个部分都是独一无二的，那么你的代码就更容易理解。否则，你可能会冒这样的风险:评论者看着一个部分，说‘我以前见过这个’，而没有注意到这次有一点点不同。

因此，将任何重复的代码放入一个可以重复调用的方法中总是一个好的做法。但是当你*想稍微改变你的代码块的时候怎么办呢？对于这些实例，您可以使用 *yield* 关键字。*

## Yield 如何工作

yield 关键字允许您在函数执行过程中临时传递函数的控制权。本质上，你可以构建一个函数，其中有一个 *yield* ，然后*你可以在每次调用函数*时用你选择的代码替换这个‘yield’。

当您使用 yield 调用一个函数时，您包括了一个用花括号括起来的代码块，它将替换 yield 关键字的任何实例。以下两个函数具有相同的输出。

```
 def hello()
        return “Hello World!”
    end
    hello() #=> “Hello World!”

    def hello2()
        return yield
    end
    hello2(){“Hello World!”} #=> “Hello World!" 
```

在同一个函数中可以使用多个 yieldss，每个 yield 都将被相同的代码块替换。

```
 def double()
        yield
        yield
    end
    double(){print “Hi!”} #=>”Hi!Hi!” 
```

当然，如果不能通过 yield 传递变量，代码块将毫无用处。在下面的代码片段中， *name* 变量被传递给 yield 块，在那里它被称为 *person* 。

```
 def greet(name)
        print “You see #{name}. ”
        puts yield(name)
    end
    greet(‘Dave’){|person| “Hi #{person}!”} #=> ”You see Dave. Hi Dave!”
    greet(‘Dave’){|person| “#{person} says hello!”} #=> ”You see Dave. 
Dave says hello!” 
```

你可能已经注意到，在最后一个例子中，屈服对我们来说更容易一些。注意，在上面的两个例子中，我们把名字放在第二个句子的开头或结尾。如果我们将句子的其余部分传递给一个函数，我们将需要一点额外的逻辑来确定名字应该放在哪里。但是由于 yield 让我们可以直接访问函数内部的变量，我们可以很容易地把它放在我们想要的地方。

## 函数 vs 产量块

*   可以多次调用相同的函数，但是每次函数必须让步时，必须完整地写入让步块。因此，最佳实践是尽可能多地使用函数，并且只对代码的可变部分使用 yield。

*   对于传递的变量，产量和函数的作用是相同的。Yield 函数**只有在被调用**时才能访问传递给它的变量，就像函数一样。变量将以默认方式传递给 yield 这意味着更改 yield 中的整数、字符串或浮点数不会影响 yield 之外的值；但是改变一个数组或者散列会。

*   **return** 关键字结束 yield 所在的函数。这意味着 yield 之后出现的任何内容都不会执行。在大多数情况下，这可能是您想要避免的，所以只需使用隐式返回。

*   在包含 yield 的函数中，您可以将 yield 视为一个函数，用于传递值和从中检索值的目的和语法。例如，如果 yield 块接收一个字符串并返回一个数组，您可以使用 *yield(string)[3]* 传入该字符串并获得该数组的第 4 个元素。也可以设置变量等于 yield 的输出: *foo = yield* 。

## 通过类迭代器使用 Yield

yields 最常见的用途之一是修改类以用于迭代器。假设我们有一个 People 类，这个类的每个实例都有不同的属性:姓名、年龄、职业等..如果我们想创建一些函数，可以很容易地过滤掉特定的人，我们可能会有一些类似这样的函数。

```
 def filterPeopleByAge(minAge,maxAge)
        people = Person.all.filter do |person|
            person.age >= minAge && person.age <= maxAge
        end
    return people
    end
    filterPeopleByAge(18,26) 
    #Returns everyone between 18 and 26

    def filterPeopleByName(name)
        people = Person.all.filter do |person|
            person.first_name == name
        end
        return people
    end
    filterPeopleByName (‘Dave’) 
    #Returns everyone named Dave

    def filterPeopleByOccupation(occupation_array)
        people = Person.all.filter do |person|
            occupation_array.include?(person.occupation)
        end
        return people
    end
    filterPeopleByOccupation ([‘Doctor’,’Lawyer’,’Professor’]) 
    #Returns all doctors, lawyers, and professors 
```

但是这看起来确实有很多重复的代码。我们只改变了这些函数中的一个东西:过滤条件。我们不能简单地编写一个函数来处理对每一个可能属性的搜索(至少，不能是一个整洁或易读的函数)。但是通过使用 yield，我们可以直接控制条件语句。下面的函数和 yield 可以以更短、更清晰的方式执行上述函数的角色。

```
 def filterPeople()
        people = Person.all.filter do |person|
            yield(person)
        end
        return people
    end
    filterPeople(){|person|person.age > 18 && person.age < 26} 
    # Returns all people between 18 and 26
    filterPeople(){|person|person.first_name = ‘Dave’} 
    #Returns all people whose first name is Dave
    filterPeople(){|person|         
    [‘Doctor’,’Lawyer’,’Professor’].include(person.occupation) } 
    #Returns all doctors, lawyers, and professors 
```

这个通用函数比前一组函数更加强大，原因有三。首先，即使我们向 person 类添加了额外的属性，它仍然是有效的。第二，只需要一行代码就可以调用并创建新的搜索条件。最后，我们可以轻松地使用它来组合搜索标准。

```
 filterPeople() {|person| person.age < 40 && person.occupation == 'Lawyer'}
    #Returns all lawyers under 40 
```

## 一个更加通用的解决方案

以前的搜索功能在理论上看起来不错，但实际上它有一个明显的缺陷。Yield 只能访问 yielding 函数传递给它的值。

```
 def filterPeople(args)
        people = Person.all.filter do |person|
            yield(person,args)
        end
        return people
    end
    minAge = 18
    maxAge = 40
    filterPeople(){|person|person.age > minAge && person.age < maxAge}
    #THIS DOES NOT WORK. The yield cannot access the Age values because they were not passed in during the filterPeople function. 
```

如果我们希望能够有一个更智能的搜索功能，我们需要允许它接受一些参数。我们将创建一个通用数组 *args* 来保存我们的标准。

```
 def filterPeople(args=nil)
        people = Person.all.filter do |person|
            yield(person,args)
        end
        return people
    end 
```

现在，我们可以自由地通过函数向 yield 传递参数。因为函数和 yield 块都是一起调用的，所以 args[foo]指的是什么应该相当清楚。

```
 minAge = 14
    maxAge = 18
    filterPeople([minAge,maxAge]){|person,args|person.age > args[0] && person.age < args[1]}

    minAge = 30
    occupations = ['Artist','Race Car Driver','Athlete']
    filterPeople([minAge,occupations]){|person,args|person.age > args[0] 
    && args[1].include?(person.occupation)} 
```

现在我们有了一个函数，它允许我们为 Person 类创建任意的搜索标准！我们可以在一行中调用它。