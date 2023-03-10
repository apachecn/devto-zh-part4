# 整理 Javascript 排序

> 原文：<https://dev.to/jenshaw/sorting-out-javascript-sort-4kbl>

## 你不知道排序

直到最近，我还真的低估了 Javascript 的 [sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) 方法的能力。

这是一个很容易被认为是理所当然的工具，尤其是在你可以在一个数组上调用它的情况下，不需要任何额外的努力，看着它神奇地按照你期望的顺序重新排列它的元素。

```
// Orders names alphabetically 
let myPets = ["Oreo", "Shortbread", "Peanut", "Souffie", "Tella"];
pets.sort();
// => [ 'Oreo', 'Peanut', 'Shortbread', 'Souffie', 'Tella' ]

// Order numbers numerically
let numbers = [3, 2, 1, 6, 5, 4];
numbers.sort();
// => [ 1, 2, 3, 4, 5, 6 ]

// Don't stop reading here! You know nothing yet! 
```

然而，`sort`本身并不完全像人们所期望的那样运行。例如，当调用`amazingInventions`(一个大写和小写单词的数组)时，`sort`会将所有大写单词排在小写单词之前。这有点奇怪，也有点不方便，但是我可以看到这里面有一些*和*的逻辑，所以我还不准备为此大做文章。

```
let amazingInventions = ['computer', 'Internet', 'telegraph', 'vaccines'];
amazingInventions.sort();
// returns . . . . . . . [ 'Internet', 'computer', 'telegraph', 'vaccines' ]
// when you expected . . [ 'computer', 'Internet', 'telegraph', 'vaccines' ] 
```

这个函数似乎也对数字进行排序，直到您在数组中引入多位数和负数，这时您才真正开始注意到有些事情不太对劲。

```
let complexNumbers = [1, 3, -2, -1, 5, 11];
complexNumbers.sort();
// returns . . . . . . . [ -1, -2, 1, 11, 3, 5 ]
// when you expected . . [ -2, -1, 1, 3, 5, 11 ] 
```

在上例中，`sort`将`-1`放在`-2`之前，并将`11`插在`1`和`3`之间。这个顺序显然毫无意义，那么这是怎么发生的呢？

## 排序的工作原理

原来 Javascript 的`sort`对数字的排序就像字典对单词的排序一样。还记得你小时候学习如何从左到右一个字母一个字母地排列单词吗？`sort`在这里做着同样的事情。不管你输入的是字符串数组还是数字数组，或者两者都是，它都会像解释字符串一样解释每个元素，并根据它的 [Unicode 码位](https://www.unicode.org/Public/UCD/latest/charts/CodeCharts.pdf)一次一个字符单元地对元素进行系统排序。

让我们自己来看看这个。下面，我们有一个包含各种字符的数组。它包括一个小写字母和一个大写字母，一位数和两位数的数字，我们还将抛出一个美元符号来衡量。

```
let randomStuff = ["a", "A", "1", "2" "12", "$"]; 
```

为了使这一点更清楚，我将使用 [`charCodeAt()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt) 并创建一个方便的引用，将每个元素指向其第一个*角色的角色代码。不用担心过程，只关注回报就好。* 

```
charCodes = {}
for(let el of randomStuff) {
charCodes[el] = el.charCodeAt(0)
}
// => { 
// '1':  49, 
// '2':  50, 
// '12': 49, 
// '$':  36, 
//  A:   65, 
//  a:   97 } 
```

你会注意到`1`和`12`共享相同的第一个字符 **1** ，因此它们也共享相同的*第一个*字符代码 **49** 。所以按照这种只比较第一个字符的逻辑，`12`将排在`2`之前，因为`sort`正在使用`12`的第一个数字的字符代码与`2`的进行比较

让我们只使用`.sort()`对数组进行排序，我们将得到这个返回。

```
arr.sort();
// => [ '$', '1', '12', '2', 'A', 'a' ] 
```

因此，了解到`sort`逐个字符地查看元素并通过字符代码进行比较，大写的`A`将出现在小写的`a`之前，并且`$`将出现在所有其他元素之前，这是有道理的。`sort`仍在以数字顺序重新排列元素，但严格按每个字符的字符编码排列。是的，结果看起来仍然不可靠，但至少我们现在明白它不是完全随机的，它确实遵循一套可预测的规则。

## 我们来整理一下东西吧！

既然我们对`.sort()`有了更多的了解，我们就可以利用它是一个[高阶函数](https://eloquentjavascript.net/05_higher_order.html)的优势，真正发挥它的最大潜力。在解释这一点时，我会尽量不重复，但是，高阶函数是一种可以将另一个函数作为参数或者返回值本身就是函数的函数。我们使用的其他常见高阶函数的例子有`forEach`、`map`、`filter`和`reduce`。

在`sort`的上下文中，我们想要传入一个“比较函数”,能够这样做的最好的部分是，我们真的可以让`sort`精确地做我们想要做的事情，无论是完全按字母、数字还是按属性对数组元素进行排序。我们可以做很多事情！

### 纯粹按字母顺序排序

我前世是一名英语老师，所以看到单词先用大写字母再用小写字母“按字母顺序排列”真的让我很不爽。这和你在字典里看到的不一样，所以没有理由让`sort`逃脱这种行为。

为了修复`sort`的错误字母顺序，我们的比较函数将执行以下操作:

*   比较单词，一次两个
*   比较前的小写单词，以防止区分大小写单词！(*注意:*这最终不会影响元素，但是它很好地设置了配对，以便进行公平的比较)
*   应用以下逻辑:如果字`a`的字符编码低于字`b`的字符编码，则返回`-1`，否则返回`1`

这里我们应用的逻辑很重要，因为返回值决定了我们如何对每个元素进行排序。负返回意味着`a`应该在`b`之前排序，正返回意味着`b`应该在`a`之前排序。

```
let pWords = ["Paris", "panic", "potato", "Portugal"]

pWords.sort() // applying only .sort()
// => [ 'Paris', 'Portugal', 'panic', 'potato' ] -- BAD.

// create compare function
function compareWords(a,b) {
  if (a.toLowerCase() < b.toLowerCase()) {
    return -1; 
  } else { 
    return 1;
  }
}

// pass compareWords function into .sort()
pWords.sort(compareWords)
// => [ 'panic', 'Paris', 'Portugal', 'potato' ] -- MUCH BETTER. 
```

这正是我想要的排序，我感觉好多了。仅仅因为我喜欢我的代码看起来简洁，我可能会使用一个箭头函数和一个三元运算符来精简它。

```
pWords.sort((a,b) => a.toLowerCase() < b.toLowerCase() ? -1 : 1) 
```

不错！

要按相反的字母顺序排序，只需颠倒比较运算符。

```
pWords.sort((a,b) => a.toLowerCase() > b.toLowerCase() ? -1 : 1) 
```

### 数字顺序

对数值数组进行排序的比较函数使用了与`compareWords`相同的概念。同样，我们将比较两个元素`a`和`b`，但是这次使用减法运算符。

同样，如果差值返回负值，`a`排在`b`之前，如果差值返回正值，`b`排在`a`之前。

```
let numbers = [1, 101, 23, 18]

// You could do it this way
function compareNumbers(a,b) {
  return a - b;
}
numbers.sort(compareNumbers);

// But this is much simpler
numbers.sort((a,b) => a - b);
// => [ 1, 18, 23, 101 ] 
```

### 按字长排序

这里我们可以更有创意一点，我们可以按单词长度排序，而不是按字母顺序排序。还记得我们如何排序数字吗？很像那么回事。我们不再比较字母，而是比较一个单词中字符的*数*，这就是为什么“Paris”和“panic”的顺序并不重要。

```
pWords.sort((a,b) => a.length - b.length)
// => [ 'Paris', 'panic', 'potato', 'Portugal' ] 
```

### 按属性排序对象

这就是`sort`变得真正有趣的地方。假设我们有一组物体。我创建了一个包含麦当劳汉堡的小样本的数组。每个物件中都有汉堡的名称、卡路里数和制作汉堡的配料清单。

我可以用许多不同的方法对这个汉堡数组进行排序，每种方法都有不同的属性。首先，我将按照汉堡名称的字母顺序进行排序。

为此，我们将遵循字母或数字比较函数的结构，但这一次，我们将把对象的属性名称链接到变量`a`和`b`。

```
let McDBurgers = [
  {name: "hamburger",
   calories: 250,
   ingredients: ["bun", "beef patty", "ketchup", "pickle slices", "onions", "mustard"]},
  {name: "Cheeseburger",
   calories: 300,
   ingredients: ["bun", "beef patty", "american cheese", "ketchup", "pickle slices", "onions", "mustard"]},
  {name: "McChicken",
   calories: 410,
   ingredients: ["bun", "chicken patty", "lettuce", "mayonnaise"]},
  {name: "Filet-O-Fish",
   calories: 390,
   ingredients: ["bun", "fish filet patty", "american cheese", "tartar sauce"]}  
 ];

// Sort by burger name
McDBurgers.sort((a,b) => a.name.toLowerCase() < b.name.toLowerCase() ? -1 : 1)

//=> [
//  {
//    name: 'Cheeseburger',
//    calories: 300,
//    ...
//  },
//  {
//    name: 'Filet-O-Fish',
//    calories: 390,
//    ...
//  },
//  {
//    name: 'hamburger',
//    calories: 250,
//    ...
//  },
//  {
//    name: 'McChicken',
//    calories: 410,
//    ...
//  }
//] 
```

看啊！我们的汉堡是按照字母顺序整齐排列的！

我可以更进一步，按卡路里数或独特成分数来排序。

```
// sort by calorie count
McDBurgers.sort((a,b) => a.calories - b.calories)

// sort by number of unique ingredients
McDBurgers.sort((a,b) => a.ingredients.length - b.ingredients.length) 
```

如果我们运行这些行，我们应该能够看到每个麦当劳汉堡的汉堡对象相应地重新排列。将每一行代码插入到您的控制台中，自己看看会得到什么样的回报！然后，尝试探索您可以操纵`sort`对阵列进行排序的其他方法！