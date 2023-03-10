# 闭包:使用记忆化

> 原文：<https://dev.to/bbarbour/closures-using-memoization-3597>

函数式编程的核心原则之一是，如果每次给定相同的输入，函数应该返回相同的值。记忆化是采用递归或迭代函数并使其运行更快的实践。这通常是通过缓存它处理的值来实现的。

```
const multiplyCache = {}

const multiplyBy2 = num => {
  if (multiplyCache[num]) {
    return multiplyCache[num]
  }
  const total = num * 2
  console.log('Loading...') // To represent this process taking time
  multiplyCache[num] = total
  return total
}

console.log(multiplyBy2(5))
console.log(multiplyBy2(2))
console.log(multiplyBy2(5))
console.log(multiplyBy2(2))

// The first time we run the function with 5 and 2, we get Loading... 
// before we get the total. The second time with each, we fetch 
// it from the cache instead. 
```

Enter fullscreen mode Exit fullscreen mode

这个非常基本的例子就是缓存是如何工作的。我们将结果存储在对象中，以便以后可以引用它们，这样做花费的时间会少得多。

那么闭包在这里扮演什么角色呢？嗯，它们通过记忆化给了我们更多的能力，允许我们保留缓存的值并保护它们。

```
const specialNum = () => {
  let cache = {}
  return name => {
    if (cache[name]) {
      return cache[name]
    }
    console.log('Generating special number...') // To represent this process taking time
    const rand = Math.floor(Math.random() * 3 + 1)
    cache[name] = rand
    return rand
  }
}

const generateSecretNum = specialNum()

const specialNumBrian = generateSecretNum('brian')
const specialNumPiper = generateSecretNum('piper')

console.log(specialNumBrian) // Will generate, since it's the first time.
console.log(specialNumPiper) // Will generate, since it's the first time.
console.log(specialNumBrian) // Returns cached value.
console.log(specialNumPiper) // Returns cached value.

// Like above, we only get "Generating secret..." the first time. 
// The key difference here is, our cache variable is protected 
// inside of our closure and can't be accessed 
// from the outside. 
```

Enter fullscreen mode Exit fullscreen mode

我希望您能看到闭包与记忆化的结合是多么强大的组合。我们通过返回缓存的值使函数运行得更快。同时，我们也通过使用闭包来保护我们的缓存。

我想说，这是一个非常棒的工具！