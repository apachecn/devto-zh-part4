# PureScript 中的实用镜头和光学

> 原文：<https://dev.to/thomashoneyman/practical-profunctor-lenses-optics-in-purescript-3fgc>

光学是函数式编程中最有价值的工具之一。您可以使用它们来操纵数据结构中的值，而不需要样板文件——您只需要像`profunctor-lenses`这样的光学库中的一些类型和函数。

考虑使用像这样的深度嵌套的数据结构:

```
type NestedData =
  Maybe (Array { foo :: Tuple String (Either Int Boolean), bar :: String }) 
```

面对这种类型，问问自己:

*   给定外部数组中的一个索引，我应该写什么代码来更新深度嵌套的`Int`值？我的代码会简洁、易读、易维护吗？
*   获取、设置和修改`Array`、`foo`或`bar`值需要多少个函数？`foo`内的内值？
*   如果这种类型改变了——例如，`Maybe (Array { foo :: ... })`变成了`Array { foo :: Maybe ... }`，我能多快多容易地更新我的代码？我需要更新多少功能？

如果你不喜欢你的答案，继续读下去:profunctor 光学系统可以帮助你轻松地操纵任何一层复杂的结构，就像这个一样。

这篇文章将教你使用[profuctor 镜头](https://thomashoneyman.com/articles/practical-profunctor-lenses-optics)提高效率，即使你以前没有使用过它们。你将学习如何使用四种最常见的光学元件- **透镜**、**棱镜**、**移动器**和**iso**-来解决实际问题。

如果你在寻找更全面的介绍，我推荐布莱恩·马里克的书《普通人的镜头》。

原创文章:[实用的 Profunctor 镜头&纯脚本光学](https://thomashoneyman.com/articles/practical-profunctor-lenses-optics)

#### 章节

1.  [使用 profunctor 光学系统的简要演示](#profunctor-lenses-optics-in-action)
2.  [备忘单:通用类型、函数和构造函数](#cheatsheet-types-functions-and-constructors)
3.  [`Lens`、镜头功能、构造镜头、`At`镜头](#lenses)
4.  [`Prism`、棱镜功能、构造棱镜](#prisms)
5.  [组装透镜、棱镜和其他光学元件的技巧](#tips-for-composing-optics)
6.  [`Traversal`、遍历函数和`Index`遍历](#traversals)
7.  [`Iso`、iso 函数、构造 iso](#isos)
8.  [相关资源&收尾](#wrapping-up)

## 光学镜头&光学在行动

让我们使用我们深度嵌套的数据类型来证明 profunctor optics 的灵活性和强大功能。

```
type NestedData =
  Maybe (Array { foo :: Tuple String (Either Int Boolean), bar :: String }) 
```

为了检索或更新`NestedData`中的`Int`值，给定外部数组中的一个索引，我们可能会编写如下函数:

```
getNestedInt :: Int -> NestedData -> Maybe Int
getNestedInt index nested = case nested >>= (_ !! index) of
  Nothing -> Nothing
  Just { foo: Tuple _ eitherInt } -> case eitherInt of
    Left int -> Just int
    Right _ -> Nothing

modifyNestedInt :: Int -> (Int -> Int) -> NestedData -> NestedData
modifyNestedInt index modifyFn nested = case _ of
  Nothing -> Nothing
  Just array -> Array.modifyAt index modifyFoo array
  where
  modifyFoo record@{ foo: Tuple str eitherInt } = case eitherInt of
    Left int -> record { foo = Tuple str (Left (modifyFn int)) }
    Right _ -> record 
```

我们可以做得更好。让我们用一个光学元件代替这两种功能。

我们的光学器件支持的不仅仅是获取或设置值，我们将在一行代码中用几个更小的光学器件来构造它。

让我们从那些小光学器件开始。下面的每个光学元件管理一层结构。除了`_foo`，它们都是在`profunctor-lenses`库中预定义的(以及 PureScript 中大多数其他常见的数据类型和类型类)。按照惯例，光学元件以下划线为前缀。

```
-- Access the second element of a tuple
_2 :: forall a b. Lens' (Tuple a b) b

-- Access the value of a record at the label "foo"
_foo :: forall a r. Lens' { foo :: a | r } a

-- Access the value within the `Just` constructor of a `Maybe`,
-- if it exists
_Just :: forall a. Prism' (Maybe a) a

-- Access the value within the `Left` constructor of an `Either`,
-- if it exists
_Left :: forall a b. Prism' (Either a b) a

-- Access the nth index of an `Array`, if it exists
ix :: forall a. Int -> Traversal' (Array a) a 
```

我们可以用上述光学方法一次处理一层结构。为了与许多 T2 结构层一起工作，我们将光学系统组合在一起。大多数人从左到右阅读这篇作文，其中`<<<`代表将一层移得更深。

例如，你可以把下面的合成光学读作“穿过`Just`层，然后是`Left`层。”你也可以从右往左读:“光学聚焦于一个`Left`中的值，它本身在一个`Just`中。”

```
_LeftWithinJust :: forall a b. Prism' (Maybe (Either a b)) a
_LeftWithinJust = _Just <<< _Left 
```

我们现在知道如何组成光学，所以让我们写一个集中在我们的`NestedData`类型里面的`Int`。

给定外部数组中的特定索引，下面的函数产生我们想要的光学元件。请注意，从左到右阅读的组合是如何与字体中的结构层对齐的。`_Just`与`Maybe`排成一行，`ix`与`Array`排成一行，以此类推。

```
-- the same type we've been working with, for reference
type NestedData =
  Maybe (Array { foo :: Tuple String (Either Int Boolean), bar :: String })

-- This function constructs an optic which accesses the `Int` value
-- within our deeply-nested type, if it exists
_NestedInt :: Int -> Traversal' NestedData Int
_NestedInt index = _Just <<< ix index <<< _foo <<< _2 <<< _Left 
```

有了这个光学器件，我们可以用简单的一行程序来代替我们之前的两个功能:

```
getNestedInt :: Int -> NestedData -> Maybe Int
getNestedInt index = preview (_NestedInt index)

modifyNestedInt :: Int -> (Int -> Int) -> NestedData -> NestedData
modifyNestedInt index = over (_NestedInt index) 
```

...就是这样！我们现在有了简单的、可维护的替代物来代替我们原来的功能。

Profunctor 镜头还帮助我们适应数据类型的变化。让我们来处理`NestedData` :
的一个重大变化

```
 type NestedData =
-  Maybe (Array { foo :: Tuple String (Either Int Boolean), bar :: String }) +  Array { foo :: Maybe (Tuple String (Either Int Boolean)), bar :: String } 
```

我们的组成是可适应的——不像我们最初的功能。当底层结构改变时，我们可以简单地移动或替换我们组成的光学元件。更好的是，由于我们使用了适用于大多数光学元件的函数(`preview`和`over`)，我们只需更新底层光学元件就能让代码工作。

一个光学作为一整个家族的真相之源，来操纵结构中的一个值。即使是对`NestedData`类型的重大改变也只需要对我们的代码做一点小小的改变。

```
 _NestedInt :: Int -> Traversal' NestedData Int
-_NestedInt index = _Just <<< ix index <<< _foo <<< _2 <<< _Left +_NestedInt index = ix index <<< _foo <<< _Just <<< _2 <<< _Left 
```

通过这种简单的重新排序，我们基于光学的`getNestedInt`和`modifyNestedInt`函数可以处理新的`NestedData`类型。与重构原始函数的努力相比！

让我们回顾一下到目前为止我们所学的内容:

*   光学是一种简洁、灵活的方法来操纵结构中的值。
*   单个光学器件通常处理单层结构。
*   将光学元件组合在一起以处理多层结构，其中最右边的光学元件代表最深的层。
*   通过调整用于处理数据结构的组合来适应数据结构的变化
*   使用具有`preview`和`over`等光学功能的光学元件执行获取、设置、修改等任务。
*   按照惯例，光学元件以下划线为前缀。

光学是一个深奥、迷人的话题，但本文主要关注它们的实际好处。在接下来的几节中，您将了解到如何使用它们来编写代码中实际问题的优雅解决方案。

## 备忘单:类型、函数和构造函数

PureScript 的旗舰光学库是`profunctor-lenses`，它包含了一系列的光学类型、函数和构造函数。你应该从这三个角度来理解光学。

[查看 profunctor 光学元件备忘单](https://thomashoneyman.com/articles/practical-profunctor-lenses-optics/#cheatsheet-types-functions-and-constructors)

#### 设置

我用 PureScript v0.13.2、Spago v0.8.5 和 2019 年 7 月 25 日的包集(psc-0.13.2-20190725)写了这篇文章。通过初始化一个新项目并安装我使用的库来编码:

```
spago init
spago install profunctor-lenses remotedata 
```

你可以用`spago repl`开始一个 repl。我推荐在项目的根目录下使用一个`.purs-repl`文件来自动导入你需要的模块。[我用的就是这个](https://gist.github.com/thomashoneyman/0d50c736503fc9a5c72f8796e70d77d1)。

我必须警告你:镜头是高度通用的，如果没有类型注释，你很可能在 repl 中遇到难以理解的错误。我推荐你:

*   用:paste 命令定义新的镜头，这样你就可以给它们和我在这篇文章中做的一样的类型(使用 Ctrl+D 结束粘贴)
*   如果您得到一个类型错误，注释光学或结果类型内联
*   或者，在编辑器的模块中编写 optics(带有类型注释)，然后将模块导入 repl
*   回顾马里克的《普通人的透镜》中的“从类型喷涌中识别光学元件”。

在真实世界的代码中，这通常是不必要的，因为有足够的上下文供编译器推断所有类型。现在，让我们转向我们的第一个光学——与`profunctor-lenses`库同名！

## 镜片

镜头用于记录和元组等产品类型。类型`Lens' s a`意味着结构`s`包含类型`a`的单个值。当你*知道*存在时，镜头被用来获取、设置或修改结构中的值`a`。

```
-- This lens focuses on the second element of a tuple and is implemented
-- in the Data.Lens.Lens.Tuple module.
_2 :: forall a b. Lens' (Tuple a b) b

-- This lens focuses on the "name" field of a record; we have to construct
-- this one ourselves.
_name :: forall a r. Lens' { name :: a | r } a
_name = prop (SProxy :: SProxy "name") 
```

我将用这些来演示常见的镜头功能`view`、`set`和`over`。然后，我将向您展示如何通过从头开始实现上述光学来构建适合您的类型的镜头。

### 使用镜头的常用功能

使用带镜头`Lens' s a`的`view`从结构`s`中获取数值`a`。镜头总是包含它们的聚焦值，所以这个操作总是会成功。

```
-- We can use our `_2` lens to retrieve the second element of a tuple
snd :: forall a b. Tuple a b -> b
snd = view _2

> view _2 (Tuple "five" 10)
10

-- We can use our `_name` lens to retrieve the value of the 'name' field
-- from a record.
getName :: forall a r. { name :: a | r } -> a
getName = view _name

> view _name { name: "Tim", age: 40 }
"Tim" 
```

使用带有镜头`Lens' s a`和类型`a`的值的`set`来覆盖结构`s`内的聚焦值`a`。

```
-- We can use our `_2` lens to replace the second element of a tuple.
setSnd :: forall a b. b -> Tuple a b -> Tuple a b
setSnd = set _2

> set _2 5 (Tuple "five" 10)
Tuple "five" 5

-- We can use our `_name` lens as an alternative to record update syntax when
-- replacing the value of a field
setName :: forall a r. a -> { name :: a | r } -> { name :: a | r }
setName = set _name

> set _name "John" { name: "Tim", age: 40 }
{ name: "John", age: 40 } 
```

使用带有镜头`Lens' s a`和修改功能`(a -> a)`的`over`在`s`内修改数值`a`。

```
-- We can use our `_2` lens to modify the second element of a tuple
modifySnd :: forall a b. (b -> b) -> Tuple a b -> Tuple a b
modifySnd = over _2

> over _2 (_ + 5) (Tuple "five" 10)
Tuple "five" 15

-- We can use our `_name` lens as an alternative to record update syntax when
-- modifying the value of a field
modifyName :: forall a r. (a -> a) -> { name :: a | r } -> { name :: a | r }
modifyName = over _name

> over _name (_ <> " Smitherson") { name: "Tim", age: 40 }
{ name: "Tim Smitherson", age: 40 } 
```

### 构造镜片

`lens'`构造函数从 getter/setter 函数中产生一个有效的镜头。这个 getter/setter 函数将结构`s`作为它的参数，并产生一个包含聚焦值`a`(getter)的元组和一个将`a`放回结构中的函数(setter)。

```
-- Note: this is a more concrete version of the type in `profunctor-lenses`
lens' :: forall s a. (s -> Tuple a (a -> s)) -> Lens' s a 
```

我们可以使用`lens'`来构建我们一直在使用的`_2`和`_name`镜头。

第一透镜聚焦在元组的第二元素上。这意味着我们的 getter/setter 函数将一个元组作为它的参数，函数的 getter 部分应该检索元组的第二个元素，setter 部分应该覆盖它。

```
_2 :: forall a b. Lens' (Tuple a b) b
_2 =
  lens' \(Tuple first second) ->
    Tuple
      second -- here we get the second element
      (\b -> Tuple first b) -- and here we set the second element 
```

第二个镜头聚焦于记录的“名称”字段。我们的 getter/setter 函数将包含这个标签的记录作为它的参数。getter 应该检索字段的值，setter 应该覆盖它，返回记录。我们可以用记录语法完成这两项任务。

```
_name :: forall a r. Lens' { name :: a | r } a
_name = lens' \record -> Tuple record.name (\new -> record { name = new }) 
```

我们现在有两个有效的镜头，但我们应该改进第二个。为自定义记录类型编写镜头是很常见的，但是`lens'`有很多样板文件。幸运的是，`profunctor-lenses`库有一个更好的记录镜头助手函数，叫做`prop`。

`prop`函数只需要您想要关注的字段的名称。字段名必须作为符号代理提供；如果您不熟悉代理，不要担心——您很快就会习惯这种模式。几乎所有的记录镜头都是这样写的。

我们来构造几个记录镜头，包括`_name`，用`prop`。

```
_name :: forall a r. Lens' { name :: a | r } a
_name = prop (SProxy :: SProxy "name")

_age :: forall a r. Lens' { age :: a | r } a
_age = prop (SProxy :: SProxy "age")

_location :: forall a r. Lens' { location :: a | r } a
_location = prop (SProxy :: SProxy "location") 
```

### 相关类:`At`镜头

类型类描述了一种特殊的镜头，用于像地图和布景这样的键控结构。这个镜头和我们以前见过的不一样；一个`At`镜头:

*   要求结构`s`是一个键控结构，比如映射或集合
*   必须使用`at`构造函数来构造，这需要结构的正确类型的键
*   允许您获取、设置和修改(像普通镜头一样)
*   还允许您插入和删除元素
*   侧重于类型为`Maybe a`的值，而不是我们到目前为止看到的简单的`a`

考虑这个使用`at`构造函数
创建的`At`镜头

```
-- This lens focuses on the specific key "Tim" in a string-keyed collection
_Tim :: forall a. At s String a => Lens' s (Maybe a)
_Tim = at "Tim"

-- We can simplify the type by specializing it
_Tim :: forall a. Lens' (Map String a) (Maybe a) 
```

我们可以将这个`At`镜头与`view` :
一样的普通功能一起使用

```
-- We can use our `_Tim` lens as an alternative to the `lookup` function for
-- maps and sets.
getTim :: forall a. Map String a -> Maybe a
getTim = view _Tim

> view _Tim (Map.singleton "Tim" 40)
Just 40

-- We can also replace the `update` function for maps and sets with `over`
updateTim :: forall a. (a -> a) -> Map String a -> Map String a
updateTim f = over _Tim (map f)

> over _Tim (map (_ + 20)) (Map.singleton "Tim" 40)
fromFoldable [ Tuple "Tim" 60 ] 
```

但是我们现在可以做的比普通镜头更多:我们还可以在结构中插入和删除值。如果使用带有`Just a`值的镜头，则该值将被更新或插入。如果与一个`Nothing`值一起使用，那么这个键将从结构中移除(如果它存在的话)。

```
-- We can use our `_Tim` lens as an alternative to `insert` function
insertTim :: forall a. a -> Map String a -> Map String a
insertTim = set _Tim <<< Just

> set _tim (Just 45) (Map.singleton "John" 20 ])
fromFoldable [ Tuple "John" 20, Tuple "Tim" 45 ]

-- In contrast, providing a `Nothing` value will delete the key (if it exists).
deleteTim :: forall a. Map String a -> Map String a
deleteTim = set _Tim Nothing

> set _Tim Nothing (Map.singleton "Tim" 40)
fromFoldable [] 
```

我们仍然使用普通的镜头功能，但是`At`镜头给了我们一些额外的功能。虽然普通的`Lens'`为我们提供了独立于类型的获取、设置和修改操作，但是`At`透镜在插入和删除方面走得更远。

## 棱镜

棱镜用于像`Maybe`和`Either`这样的求和类型。类型`Prism' s a`意味着类型`s`可能包含类型`a`的值。考虑这对简单的棱镜:

```
-- This prism focuses on the value within the `Left` data constructor. It's
-- defined in `Data.Lens.Either`.
_Left :: forall a b. Prism' (Either a b) a

-- The `RemoteData err a` data type is often used in PureScript to represent
-- requests. It's implemented like this in the `remotedata` library:
data RemoteData err a = NotAsked | Loading | Error err | Success a

-- The library exports prisms for the sum type. This prism focuses on the value
-- within the `Success` data constructor. If you are using the `.purs-repl` file
-- for this article then you already have this prism in scope.
_Success :: forall err a. Prism' (RemoteData err a) a 
```

我将用这些来探索使用棱镜的`preview`和`review`函数，然后我将演示如何用`prism'`函数为你的类型构造棱镜。

### 使用棱镜的常用功能

使用带有棱镜`Prism' s a`的`preview`从结构`s`中检索`a`类型的值，如果它存在的话。如果存在，该值将作为`Just a`返回，否则为`Nothing`。

```
-- Our `_Left` prism focuses on the left branch of an `Either`, which frequently
-- represents errors or failure cases.
getLeft :: forall a b. Either a b -> Maybe a
getLeft = preview _Left

> preview _Left (Left "five")
Just "five"

-- We can use our `_Success` prism to retrieve a successful result from a
-- request, if there is one.
getSuccess :: forall err a. RemoteData err a -> Maybe a
getSuccess = preview _Success

> preview _Success (Success { name: "Tim" })
Just { name: "Tim" } 
```

我还使用有用的`is`函数来测试聚焦的值是否存在于结构中。比如:

```
-- check if the request succeeded, specializing the result to Boolean so it
-- can be shown in the repl
> is _Success (Success { name: "Tim" }) :: Boolean
true

-- this request has not been sent yet, so it did not succeed
> is _Success NotAsked :: Boolean
false 
```

接下来，使用`review`和棱镜`Prism' s a`产生一个给定值`a`的结构`s`。这有点像使用`pure`将一个值提升到一个可应用的或一元的上下文中。它概括了数据构造函数的概念；在下面的例子中，我们也可以直接使用`Left`和`Success`数据构造函数。

```
mkLeft :: forall a b. a -> Either a b
mkLeft = review _Left

-- create a `Left` from a `String`, specializing the right branch to a concrete
-- type so it can be shown in the repl
> (review _Left "Resource not found") :: Either String Void
Left "Resource not found"

mkSuccess :: forall err a. a -> RemoteData err a
mkSuccess = review _Success

> review _Success { name: "Tim" } :: RemoteData Void { name :: String }
Success { name: "Tim" } 
```

棱镜也可以使用我们已经讨论过的镜头功能:`view`、`set`和`over`。当使用棱镜而不是镜头时，这些功能的表现略有不同:

*   `view`必须从结构中返回焦点值，但该值可能不存在于棱镜中。因此，`view`要求焦点值`a`有一个`Monoid`实例，如果不存在则返回空值。
*   如果焦点值存在，`over`和`set`将操作它，但是如果它不存在，将保持结构不变。

让我们在实践中看看这些:

```
-- view will return the focus value when it exists
> view _Success (Success { name: "Tim" })
{ name: "Tim" }

-- or `mempty` if it does not
> view _Success NotAsked :: { name :: String }
{ name: "" }

-- set and over will manipulate the focus value if it exists
> over _Left (_ <> " Smitherson") (Left "Tim") :: Either String Void
Left "Tim Smitherson"

-- and will leave the structure unchanged if it does not; contrast this to
-- the behavior of the `At` lens
> set _Left "John" (Right 10)
Right 10 
```

### 构造棱镜

您可以使用`prism'`构造函数为您的类型编写棱镜。给定一个构造函数`a -> s`和一个检索函数`s -> Maybe a`，这个函数产生一个`Prism' s a`。请记住，`a`的聚焦值并不保证存在于棱镜中！

*   构造函数通常是像`Left`或`Success`这样的数据构造函数。
*   检索函数通常是一个 case 语句，它使用模式匹配来检索聚焦值`a`

几乎所有的棱镜定义看起来都是一样的。让我们构建两个我们目前一直在使用的棱镜:

```
_Left :: forall a b. Prism' (Either a b) a
_Left = prism' Left case _ of
  Left a -> Just a
  _ -> Nothing

_Success :: forall err a. Prism' (RemoteData err a) a
_Success = prism' Success case _ of
  Success a -> Just a
  _ -> Nothing 
```

## 光学构图技巧

构图是光学如此灵活的原因。每个光学元件聚焦于单层结构中的一个值。您可以一次组合一层，深入多层结构，如果结构发生变化，您可以简单地移除、替换或重新排列组合的光学元件，以适应变化。作为一般规则，我建议您:

*   定义光学元件，一次只关注一层结构，然后将它们组合起来，以处理多层结构
*   定义预合成光学器件，以获取和设置我经常使用的数据；例如，我可能会定义`_uuid`从一个更大的`User`类型中检索用户的唯一 ID，而不是一遍又一遍地编写组合。
*   不要定义具体的 getter 或 setter 函数；像`getUUID :: User -> UUID`这样的功能可以防止与其他光学元件合成。相反，定义光学元件，然后直接使用光学元件的常用功能，如`view`、`preview`和`over`。

阅读组合光学时，确保将光学视为描述值的位置或如何访问它，而不是像获取和设置这样的操作链。例如，考虑这个组成:

```
_Just <<< _name <<< _2 
```

如果您将此理解为“获取元组的第二个元素，然后获取记录的`name`字段，然后获取`Just`构造函数中的值”，那么您是在反向读取。

相反，可以把组合看作是描述值的位置:“焦点是一个元组的第二个元素，它在一个记录的`name`字段中，在一个`Maybe`的`Just`构造函数中。”

三种常见的成分描述了大多数光学元件:

*   合成两个相同类型的光学元件，你将得到相同的类型。
*   组合一个透镜和一个棱镜，你会得到一个仿射遍历。
*   用一个遍历组合几乎任何东西，你就会得到一个遍历。

最后，让我们转向遍历。

## 遍历

类型`Traversal' s a`意味着类型`s`包含零个、一个或多个`a`值。遍历可以指以下两种情况之一:

*   遍历用于列表和地图等集合，其中光学元件具有零个、一个或多个焦点。
*   一个**仿射**遍历是一个透镜、棱镜或组合光学元件的更一般的形式，它只有零个或一个焦点。

无论哪种方式都有遍历，但是仿射遍历在使用上更类似于棱柱。然而，仿射遍历比透镜或棱镜更普遍，不能用于许多功能。

对集合进行操作的 travelsals 对于以下情况特别有用:

*   对集合中的每个焦点应用函数(如`map`)
*   将集合中的每个焦点折叠成一个值(如`fold`)

仿射遍历在合成透镜和棱镜时很有用；你会经常使用类似于`preview`和`over`的棱镜函数。

```
-- This pre-defined optic is valid for any `Traversable`, which includes
-- arrays, lists, and maps. It's defined in Data.Lens.Traversal.
traversed :: forall t a. Traversable t => Traversal' (t a) a

-- This affine traversal is the composition of lenses and prisms we've already
-- seen. Composing a lens and a prism will produce an affine traversal.
_city :: forall a b r. Traversal' (Maybe { city :: Maybe a | r }) a
_city = _Just <<< prop (SProxy :: _ "city") <<< _Just 
```

### 处理遍历的常用函数

遍历可以与我们已经看到的许多相同的镜头和棱镜函数一起使用，但也可以与用于处理值集合的库函数一起使用。您将使用的大多数特定于遍历的函数都在`Data.Lens.Fold`模块中定义。

让我们从我们已经使用过的熟悉的函数开始。

小心使用`view`，它被定义为只从一个结构返回一个值`a`:遍历支持零个、一个或多个类型为`a`的值。就像棱镜一样，如果焦点不存在，那么返回 monoidal 空值。像透镜一样，如果存在单焦点，则返回单焦点。像数组这样包含许多焦点的集合呢？在这种情况下，这些值会连接在一起，生成一个值。

根据公约一:

*   对仿射遍历使用`view`，仿射遍历最多有一个焦点。
*   将`foldOf`用于可能有许多焦点的遍历，如数组；它的行为与`view`相同，但是对集合有一个更直观的名称。

在下面的例子中，我将遵循这个惯例。

```
-- when no element exists, the empty value is returned
> foldOf traversed [] :: String
""

> view _city (Just { city: Nothing }) :: String
""

-- when one element exists it is returned
> foldOf traversed [ "Gjelina" ]
"Gjelina"

> view _city (Just { city: Just "Los Angeles" })
"Los Angeles"

-- when many elements exist, they are concatenated
> foldOf traversed [ "Tim", " Smitherson" ]
"Tim Smitherson" 
```

我们可以继续使用`set`和`over`，就像我们使用透镜和棱镜一样；然而，这一次，修改将应用于遍历所聚焦的所有内容，而不仅仅是类型为`a`的单个值。

```
-- when no focus exists, the structure is unchanged
> set traversed 10 []
[]

> over _city (_ <> ", CA") Nothing
Nothing

-- when one focus exists, `set` and `over` behave exactly as they did with
-- lenses and prisms
> set traversed 10 [ 4 ]
[ 10 ]

> over _city (_ <> ", CA") (Just { city: Just "Los Angeles" })
Just { city: Just "Los Angeles, CA" }

-- when multiple foci exist, `set` and `over` will apply to every one
> set traversed 10 (Array.range 0 4)
[ 10, 10, 10, 10, 10 ]

> over traversed (\x -> x * x) (Array.range 0 4)
[ 0, 1, 4, 9, 16 ] 
```

我们可以使用`preview`，就像使用棱镜一样，但是我们不能在遍历中使用`review`。小心`preview`:和`view`一样，它被设计成最多返回一个焦点。当一次遍历中可能存在多个焦点时，它将只返回第*个*个。我更喜欢:

*   将`preview`用于仿射遍历，它最多有一个焦点
*   对于有许多焦点的遍历，使用`firstOf`；它的行为与`preview`相同，但是对集合有一个更直观的名称

我将在我们的两次遍历中使用该约定。

```
-- when no focus exists, `preview` and `toFirstOf` return `Nothing`
> firstOf traversed [] :: Maybe Void
Nothing

> preview _city Nothing :: Maybe Void
Nothing

-- when one focus exists, it is returned in `Just`
> firstOf traversed [ 1 ]
Just 1

> preview _city (Just { city: Just "Los Angeles" })
Just "Los Angeles"

-- when many foci exist, only the first is returned
> firstOf traversed [ 1, 2, 3, 4, 5 ]
Just 1 
```

在`Data.Lens.Fold`中定义的许多库函数中，最常用的是`lastOf`，它返回许多焦点中的最后一个，以及`toListOf`，它将焦点放在一个列表中。例如:

```
> lastOf traversed [ 1, 2 ]
Just 2

> toListOf _city (Just { city: Just "Los Angeles" })
"Los Angeles" : Nil 
```

### 构建遍历

你通常不需要直接构造一个遍历，因为:

*   `traversed`光学元件适用于`Traversable`类型的任何成员
*   透镜和棱镜已经是横向的了
*   许多其他的光学元件在组合在一起时会变成 traversals。

`wander`函数帮助您为不符合`Traversable`的数据类型构建一个遍历，但是仍然有您想要关注的部分。它很少在`profunctor-lenses`图书馆之外使用，但是知道它的存在还是很好的。

### 相关类:`Index`遍历

类型类描述了一种特殊的遍历，用于关注索引集合中的单个元素。这种遍历不同于我们以前见过的遍历；一次`Index`遍历:

*   要求对结构`s`进行索引，如数组、列表或映射
*   必须使用`ix`构造函数来构造，这需要结构的正确类型的索引
*   允许您获取、设置和修改集合中的一个焦点——但是不允许您像`At`那样插入或删除元素，这使得它非常适合固定大小的集合

您可以用一个`Index`遍历来使用所有的常规遍历函数，但是记住只有一个焦点。这里有一个例子:

```
-- This index traversal focuses on the third index of a Traversable
_3 :: Index t Int a => Traversal' t a
_3 = ix 3

-- We can simplify the type by specializing to an array of strings
_3 :: Traversal' (Array String) String 
```

我在索引遍历中最常用的操作包括:

*   用`preview`获取数值
*   用`set`和`over`设置和修改数值

```
-- We can use `preview` to look up the value by its index
getIndex3 :: Array String -> Maybe String
getIndex3 = preview _3

> preview _3 [ "Preux & Proper", "Gjelina", "Sonoratown", "Republique", "Bavel" ]
Just "Republique"

-- Use `set` and `over` to modify the value (if it exists)
setIndex3 :: String -> Array String -> Array String
setIndex3 = set _3

> set _3 "Bavel" []
[]

> over _3 (_ <> "!") [ "Gjelina", "Republique", "Bavel", "Sonoratown" ]
[ "Gjelina", "Republique", "Bavel", "Sonoratown!" ] 
```

## iso

Isos 是最受限制的光学元件，它只是让你在两种类型之间来回转换，而不会丢失信息。类型`Iso' s a`意味着`s`和`a`是同构的——这两种类型表示相同的信息。当你需要打开一个新类型或者将一个数组转换成一个列表或者在不同类型之间转换时，它们特别有用。这种光学元件对于没有样板文件的向后兼容性也很有用。

```
-- The most common iso used in practice is _Newtype, which allows you to
-- unwrap or wrap a newtype, usually composed with other optics. As usual,
-- this is simplified from the type in `profunctor-lenses`. The optic is
-- defined in Data.Lens.Iso.Newtype.
_Newtype :: forall s a. Newtype s a => Iso' s a 
```

### 常用功能

iso 与透镜和棱镜有相似之处。如果你有一个 iso `Iso' s a`，那么你可以用`view`产生一个`a`，给定一个`s`，就像一个透镜，你可以用`review`产生一个`s`，给定一个`a`，就像一个棱镜。

```
-- `view` can act as a replacement for the `unwrap` function from the `Newtype`
-- type class
unwrap :: forall s a. Newtype s a => s -> a
unwrap = view _Newtype

-- We'll need a type annotation here
> view (_Newtype :: Iso' (Additive Int) Int) (Additive 10)
10

-- `review` can act as a replacement for the `wrap` function
wrap :: forall s a. Newtype s a => s -> a
wrap = review _Newtype

> review (_Newtype :: Iso' (Additive Int) Int) 10
Additive 10 
```

### 构造 iso

iso 是用`iso`函数构造的。对于给定的`Iso' s a`，它期望两个转换函数作为参数:一个将`s`转换为`a`，另一个将`a`转换为`s`。如果你已经写了任何转换函数，像`fromFoldable`或`unwrap`，那么这是微不足道的；如果没有，那么在构建 iso 之前先编写转换函数。

```
-- we can re-use the `unwrap` and `wrap` functions from `Data.Newtype` to create
-- this iso
_Newtype :: forall s a. Newtype s a => Iso' s a
_Newtype = iso unwrap wrap 
```

## 包装完毕

光学是一种强有力的工具。只需几个构建和使用光学的函数，您就可以编写更优雅的代码来管理复杂的结构。当你继续使用光学系统时，考虑回头参考 [profunctor 镜头](https://thomashoneyman.com/articles/practical-profunctor-lenses-optics)备忘单。

如果你想了解更多关于光学的知识，我推荐以下资源:

*   布莱恩·马里克的书《凡人的镜头》。
*   Artyom Kazak 编写的关于 Haskell `lens`库的系列教程 [Lens over Tea](https://artyom.me/#lens-over-tea)

如果你得到了其他资源的帮助，请在本帖关于 PureScript 讨论的列表[中推荐它们。](https://discourse.purescript.org/t/post-practical-profunctor-lenses-optics-in-purescript/904?u=thomashoneyman)