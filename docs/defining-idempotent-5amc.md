# 定义幂等的

> 原文：<https://dev.to/stephencweiss/defining-idempotent-5amc>

幂等是一个在计算中经常被抛出的词，但是我总是忘记它的意思。从 Wordnik 中，定义: <sup>1</sup>

> *n.* 在*多重代数*中，一个量乘以自身给出自身。普通的统一是*等幂。*
> 
> *adj*([计算](https://www.wordnik.com/words/computing))形容一个动作，多次执行，第一次执行后对其主体没有进一步的影响。
> 
> *adj.* 用[二元运算](https://www.wordnik.com/words/binary%20operation)表示[代数结构](https://www.wordnik.com/words/algebraic%20structure)(如[群](https://www.wordnik.com/words/group)或[半群](https://www.wordnik.com/words/semigroup))的元素:当元素对自身运算时，结果等于自身。

因此，如果一个主体多次执行一个动作，而第一次没有效果，那么这个主体就被认为是等幂的。

例子有助于巩固我的理解:

> 在数学中，幂等运算是指 *f(f(x)) = f(x)* 的运算。例如，`abs()`函数是幂等的，因为`abs(abs(x)) = abs(x)`对于所有的`x`。

类似地，删除操作是幂等的，因为你不能多次删除一个项目。例如:

```
target.delete(value)
target.delete(value) 
```

第二个 delete 调用不会对目标产生任何影响，因为它试图删除的值不存在。

# 脚注

*   <sup>1</sup> [幂等| Wordnik](https://www.wordnik.com/words/idempotent)
*   <sup>2</sup> [什么是幂等运算？-堆栈溢出](https://stackoverflow.com/a/1077421/9888057)
*   [幂等|维基百科](https://en.wikipedia.org/wiki/Idempotence)