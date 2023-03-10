# 打字稿中的打字孔

> 原文：<https://dev.to/gcanti/type-holes-in-typescript-2lck>

Sandy Maguire 最近写了一篇关于类型洞的好文章。)，这篇博文是对 TypeScript 的移植。

## 什么是类型洞？

> 这个想法是实现你知道如何做的函数的一小部分，然后请求编译器帮助完成剩下的部分。这是一个反复的过程。是和编译器的讨论。每走一步，你都离正确的答案更近一点，经过足够的迭代，你的函数已经写好了自己——即使你不完全确定*如何完成*。

TypeScript 不支持类型孔，但是它们可以被模拟。

# 第一个例子

让我们看看文章
中的第一个例子

```
declare function jonk<A, B>(
  ab: (a: A) => B,
  ann: (an: (a: A) => number) => number
): (bn: (b: B) => number) => number 
```

Enter fullscreen mode Exit fullscreen mode

为了模拟一个类型洞，我将使用下面的函数声明

```
declare function _<T>(): T 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它放进`jonk`的身体

```
function jonk<A, B>(
  ab: (a: A) => B,
  ann: (an: (a: A) => number) => number
): (bn: (b: B) => number) => number {
  return _()
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你将鼠标移动到“类型洞”上`_`，你可以看到 TypeScript 为它的类型参数`T`
推断了什么

```
(bn: (b: B) => number) => number 
```

Enter fullscreen mode Exit fullscreen mode

那么类型检查器告诉我们什么呢？两件事

*   我们想要替换为`_()`的表达式必须具有类型`(bn: (b: B) => number) => number`。
*   我们有一些本地绑定(`ab`、`ann`、`jonk`及其类型)，可以用来帮助实现。

因为我们的洞有类型`(bn: (b: B) => number) => number`，我们应该在 lambda 中绑定`bn`(从现在开始我只写函数体)

```
return bn => _() // inferred type: number 
```

Enter fullscreen mode Exit fullscreen mode

新推断的类型是什么？`number`。怎样才能产生一个`number`？我们可以用`ab`、`ann`或者`bn`。因为`ann`和`bn`都返回一个`number`，所以让我们选择`ann`作为猜测

```
return bn => ann(_()) // inferred type: (a: A) => number 
```

Enter fullscreen mode Exit fullscreen mode

我们的新洞有一个函数类型，所以让我们引入一个 lambda

```
return bn => ann(a => _()) // inferred type: number 
```

Enter fullscreen mode Exit fullscreen mode

我们需要再次产生一个`number`，这次让我们选择`bn`

```
return bn => ann(a => bn(_())) // inferred type: B 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要制作一个`B`。我们有一个函数可以做到这一点，`ab: (a: A) => B`

```
return bn => ann(a => bn(ab(_()))) // inferred type: A 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们有一个类型为`A`的洞。因为我们有一个`A`(`a`参数)让我们只使用那个

```
function jonk<A, B>(
  ab: (a: A) => B,
  ann: (an: (a: A) => number) => number
): (bnn: (b: B) => number) => number {
  return bn => ann(a => bn(ab(a)))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个完整的实现，几乎是由类型检查器驱动的。

# 第二个例子

让我们来处理博文的第二个例子:`zoop`

```
declare function zoop<A, B>(abb: (a: A) => (b: B) => B, b: B, as: Array<A>): B 
```

Enter fullscreen mode Exit fullscreen mode

我们注意到`as`有类型`Array<A>`，让我们使用`foldLeft`T3 对其进行“模式匹配”

```
import { foldLeft } from 'fp-ts/lib/Array'
import { pipe } from 'fp-ts/lib/pipeable'

function zoop<A, B>(abb: (a: A) => (b: B) => B, b: B, as: Array<A>): B {
  return pipe(
    as,
    foldLeft(
      () => _(), // inferred type: B
      (head, tail) => _() // inferred type: B
    )
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为“nil”情况(即当数组为空时)生成一个`B`。既然我们有一个`B`，那就用它吧(从现在开始我只写函数体)

```
return pipe(
  as,
  foldLeft(
    () => b,
    (head, tail) => _() // inferred type: B
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们希望为另一个案例生成一个`B`，我们希望调用`abb`。既然需要两个参数，我们就给它两个洞

```
return pipe(
  as,
  foldLeft(
    () => b,
    (head, tail) =>
      abb(
        _() // inferred type: A
      )(
        _() // inferred type: B
      )
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

`head`有类型`A`所以让我们使用它

```
return pipe(
  as,
  foldLeft(
    () => b,
    (head, tail) =>
      abb(head)(
        _() // inferred type: B
      )
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须生成一个`B`，我们想使用类型为`Array<A>`的`tail`。我们唯一的选择是使用`zoop`本身

```
function zoop<A, B>(abb: (a: A) => (b: B) => B, b: B, as: Array<A>): B {
  return pipe(
    as,
    foldLeft(() => b, (head, tail) => abb(head)(zoop(abb, b, tail)))
  )
}

// p.s. `zoop` is `reduceRight` 
```

Enter fullscreen mode Exit fullscreen mode

> 这种工作的原因被称为免费定理，它大致说明了我们可以推断出关于类型签名的许多事实(假设它是正确的)。)