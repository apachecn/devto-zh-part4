# 使用 JavaScript 生成器的惰性递归

> 原文：<https://dev.to/hemaka/lazy-recursion-using-javascript-generators-4f5l>

# 简介

生成器和迭代器支持 JavaScript 中的惰性求值。例如，在循环中使用时，执行在每个`yield`语句处“暂停”,直到请求下一次迭代。经常被忽略的是，生成器可以是递归函数。在这篇简短的文章中，我将通过一个简单的例子来演示如何为延迟求值创建递归生成器函数。

# 产量

大多数关于生成器的文档都提供了迭代示例，通常使用带有`yield`语句的`while`或`for`结构。例如，产生序列号的简单生成器可以写成这样:

```
function* count() {
  let count = 0
  while( true ) {
    yield count++
  }
} 
```

迭代没问题；但是递归表达更好的算法呢？我们如何使用生成器来创建延迟求值的递归函数？我们通过委托给另一个生成器来实现这一点。

# yield *关键字(带星号)

见见`yield*`，这是`yield`语句的懒惰表亲。`yield`语句在下一个值处暂停，直到被请求。另一方面，`yield*`语句(带有星号)只是遵从另一个迭代器对象。

求值实际上并不在`yield*`处停止，它只是一个语法，表明我们将从给定的迭代器对象转发所有的`yields`，直到它结束——之后我们继续。事实证明，这是相当强大的。

对于我们的第一个例子，假设我们想要无限地循环遍历一个 iterable。我们可以这样做:

```
function* loop( iterable ) {
  yield* iterable
  yield* loop( iterable )
} 
```

对于我们的第二个例子，我们将看一个更具体的场景——这是一个使用堆的置换算法生成数组置换的函数:

```
function* heapsPermutationsMutating( source, end = source.length  ) {
  // base case
  if( end === 1 ) { yield [ ... source ] }

  // branch
  for ( var index = 0; index < end; index++ ) {
    yield* heapsPermutationsMutating( source, end - 1 );
    swap( source, end - 1, end % 2 === 0 ? index : 0 )
  }
}

function* heapsPermutations( source ) {
  yield*  heapsPermutationsMutating( source )
}

function swap( arr, i1, i2 ) {
  return [ arr[ i1 ], arr[ i2 ] ] = [ arr[ i2 ], arr[ i1 ] ] 
} 
```

注意，我们不需要构建和保存结果数组，因为我们产生了每个排列并继续前进。`yield*`关键字遵从在每个递归分支结束时到达的基础用例中给出的`yield`。

这种模式非常适合许多递归解决方案。这种方法在空间和时间复杂度方面的优势在于，我们可以在得到想要的结果后停止执行——我们不需要生成所有的排列。

为了说明这一点，这里有一个`take`生成器函数，我们可以用它来创建特定数量的排列。

```
function* take( num, iter ) {
  let item = iter.next()
  for( let index = 0; index < num && !item.done ; index++) {
    yield item.value
    item = iter.next()
  }
} 
```

只取数组的前 5 个排列，我们可以这样做:

```
let permutate5 = [ ...take( 5, heapsPermutations([1,2,3,4,5]) ) ] 
```

# 结论

递归惰性求值进一步提高了 JavaScript 的功能。不容忽视！许多算法在递归编写时表达得更加优雅和自然。递归函数和它们的迭代对应物一样能够进行惰性求值。