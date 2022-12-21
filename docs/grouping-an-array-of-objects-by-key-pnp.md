# 如何创建 groupBy 函数

> 原文：<https://dev.to/studnitz/grouping-an-array-of-objects-by-key-pnp>

我们将实现一个 groupBy 函数，当给定一个集合和一个键时，它返回一个按该键分组的新的`Map()`。

例如给定这个输入:

```
const blogPosts = [
  { category: 'javascript', author: 'Alex', title: 'Usecases for reduce' },
  { category: 'php', author: 'Alex', title: 'Getting started with phpunit' },
  { category: 'javascript', author: 'Ben', title: 'Maps and Sets' },
]

const blogPostsByCategory = ???

blogPostsByCategory.forEach(category => console.table(category)) 
```

它应该产生这样的输出(`console.table()`是一个隐藏的宝石):

```
┌─────────┬──────────────┬────────┬───────────────────────┐
│ (index) │   category   │ author │         title         │
├─────────┼──────────────┼────────┼───────────────────────┤
│    0    │ 'javascript' │ 'Alex' │ 'Usecases for reduce' │
│    1    │ 'javascript' │ 'Ben'  │    'Maps and Sets'    │
└─────────┴──────────────┴────────┴───────────────────────┘
┌─────────┬──────────┬────────┬────────────────────────────────┐
│ (index) │ category │ author │             title              │
├─────────┼──────────┼────────┼────────────────────────────────┤
│    0    │  'php'   │ 'Alex' │ 'Getting started with phpunit' │
└─────────┴──────────┴────────┴────────────────────────────────┘ 
```

我们将一步一步地做这件事，从`reduce()`方法开始，快速进入高阶函数，然后检查`Map`数据结构。最后，我们为我们的`groupBy`函数准备好了一切！

## `reduce()`

`reduce()`方法获取一个数组并将其组合成一个值。它通过执行一个**缩减函数**和**累加结果**来实现。

最常见的例子是对数组中的所有数字求和。

```
const numbers = [1, 2, 3]

const add = (a, b) => a + b

const sumOfNumbers = numbers.reduce(add)

console.log(sumOfNumbers) 
```

```
6 
```

## 高阶函数

高阶函数是可以返回一个函数或接受一个函数作为参数的函数。`map()`、`reduce()`和`filter()`是高阶函数。

### 逢迎

Curried 函数是返回带有一个参数的函数。

```
const addCurried = a => b => a + b

//    add2 is a function, which takes exactly one argument now
const add2 = addCurried(2)
//    addCurried = a => b => a + b
//    add2 =       2 => b => 2 + b
//    add2 =            b => 2 + b

const sumOfnumbersPlus2 = numbers.map(add2).reduce(add)

console.log(sumOfnumbersPlus2) 
```

```
12 
```

## `Map<Key,Value>`

`Map`是将给定的键映射到一个值的对象。密钥可以是任何东西，一个`String`、`Number`甚至一个`Object`。对于值来说也是如此:它也可能是一个`Array`，这在以后会变得很重要。

当迭代一个映射时，顺序是值的**插入顺序**。

### 以某个字符开头的人名地图

```
const namesStartingWithA = ['Alex', 'Adrian']
const namesStartingWithB = ['Ben']
const namesStartingWithC = ['Charlie', 'Carl']

const nameMap = new Map([
  ['A', namesStartingWithA],
  ['B', namesStartingWithB],
  ['C', namesStartingWithC],
])

console.table(nameMap) 
```

```
┌───────────────────┬─────┬───────────────────────┐
│ (iteration index) │ Key │        Values         │
├───────────────────┼─────┼───────────────────────┤
│         0         │ 'A' │ [ 'Alex', 'Adrian' ]  │
│         1         │ 'B' │       [ 'Ben' ]       │
│         2         │ 'C' │ [ 'Charlie', 'Carl' ] │
└───────────────────┴─────┴───────────────────────┘ 
```

#### 仅检索以“A”开头的名称

我们用`map.get(key)`来做这件事。

```
console.log(nameMap.get('A')) 
```

```
[ 'Alex', 'Adrian' ] 
```

#### 添加新名称

我们使用`map.set(newValue)`为那个键设置一个新值。

我们必须创建一个新的数组，其中我们的新名字被添加到旧名字中。我们将这个新数组设置为键值。

```
nameMap.set('A', [...namesStartingWithA, 'Alf'])
console.log(nameMap.get('A')) 
```

```
[ 'Alex', 'Adrian', 'Alf' ] 
```

#### 创建一个帮助器函数将值追加到数组中

过一会儿，通过`map.get()`获取数组会变得很乏味。幸运的是，我们可以创建一个高阶函数，它有助于将一个值附加到该键的现有数组中，或者如果该数组不存在，则创建一个新的数组。

我们使用扩展语法将旧数组与新值[连接起来。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 

```
// Higher order function, fn(map) -> fn(key) -> fn(value)
const appendValueToArray = map => key => value => {
  // if the map has that key, use that value as array, if not create a new one
  const currentValues = map.get(key) || []

  return map.set(key, [...currentValues, value])
}

appendValueToArray(nameMap)('A')('Albert')
console.log(nameMap.get('A')) 
```

```
[ 'Alex', 'Adrian', 'Alf', 'Albert' ] 
```

## 将博客文章按类别分组

现在，我们已经拥有了创建 reduce 函数所需的所有工具，该函数可以根据博客文章的类别对它们进行分组。让我们从“创建”一些博客文章开始。

```
const blogPosts = [
  { category: 'javascript', author: 'Alex', title: 'Usecases for reduce' },
  { category: 'php', author: 'Alex', title: 'Getting started with phpunit' },
  { category: 'javascript', author: 'Ben', title: 'Maps and Sets' },
] 
```

首先，我们利用我们的`appendValueToArray()`方法创建我们的 reducer 函数。我们通过做一个[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring)得到博文的类别。

```
// returns the map with our new value appended for the array of the category key
const groupByCategory = (blogPostCategoryMap, blogPost) => {
  const { category } = blogPost
  const addBlogPostsToCategory = appendValueToArray(blogPostCategoryMap)

  return addBlogPostsToCategory(category)(blogPost)
} 
```

现在，缺少的按类别获取我们的博客文章的`reduce()`调用只是一个可爱的小行。注意，我们必须为空`new Map()`的缩减器提供初始值。

```
const blogPostsByCategory = blogPosts.reduce(groupByCategory, new Map()) 
```

然后我们可以用`map.forEach(value, key)`在地图上迭代，得到`blogPostsByCategory`的漂亮输出。

```
blogPostsByCategory.forEach((posts, category) => {
  console.log(`Category: ${category}`)
  console.table(posts)
}) 
```

```
Category: javascript
┌─────────┬──────────────┬────────┬───────────────────────┐
│ (index) │   category   │ author │         title         │
├─────────┼──────────────┼────────┼───────────────────────┤
│    0    │ 'javascript' │ 'Alex' │ 'Usecases for reduce' │
│    1    │ 'javascript' │ 'Ben'  │    'Maps and Sets'    │
└─────────┴──────────────┴────────┴───────────────────────┘
Category: php
┌─────────┬──────────┬────────┬────────────────────────────────┐
│ (index) │ category │ author │             title              │
├─────────┼──────────┼────────┼────────────────────────────────┤
│    0    │  'php'   │ 'Alex' │ 'Getting started with phpunit' │
└─────────┴──────────┴────────┴────────────────────────────────┘ 
```

## 实现通用`groupBy(key)`

我们可以通过实现一个通用的 groupBy 方法来进一步概括这一点，该方法根据任何给定的键进行分组。

```
const groupByKey = key => (mapGroupedByKey, entry) => {
  const addEntryToKey = appendValueToArray(mapGroupedByKey)(entry[key])
  return addEntryToKey(entry)
}

const groupedByKey = (array, key) => array.reduce(groupByKey(key), new Map())

const blogPostsByKey = key => groupedByKey(blogPosts, key)

const blogPostsByAuthor = blogPostsByKey('author')

const blogPostsByCategoryNew = blogPostsByKey('category')

blogPostsByAuthor.forEach(posts => console.table(posts)) 
```

```
┌─────────┬──────────────┬────────┬────────────────────────────────┐
│ (index) │   category   │ author │             title              │
├─────────┼──────────────┼────────┼────────────────────────────────┤
│    0    │ 'javascript' │ 'Alex' │     'Usecases for reduce'      │
│    1    │    'php'     │ 'Alex' │ 'Getting started with phpunit' │
└─────────┴──────────────┴────────┴────────────────────────────────┘
┌─────────┬──────────────┬────────┬─────────────────┐
│ (index) │   category   │ author │      title      │
├─────────┼──────────────┼────────┼─────────────────┤
│    0    │ 'javascript' │ 'Ben'  │ 'Maps and Sets' │
└─────────┴──────────────┴────────┴─────────────────┘ 
```

如果你很难理解这些概念，不要担心，这需要一些时间。但是它们在以声明方式处理数据方面非常强大。

```
const amountOfBlogPostsByAuthor = author => blogPostsByAuthor.get(author).length

const alexBlogPostsCount = amountOfBlogPostsByAuthor('Alex')

console.log(alexBlogPostsCount) 
```

```
2 
```

## 结论

在本指南中，我们学习了如何利用`array.reduce()`、`Map`和高阶函数来创建一个相当灵活的方法，通过一些键对数组中的对象进行分组。

你有什么改进或者其他好主意吗？让我知道！

你也可以在推特上用@jvstudnitz 或者在[我的博客](https://studnitz.dev/)上找到我。