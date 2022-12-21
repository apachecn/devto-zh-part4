# 带验证的 Javascript 可组合解码器

> 原文：<https://dev.to/kwirke/javascript-composable-decoders-with-validation-1ldh>

我是个多疑的人。如果我在床边找不到拖鞋，我会怀疑。如果我看到一只穿着外套的猫看着我的方向，我会怀疑。同样，当我从外部来源接收数据时，我会怀疑。

不管吹嘘或规范说什么，如果您从 API 或任何其他外部来源接收数据，知道它满足格式和任何业务限制总是好的。这就是 TypeScript 类型不再帮助你的地方。即使有很好的库来帮助弥补这个缺口，比如 [io-ts](https://github.com/gcanti/io-ts) ，您可能仍然会错过一个跟踪发生了什么故障以及故障发生在哪里的好方法。

在这里，我将描述我如何实现一个 Javascript 解码器，它累积接收数据的错误，同时在解码过程后保留所有需要的数据。你可以在这里看到完整的片段。

## 问题

为了说明这个例子，我将收到一个视频游戏数据列表，如下所示:

```
const videogames = [
  {id: 1, name: 'Doom', genre: 'FPS', rating: 7},
  {id: 2, name: 'NieR: Automata', genre: 'Action RPG', rating: 100},
  {id: 3, name: 'Dead Cells', genre: 'Rogue-like', rating: 8},
] 
```

我们还会有一些限制，即:

*   数据集必须是一组视频游戏。
*   一个电子游戏必须有一个`id`、一个`name`、一个`genre`和一个`rating`。
*   `name`不能为空
*   必须是我们认可的类型之一(FPS、RPG、模拟器、策略和平台)。
*   `rating`必须介于 1 和 10 之间。

如果您是一个敏锐的观察者，您会看到示例数据集已经打破了其中的一些限制。非常好。

我们想要的是能够解析这种数据集，知道发生的所有错误，以便我们可以通知用户或开发人员，并在方便时保留或丢弃无效数据。

## 解决它的工具

为了做到这一点，我将使用我的库[验证](https://www.npmjs.com/package/@rexform/validation)。验证是一个单子。monad 是一种类型/类的软件模式，具有某些功能和某些限制。成为单子意味着两件事:

*   它是某种东西(在我们的例子中是值)的包装器，可以使用`Validation.of(value)`来构造(这被称为 Applicative)。
*   它实现了“可映射”或者有一个`map`函数(这叫做仿函数)，和“可链接”，或者一个`chain`函数(还有这个，链)。

映射一个单子意味着将一个函数应用于它的值而不修改容器，就像它是一个数组一样:`[1, 2].map(x => x + 1) === [2, 3]`

链接一个 monad 意味着将一个函数应用到它的值，并为该函数返回的值更改容器。它也被称为 flatMap，因为如果您映射一个返回另一个容器的函数，并且不将结果扁平化，那么您最终会在一个容器中得到一个容器:
`[1, 2].map(x => [x, 0]) === [[1, 0], [2, 0]]`，但是
`[1, 2].flatMap(x => [x, 0]) === [1, 0, 2, 0]`

`Validation<E, V>`是单子，可以有两种类型:`Valid`和`Invalid`。`E`和`V`这里是验证包装的两个值的通用类型:它的错误和值。

一个`Valid`类型只存储了`V`类型的一些数据，并确认它到目前为止是“有效”的。可以用`Validation.of`构建，也可以用`Validation.valid`构建

一个`Invalid`类型存储两个值:一些类型为`V`的无效数据，以及一个类型为`E`的错误列表。可以使用`Validation.invalid`进行构建。

有了所有这些，我们可以验证这样一个特定的评级:

```
const ratingDecoder = rating => (
  isBetween(1, 10)(rating)
    ? Validation.valid(rating)
    : Validation.invalid(rating, `Rating must be between 1 and 10, but received ${rating}`)
) 
```

在这里，如果评级满足限制，我们将返回一个`Valid(rating)`，如果评级不满足限制，我们将返回`Invalid(rating, errorMessage)`。

我们这里有其他验证库没有提供的 unicorn 东西，即*我们保留评级值*，即使我们知道它是无效的，因为我们以后可能需要这个信息。

## 计划

好吧，那计划是什么？我们怎么用这个来解码所有的数据呢？

首先，我们想递归地做这件事，其次，用可组合的解码器来描述我们的数据形状。比如我们的`videogameDecoder`会是这样的:

```
const videogameDecoder = videogame => doSomeStuff({ // We'll complete this later
  id: idDecoder,
  name: nameDecoder,
  genre: genreDecoder,
  rating: ratingDecoder,
}, videogame) 
```

这样，`videogameDecoder`有两个目的:

*   它是一个解码器功能，返回一个经过验证的视频游戏。
*   这是一个视频游戏类型形状的声明，就像`PropTypes`。当我们没有 TypeScript 时，这也是一个很好的文档。

我们将在所有层次、所有形状和类型上这样做。在我们的例子中，这是我们的四个属性(`id`、`name`、`genre`和`rating`)、我们的`videogame`类型和我们的`videogameArray`类型。让我们开始吧:

## 解码取乐

我们将从`nameDecoder`函数开始。假设我们有一个函数`isFilled(str)`告诉我们一个名字是否非空，我们可以做一些类似于之前的`ratingDecoder`的事情:

```
const nameDecoder = name => (
  isFilled(name)
    ? valid(name)
    : invalid(name, 'name can not be empty')
) 
```

所以我们必须对所有属性都这样做。这不是有点老套吗？幸运的是，验证有几个助手，其中之一是`fromPredicateOr(errorFn, predicate)`。它可以用来创建一个函数，该函数将接收我们的值并返回对它的验证。我们来看看怎么用:

```
const nameDecoder = fromPredicateOr(() => 'name can not be empty', isFilled) 
```

好多了！现在，当我们调用 nameDecoder 时，它将检查是否已填充，并根据其真实性返回有效或无效。

此外，如果不是视频游戏的另一种类型需要有一个不能为空的名称，我们可以重用这个解码器！

我们现在已经解码了所有的属性:

```
const idDecoder = valid
const nameDecoder = fromPredicateOr(() => 'name can not be empty', isFilled)
const genreDecoder = fromPredicateOr(() => 'genre must be in validGenres', flip(includes)(validGenres))
const ratingDecoder = fromPredicateOr(() => 'rating must be between 1 and 10', isBetween(1, 10)) 
```

那`idDecoder`呢？它没有任何限制，所以它总是有效的，但是我们仍然需要提供一个验证，所以我们将直接使用有效的构造函数。

## 电子游戏类型

我们想要的函数`videogameDecoder`将接收一个视频游戏对象，验证它的每一个属性，然后将所有验证分组(或*减少*)成一个单独的验证:

```
// Videogame
{
  id: 3,
  name: 'Dead Cells',
  genre: 'Rogue-like',
  rating: 8
}

// Videogame with validated attributes
{
  id: valid(3),
  name: valid('Dead Cells'),
  genre: invalid('Rogue-like', ['genre is not in validGenres']),
  rating: valid(8),
}

// Validated videogame without invalid properties
invalid({
  id: 3,
  name: 'Dead Cells',
  rating: 8,
}, ['genre is not in validGenres']) 
```

注意，在最后一步中，我们选择不保留无效值。不需要那样，我们可以选择保留它们，但是在这个例子中我们不再需要它们了。

为了完成第一步，我们可以使用来自 Ramda 的 [`evolve`](https://ramdajs.com/docs/#evolve) 方法，但是我们不会这样做，因为它没有对缺失的属性应用任何函数，并且我们想要检测一个缺失的属性来表明它是无效的。

我们还可以迭代对象属性:

```
const validations = {id: idDecoder, name: nameDecoder, /* ... */}

Object.keys(validations).reduce(
  (acc, k) => ({
    ...acc,
    [k]: property(k, videogame).chain(val => validations[k](val)),
  }),
  {}
) 
```

请注意，在第四行中，我们使用了`Validation.property`方法，如果找到该属性，则返回一个有效的，否则返回一个无效的。然后，我们将它链接到一个函数，如果该属性的验证通过，该函数将返回一个有效的，否则返回一个无效的。

这是怎么回事？

当我们链接验证时，它会记住我们之前检测到的错误，并将它们添加到任何新的错误中。它会这样表现:

```
valid(1).chain(n => valid(2)) === valid(2)
valid(1).chain(n => invalid(2, ['error'])) === invalid(2, ['error'])
invalid(1, ['error1']).chain(n => invalid(2, ['error2']) === invalid(2, ['error1', 'error2']) 
```

这样，关于错误的信息被保留下来。

我们将使用另一个验证助手:`validateProperties`，而不是这样做。这正是我们想要的:

```
const videogameWithValidatedProperties = validateProperties({
  id: idDecoder,
  name: nameDecoder,
  /* ... */
}, videogame) 
```

到了第二步，也是最后一步，我们需要遍历对象属性，只添加有效的属性。我们可以用`Validation.isValid()`方法检查这个，用`Validation.value` :
访问里面的值

```
const allProperties = obj => (
  Object.keys(obj).reduce((validatedObj, k) => (
    validatedObj.chain(validObj => obj[k].isValid()
      ? Validation.of({...validObj, [k]: obj[k].value})
      : obj[k].map(() => validObj)
    )),
    valid({})
  )
) 
```

然而，这是一个复杂的函数，也是一个足够常见的函数，有自己的助手，`Validation.allProperties`，所以我们将使用它。

最后，我们将非常简短地感谢帮助者:

```
const videogameDecoder = videogame => {
    const videogameWithValidatedProperties = Validation.validateProperties({
        id: idDecoder,
        name: nameDecoder,
        genre: genreDecoder,
        rating: ratingDecoder,
    }, videogame)
    return Validation.allProperties(videogameWithValidatedProperties)
} 
```

如果我们在 Ramda:
的[管道](https://ramdajs.com/docs/#pipe)函数的帮助下，使用无点风格重构它，我们可以改善这一点

```
const videogameDecoder = pipe(
    Validation.validateProperties({
        id: idDecoder,
        name: nameDecoder,
        genre: genreDecoder,
        rating: ratingDecoder,
    }),
    Validation.allProperties,
) 
```

## 验证数组

正如验证有一些助手来处理对象一样，它也有一些助手来处理数组。

事实证明，这些操作在函数式编程世界中定义得很好，因为 FP 喜欢列表。进入**幺半群**。

同单子一样，幺半群也是另一种编程模式(尽管它们没有太多共同点，即使名字看起来很相似)。如果一个类型是“可串联的”并且有一个返回空元素的“空”函数，那么它就是幺半群。

因此，幺半群总是有两个函数:

*   `empty`返回空元素。对于数组，这将是`[]`。
*   `concat`连接两个幺半群的值并返回另一个幺半群。对于数组，这将是`Array.concat`。

这意味着 JS 数组是一个幺半群，如果它们有一个返回`[]`的`.empty()`方法，它们甚至会是[静态土地](https://github.com/fantasyland/static-land)兼容的。但是他们没有。

事实证明，当包装的值是一个数组时，验证是一个静态的 Land 兼容幺半群(如果不是，则在连接时转换为数组)。这意味着我们掌握了幺半群的全部力量。

## 列表类型

现在是最后一个函数:`videogameArrayDecoder`。它接收一个视频游戏数组，并返回该数组的验证结果。

我们可以像之前一样分两步来做:验证每个视频游戏，然后将验证累积( *reduce* )成一个验证。

```
// Array of videogames
[vg1, vg2, vg3]

// Array of videogame Validations
[valid(vg1), invalid(vg2, err2), invalid(vg3, err3)]

// Validation of array of videogames
invalid([vg1], [...err2, ...err3]) 
```

请注意，就像之前一样，在最后一步中，我们将从列表中删除无效的视频游戏，因为我们想要这样做。

为了验证每一个视频游戏，我们可以用一个常规的`Array.map`来做，就像这样:

```
const validatedVideogames = videogames.map(videogameDecoder) 
```

咿咿呀呀。对于第二步，我们希望将验证数组简化为数组验证。我们知道，当值是数组时，验证就像幺半群一样，所以让我们把它们映射到一个元素数组:

```
const toArrayValidation = Validation.map(x => [x])
const videogameArrayValidations = validatedVideogames.map(toArrayValidation) 
```

现在我们准备连接它们，因为它们包含数组。方法连接有效值，并丢弃无效值，正如我们所希望的那样。这意味着我们可以如下简化列表:

```
const videogamesValidation = videogameArrayValidations
    .reduce(Validation.concat, Validation.empty()) 
```

这看起来很棒，因为这正是用幺半群生成列表的定义。它太棒了，在图书馆里有自己的功能:

```
const videogamesValidation = Validation.sequence(videogameArrayValidations) 
```

如果我们想保留无效的值，我们必须用另一种方法:

```
const losslessSequence = l => l.reduce((valList, val) => (
  valList.chain(list => val.map(x => [...list, ...x]))
), Validation.empty())

const videogamesValidation = losslessSequence(videogameArrayValidations) 
```

通过在`chain`中使用`map`，我们正在做的是在每次迭代中连接新验证中的所有值，然后将其链接到原始值以保留错误，因为`chain`函数保留了所有错误。

那么解码器会是什么样子呢？

```
const videogameArrayDecoder = videogames => {
    const validatedVideogames = videogames.map(videogameDecoder)
    return Validation.sequence(validatedVideogames)
} 
```

如果我们使用无点风格和 Ramda 重构它，我们得到这个:

```
const videogameArrayDecoder = pipe(map(videogameDecoder), Validation.sequence) 
```

## 结果

最后，这是我们整个解码器的完整代码:

```
const {Validation, valid, invalid} = require("@rexform/validation")
const {isNil, isEmpty, complement, either, includes, flip, both, lte, gte, pipe, map} = require('ramda')

const videogames = [
    {id: 1, name: 'Doom', genre: 'FPS', rating: 7},
    {id: 2, name: 'NieR: Automata', genre: 'Action RPG', rating: 100},
    {id: 3, name: 'Dead Cells', genre: 'Rogue-like', rating: 8},
]

const validGenres = ['FPS', 'Platforms', 'RPG', 'Strategy', 'Simulator']

const isFilled = complement(either(isNil, isEmpty))
const isBetween = (a, b) => both(flip(lte)(b), flip(gte)(a))

const nameDecoder = Validation.fromPredicateOr(() => 'name can not be empty', isFilled)
const genreDecoder = Validation.fromPredicateOr(() => 'genre must be in validGenres', flip(includes)(validGenres))
const ratingDecoder = Validation.fromPredicateOr(() => 'rating must be between 1 and 10', isBetween(1, 10))

const videogameDecoder = pipe(
    Validation.validateProperties({
        id: valid,
        name: nameDecoder,
        genre: genreDecoder,
        rating: ratingDecoder,
    }),
    Validation.allProperties,
)

const videogameArrayDecoder = pipe(map(videogameDecoder), Validation.sequence)

videogameArrayDecoder(videogames) 
```

而这就是结果:

```
Invalid(
  [{id: 1, name: 'Doom', genre: 'FPS', rating: 7}],
  [
    "genre must be in validGenres",
    "rating must be between 1 and 10",
    "genre must be in validGenres",
  ]
) 
```

唯一遗漏的问题是，当我们看到错误时，我们不知道是什么视频游戏产生了它们。如果我们回到 videogameDecoder 并在错误消息中添加 videogame `id`(或者，如果我们愿意，将整个 videogame 对象字符串化，而不是 id ),我们就可以解决这个问题。

我们可以使用函数`mapError`将`id`添加到错误消息中。函数`mapError`的工作方式类似于`map`，但是是针对包装的错误数组而不是包装的值。它只会修改每个错误，而不会改变验证:

```
const videogameDecoder = pipe(
    Validation.validateProperties({
        id: valid,
        name: nameDecoder,
        genre: genreDecoder,
        rating: ratingDecoder,
    }),
    Validation.allProperties,
    videogame => videogame.mapError(e => `In ID=${videogame.value.id}: ${e}`),
) 
```

就是这样，现在结果会有更多的意义:

```
Invalid(
  [{id: 1, name: 'Doom', genre: 'FPS', rating: 7}],
  [
    "In ID=2: genre must be in validGenres",
    "In ID=2: rating must be between 1 and 10",
    "In ID=3: genre must be in validGenres",
  ]
) 
```

我们完成了新的视频游戏解码器，万岁！😄

### 感谢

如果你到了这一步，谢谢你！这是我的第一篇文章，欢迎任何建议或反馈。我希望你从中学到了一些东西，但是如果你没有，也许你可以教我一些东西！

同样，如果你喜欢，试试[验证](https://www.npmjs.com/package/@rexform/validation)😉