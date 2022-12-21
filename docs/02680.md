# Javascript 中的函子和单子

> 原文：<https://dev.to/bonesmcginty/functors-and-monads-in-javascript-4j29>

# 函子和单子

这次演讲的目的是阐明一些我们随处可见的函数式编程术语，主要是`Functor`和`Monad`。

这些是什么鬼东西？先说我在网上看到的两个短语。

> “A `functor`是你可以`map`的东西。”
> 
> "一个`monad`是一个`functor`，你可以`flatMap`. "

让我们深入研究一下。

## 函子

在数学中，特别是在范畴理论中，函子是范畴之间的映射。

在数学中，范畴(有时称为抽象范畴，以区别于具体范畴)是由“箭头”联系起来的“对象”的集合。

迷茫？很好。

简单来说范畴就是一个`collection of objects`姑且称之为某物，函子就是`collections of objects`之间的映射。

这就引出了我们的第一句话:

> “A `functor`是你可以`map`的东西。”

我们来看一些代码:

```
const collection1 = [1, 2, 3] // [1,2,3]
const collection2 = collection.map(x => x + 1) // [2,3,4] 
```

这里我们有一个数组(整数的集合)。因为我们可以通过执行`x => x + 1`将集合 1 映射到集合 2，所以我们可以说 JS 中的数组是`Functors`。

假设我们想创建自己的函子。这个仿函数将代表一个人对象。

```
const p1 = {
  firstName: 'matthew',
  lastName: 'staniscia',
  hairColor: 'brown',
  age: 37,
}

const Person = value => ({
  value,
})

Person(p1)

/*
Result
{ 
  value:{ 
    firstName: 'matthew',
    lastName: 'staniscia',
    hairColor: 'brown',
    age: 37 
  }
}
*/ 
```

这还不是一个函子，因为我们还不能映射它。所以让我们给它添加一个映射函数。

```
const p1 = {
  firstName: 'matthew',
  lastName: 'staniscia',
  hairColor: 'brown',
  age: 37,
}

const Person = value => ({
  map: fn => Person(fn(value)),
  value,
})

Person(p1)

/*
Result
{ 
  map: [Function: map],
  value:{ 
    firstName: 'matthew',
    lastName: 'staniscia',
    hairColor: 'brown',
    age: 37 
  }
}
*/ 
```

我们现在可以将一些功能映射到它。

```
const objectMapper = fn => value =>
  Object.keys(value).reduce((acc, cur, idx, arr) => ({ ...acc, [cur]: fn(value[cur]) }), value)

const makeUpper = s => (typeof s === 'string' ? s.toUpperCase() : s)

Person(p1).map(x => objectMapper(y => makeUpper(y))(x))
Person(p1).map(x => objectMapper(makeUpper)(x))
Person(p1).map(objectMapper(makeUpper))

/*
Result for all 3 calls
{ 
  map: [Function: map],
  value:{ 
    firstName: 'MATTHEW',
    lastName: 'STANISCIA',
    hairColor: 'BROWN',
    age: 37 
  }
}
*/ 
```

让我们尝试将几个函数映射在一起。

```
const objectMapper = fn => value =>
  Object.keys(value).reduce((acc, cur, idx, arr) => ({ ...acc, [cur]: fn(value[cur]) }), value)

const makeUpper = s => (typeof s === 'string' ? s.toUpperCase() : s)

const checkAge = n => (typeof n === 'number' ? (n <= 35 ? [n, 'You is good.'] : [n, 'You is old.']) : n)

Person(p1)
  .map(objectMapper(makeUpper))
  .map(objectMapper(checkAge))

/*
Result
{ 
  map: [Function: map],
  value:{ 
    firstName: 'MATTHEW',
    lastName: 'STANISCIA',
    hairColor: 'BROWN',
    age: [ 37, 'You is old.' ] 
  }
}
*/ 
```

这个对象现在是一个函子，因为它是我们可以映射的东西。现在是时候把它变成单子了。

## 单子

让我们回到前面单子的定义。

> "一个`monad`是一个`functor`，你可以`flatMap`. "

### 什么是 flatMap？

简而言之，当你平面映射某个东西时，你将运行一个映射函数，然后将它展平。

在我们的 Person 对象的情况下，我们的输出看起来不像`Person({...stuff...})`而是`{...stuff...}`。

我们使用 flatMap 从上下文中提取地图的结果。平面图的其他名称是`chain`和`bind`。

回到代码。

```
const Person = value => ({
  map: fn => Person(fn(value)),
  chain: fn => fn(value),
  value,
}) 
```

这看起来很简单。因为我们正在映射并从上下文中取出值，所以我们只需要返回展开的值。让我们看看它的实际效果。

```
Person(p1).chain(objectMapper(makeUpper))

/*
Result
{ 
  firstName: 'MATTHEW',
  lastName: 'STANISCIA',
  hairColor: 'BROWN',
  age: 37 
}
*/

Person(p1)
  .chain(objectMapper(makeUpper))
  .chain(objectMapper(checkAge))

/* 
Result

TypeError: Person(...).chain(...).chain is not a function
*/ 
```

休斯顿，我们有麻烦了。这里发生了什么？为什么是错的？很简单。第一个链的返回不再是一个人单子，它只是一个 JSON 字符串，所以试图再次链接它是行不通的，如果我们想在一个链上链接我们需要维护上下文。

```
Person(p1)
  .chain(x => Person(objectMapper(makeUpper)(x)))
  .chain(objectMapper(checkAge))

/*
Result
{
  firstName: 'MATTHEW',
  lastName: 'STANISCIA',
  hairColor: 'BROWN',
  age: [ 37, 'You is old.' ]
}
*/ 
```

但那不是和这个一样吗？

```
Person(p1)
  .map(objectMapper(makeUpper))
  .chain(objectMapper(checkAge)) 
```

是的。因为映射保持上下文，所以我们可以映射或链接上下文。

我想我们以前见过类似的事情...

## 单子法则

一个物体成为单子必须满足三个单子定律。

*   左侧标识
*   正确的身份
*   结合性

```
// testing monad rules
const x = 'Matt'
const f = x => Person(x)
const g = x => Person(x + ' is kool')

const LI1 = Person(x).chain(f)
const LI2 = f(x)

const RI1 = Person(x).chain(Person)
const RI2 = Person(x)

const AC1 = Person(x)
  .chain(f)
  .chain(g)
const AC2 = Person(x).chain(x => f(x).chain(g))

// Left Identity
// Monad(x).chain(f) === f(x)
// f being a function returning a monad
Object.entries(LI1).toString() === Object.entries(LI2).toString()

// Right Identity
// Monad(x).chain(Monad) === Monad(x)
Object.entries(RI1).toString() === Object.entries(RI2).toString()

// Associativity
// Monad(x).chain(f).chain(g) == Monad(x).chain(x => f(x).chain(g));
// f and g being functions returning a monad
Object.entries(AC1).toString() === Object.entries(AC2).toString()

/*
Result
true
true
true
*/ 
```

在我们的人单子的情况下，它满足这些规则。

## 为什么要用单子？

你不需要用单子。如果你使用单子并以同样的方式编写所有单子，那么你将拥有一个可以随意链接和混合的结构。单子很大程度上是一种设计结构，可以用来帮助您跟踪上下文，以便您的代码清晰一致。

让我们看一个不同单子一起使用的基本例子。这些是非常基本的单子，但他们会明白这一点。

我们将再创建 3 个单子`Child`、`Teen`和`Adult`。如果你想知道它是`Child`、`Teen`还是`Adult`，你可以访问这些单子的一些属性。

```
const Person = value => ({
  map: fn => Person(fn(value)),
  chain: fn => fn(value),
  value,
})

const Adult = value => ({
  map: fn => Adult(fn(value)),
  chain: fn => fn(value),
  isChild: false,
  isTeen: false,
  isAdult: true,
  value,
})

const Teen = value => ({
  map: fn => Teen(fn(value)),
  chain: fn => fn(value),
  isChild: false,
  isTeen: true,
  isAdult: false,
  value,
})

const Child = value => ({
  map: fn => Child(fn(value)),
  chain: fn => fn(value),
  isChild: true,
  isTeen: false,
  isAdult: false,
  value,
}) 
```

我们还将添加用于映射和/或链接的函数。

```
const objectMapper = fn => value =>
  Object.keys(value).reduce((acc, cur, idx, arr) => ({ ...acc, [cur]: fn(value[cur]) }), value)

const makeUpper = s => (typeof s === 'string' ? s.toUpperCase() : s)

const makeLower = s => (typeof s === 'string' ? s.toLowerCase() : s)

const makeCapitalize = s => (typeof s === 'string' ? s.replace(/(?:^|\s)\S/g, a => a.toUpperCase()) : s)

const addAge = curr => add => curr + add

const setContext = obj => (obj.age < 13 ? Child(obj) : obj.age < 18 ? Teen(obj) : Adult(obj))

const agePerson = age => obj => setContext({ ...obj, age: addAge(obj.age)(age) }) 
```

让我们开始玩单子吧。

```
const p1 = {
  firstName: 'matthew',
  lastName: 'staniscia',
  hairColor: 'brown',
  age: 10,
}

Person(p1).map(objectMapper(makeUpper))

/*
Result: This is a Person Monad
{ 
  map: [Function: map],
  chain: [Function: chain],
  value:
   { 
     firstName: 'MATTHEW',
     lastName: 'STANISCIA',
     hairColor: 'BROWN',
     age: 10 
   }
}
*/

Person(p1)
  .map(objectMapper(makeUpper))
  .chain(setContext)

/*
Result: This is a Child Monad
{ 
  map: [Function: map],
  chain: [Function: chain],
  isChild: true,
  isTeen: false,
  isAdult: false,
  value:
   { 
     firstName: 'MATTHEW',
     lastName: 'STANISCIA',
     hairColor: 'BROWN',
     age: 10 
   }
}
*/

Person(p1)
  .map(objectMapper(makeUpper))
  .chain(setContext)
  .chain(agePerson(4))
  .map(objectMapper(makeLower))

/*
Result: This is a Teen Monad
{ 
  map: [Function: map],
  chain: [Function: chain],
  isChild: false,
  isTeen: true,
  isAdult: false,
  value:
   { 
     firstName: 'matthew',
     lastName: 'staniscia',
     hairColor: 'brown',
     age: 14 
   }
}
*/

Person(p1)
  .map(objectMapper(makeUpper))
  .chain(setContext)
  .chain(agePerson(4))
  .map(objectMapper(makeLower))
  .chain(agePerson(4))
  .map(objectMapper(makeCapitalize))

/*
Result: This is an Adult Monad
{ 
  map: [Function: map],
  chain: [Function: chain],
  isChild: false,
  isTeen: false,
  isAdult: true,
  value:
   { 
     firstName: 'Matthew',
     lastName: 'Staniscia',
     hairColor: 'Brown',
     age: 18 
   }
}
*/ 
```

只是为了好玩，让我们包括另一个单子。我们将使用 Pratica 库中的 Maybe monad，并添加一个函数来查看该人在美国是否可以喝酒。

```
import { Maybe } from 'pratica'

const maybeDrinkInUS = obj => (obj.age && obj.age >= 21 ? Maybe(obj) : Maybe()) 
```

运行完管道后，我们将返回数据结构或消息。

```
Person(p1)
  .map(objectMapper(makeUpper))
  .chain(setContext)
  .chain(agePerson(4))
  .map(objectMapper(makeLower))
  .chain(agePerson(4))
  .map(objectMapper(makeCapitalize))
  .chain(maybeDrinkInUS) // This returns a Maybe Monad
  .cata({
    Just: v => v,
    Nothing: () => 'This Person is too young to drink in the US',
  })

/*
Result
'This Person is too young to drink in the US'
*/

Person(p1)
  .map(objectMapper(makeUpper))
  .chain(setContext)
  .chain(agePerson(4))
  .map(objectMapper(makeLower))
  .chain(agePerson(7)) // Changed this line to now be 21
  .map(objectMapper(makeCapitalize))
  .chain(maybeDrinkInUS) // This returns a Maybe Monad
  .cata({
    Just: v => v,
    Nothing: () => 'This Person is too young to drink in the US',
  })

/*
Result
{ 
  firstName: 'Matthew',
  lastName: 'Staniscia',
  hairColor: 'Brown',
  age: 21 
}
*/ 
```

## 结论

总之，Monad 只不过是一个包装器/上下文/类，它有能力:

*   在其自己的上下文中映射数据。
*   通过映射其数据并从其上下文中提取数据来建立链。
*   满足三个一元定律。
*   它可能有额外的属性或方法与之关联。

## 来源

下面的链接帮助我理解了单子，并且能够用语言表达出来。

1.  [https://dev.to/rametta/basic-monads-in-javascript-3el3](https://dev.to/rametta/basic-monads-in-javascript-3el3)
2.  [https://www.youtube.com/watch?v=2jp8N6Ha7tY](https://www.youtube.com/watch?v=2jp8N6Ha7tY)
3.  [https://medium . com/front-end-weekly/implementing-JavaScript-functors-and-monads-a 87 b 6 a4 B4 d 9 a](https://medium.com/front-end-weekly/implementing-javascript-functors-and-monads-a87b6a4b4d9a)
4.  [https://medium . com/JavaScript-scene/JavaScript-monads-made-simple-7856 be 57 bfe 8](https://medium.com/javascript-scene/javascript-monads-made-simple-7856be57bfe8)
5.  [https://www . codingame . com/playgrounds/2980/practical-introduction-to-functional-programming-with-js/functors-and-monads](https://www.codingame.com/playgrounds/2980/practical-introduction-to-functional-programming-with-js/functors-and-monads)
6.  [https://medium . com/better-programming/tuples-in-JavaScript-57 ede 9 B1 c 9d 2](https://medium.com/better-programming/tuples-in-javascript-57ede9b1c9d2)
7.  [https://hacker noon . com/functional-JavaScript-functors-monads-and-promises-679 ce 2 ab 8 Abe](https://hackernoon.com/functional-javascript-functors-monads-and-promises-679ce2ab8abe)
8.  [https://blog . klipse . tech/JavaScript/2016/08/31/monads-JavaScript . html](https://blog.klipse.tech/javascript/2016/08/31/monads-javascript.html)
9.  [https://github.com/getify/Functional-Light-JS](https://github.com/getify/Functional-Light-JS)
10.  [https://www.youtube.com/watch?v=ZhuHCtR3xq8](https://www.youtube.com/watch?v=ZhuHCtR3xq8)
11.  [https://marmelab . com/blog/2018/09/26/functional-programming-3-functor-redone . html](https://marmelab.com/blog/2018/09/26/functional-programming-3-functor-redone.html)
12.  [https://www.npmjs.com/package/pratica](https://www.npmjs.com/package/pratica)