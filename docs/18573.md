# 休息和伸展指南:第 1 部分

> 原文：<https://dev.to/guin/what-does-mean-in-javascript-a-guide-to-rest-and-spread-part-1-1fmd>

## 什么是休止参数和传播算子？

rest 参数和 spread 操作符都在 JavaScript 代码中表示为`...`,这一开始可能会令人困惑，因为它们做的是相反的事情。

**Rest 参数:**将所有剩余的元素或参数收集到一个数组中。

**Spread operator:** 允许将数组、字符串或对象等可迭代对象扩展为单个参数或元素。

让我们看一些例子来理解上面的意思。

## 潜入休息参数

在 JavaScript 中定义函数时，我们可以添加参数。参数描述调用函数时将提供的参数。在下面的代码中，我们有一个函数`puppy`的函数定义，它返回一只小狗的描述。名字、品种和大小是函数的参数。调用函数时，Spot、Dachshund 和 small 是提供的参数。

```
function puppy(name, breed, size){
  var description = `${name} is a ${size}  ${breed}`
  return description
}

puppy("Spot", "Dachshund", "small") 
```

我们可以给一个叫做 rest 参数的函数添加另一种特殊的参数。此参数将获取提供的所有剩余参数，并将它们收集到一个数组中。因为它收集所有剩余的参数，所以它必须是函数定义中提供的最后一个参数。

只要使用`...something`语法，您可以随意称呼它。下面我用的是`...traits`。如果提供了特征，小狗函数现在返回一个更新的描述。

```
function puppy(name, breed, size, ...traits){
  console.log(traits)
  var description = `${name} is a ${size}  ${breed}.`
  if (traits.length > 0) {
    return `${description} They have the following traits: ${traits.join(', ')}`
  }
  return description
} 
```

现在，如果我们在调用函数和控制台日志`traits`时添加更多参数，我们将获得作为数组的额外参数。如果没有提供额外的参数，那么`traits`将是一个空数组。

```
puppy("Spot", "Dachshund", "small", "playful", "intelligent", "energetic", "stubborn")
// traits will be logged as:
// `[ 'playful', 'intelligent', 'energetic', 'stubborn' ]`.
// the function will return: 
// 'Spot is a small Dachshund. They have the following traits: playful, intelligent, energetic, stubborn'
puppy("Spot", "Dachshund", "small")
// traits will be logged as []
// the function will return:
// 'Spot is a small Dachshund.' 
```

## rest 操作符和`arguments`对象有什么不同？

当编写 JavaScript 函数时，我们也可以访问一个`arguments`对象。arguments 对象包含传递给函数的所有参数。乍一看，我们可能认为这两者之间没有区别

1.  将 rest 参数作为唯一的参数传递给函数
2.  参数对象

然而，在这种情况下使用 rest 参数的好处是 rest 参数返回一个实际的数组实例。这意味着我们可以直接在它上面使用任何数组方法。arguments 对象不是真正的数组。尝试在其上调用数组方法将导致错误。

### 使用`arguments`对象:

如果我们有一个新函数`puppyTraits`并用一些参数调用它，我们可以记录 arguments 对象来获取所有传入的参数。但是如果我们试图直接在对象上使用数组方法，我们会得到一个错误。如果我们想的话，可以通过使用`Array.from(arguments)`将对象转换成数组实例来解决这个问题。然而，如果我们使用 rest 操作符，这是不必要的。

```
function puppyTraits(){
  console.log(arguments)
  return `This puppy has the following traits: ${arguments.join(', ')}`
}

puppyTraits("playful", "intelligent", "energetic", "stubborn")
// arguments will be logged as:
/* [Arguments] {
  '0': 'playful',
  '1': 'intelligent',
  '2': 'energetic',
  '3': 'stubborn' }
*/
// the function will not return as there will be an error:
// TypeError: arguments.join is not a function 
```

### 使用 rest 运算符

使用 rest 参数代替 arguments 函数的相同函数。因为 traits 现在是一个数组，所以我们可以在代码中对它使用任何数组方法！

```
function puppyTraits(...traits){
  console.log(traits)
  return `This puppy has the following traits: ${traits.join(', ')}`
}

puppyTraits("playful", "intelligent", "energetic", "stubborn")
// traits will be logged as:
// [ 'playful', 'intelligent', 'energetic', 'stubborn' ]
// the function will return:
// 'This puppy has the following traits: playful, intelligent, energetic, stubborn' 
```

希望您已经对 rest 参数在 JavaScript 中的作用及其一些用例有了更好的理解。我们将在第 2 部分深入研究 spread 操作符！

如果您有任何问题、意见或反馈，请告诉我。关注每周关于 JavaScript、React、Python 和 Django 的新帖子！