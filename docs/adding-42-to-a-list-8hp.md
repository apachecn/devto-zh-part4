# 向列表中添加 42

> 原文：<https://dev.to/bodonferenc/adding-42-to-a-list-8hp>

*这篇文章最初发表在 [LinkedIn](https://www.linkedin.com/pulse/adding-42-list-ferenc-bodon-ph-d-/) 上。*

公司 ColorCode LTD 真正相信包容性和多样性，并让他们的开发人员决定他们希望使用哪种编程语言。

Jack 是一名 C 语言程序员，他被分配了一项传输数据的临时任务。他很快编写了一个 Python 脚本，在这个脚本中，他向 list *l* 的所有元素添加了 42。

```
>>> res = []
>>> for i in range(len(l)):
      res.append(l[i] + 42) 
```

他的同桌让 Jack 注意到 Python 本身支持的 [for-each](https://docs.python.org/3/tutorial/controlflow.html#for-statements) 概念。Jack 简化了他的脚本，并将其合并到 Git repo 的主分支中。

```
>>> res = []
>>> for e in l:
      res.append(e + 42) 
```

中级 Python 程序员 Brina 发现了提交，并拜访 Jack 以展示她最喜欢的 Python 特性，[列表理解](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)。

```
>>> res = [e + 42 for e in l] 
```

随后 Python 大师徐也加入了对话。他问 Jack 变量 res 以后是否会被修改。如果没有，那么他建议使用元组和生成器表达式来代替列表。创建元组比创建列表更快，需要的内存也更少。

```
>>> res = tuple(e + 42 for e in l) 
```

业务连续性经理 Harry 就其他编程语言中列表理解的普遍性向小组成员提出了挑战。如果 Python 开发人员开始新的炒作，谁来维护代码？其他开发人员无意中听到了对话并加入了讨论。Haskell、Erlang、Perl、Ruby 和 Scala 开发者都同意 Jack 应该使用[高阶函数映射](https://en.wikipedia.org/wiki/Map_(higher-order_function))，这比列表理解更广泛。事实上，大多数其他编程语言也调用这个函数图，因此代码的目标对于其他开发人员来说将是显而易见的。哈利对这一共识感到满意。

```
>>> res = list(map(lambda e: e + 42, l)) 
```

徐再次插话，问结果应该真的是一个列表还是可以是一个生成器。“让我们避免不必要的转变”，-他说。杰克实在无法回答这个问题，他接受了徐的提议，仔细检查了代码。

Java 开发人员 János 悲伤地听着讨论。他知道 Java 8 鼓励函数式编程，但他只有在家里才有机会尝试。在办公室，他在一个拥有超过一百万行代码的系统上工作，而企业不会赞助迁移到新的 Java 版本。五年前，许多停机都是由迁移到 Java 6 引起的，尽管他们用大量的单元测试保护了迁移，并雇佣了一个完整的 QA 团队。

```
import java.util.List;
import java.util.ArrayList;

// Java 6:
List<Integer> res = new ArrayList<Integer>();

for (Integer e : l):
  res.add(e + 42);

// Java 8:
import java.util.stream.Collectors;

List<Integer> res = l.stream().map(e -> e + 42).collect(Collectors.toList()); 
```

其他开发人员感同身受，试图安慰 János。尽管他有一个 oneliner，但他只能节省 7%的打字量。

shraddha——Python 开发者，他的化身是图书管理员——提醒其他人避免重复发明轮子。不需要创建另一个 lambda 函数，只需从 operator plus 进行投影。库[功能工具](https://docs.python.org/3/library/functools.html#partial-objects)和库[操作符](https://docs.python.org/3/library/operator.html)一起支持这一点。

```
>>> from functools import partial
>>> from operator import add

>>> res = list(map(partial(add, 3), l)) 
```

Shraddha 喜欢部分功能。她承认自己受到了 Q 编程语言的影响，在这种语言中，部分函数(Q 语言中的 T2 投影)被很好地嵌入到了核心语言中。

```
q) 2 + 3           // infix notation
5
q) +[2; 3]         // prefix notation
5
q) addTwo: +[2]    // fix the first parameter
q) addTwo[3]
5 
```

面向对象编程的爱好者 Yiorgos 提醒 Shraddha，在 Python 中一切都是对象，并且有一些神奇的方法。加号运算符在后台调用私有方法`__add__`。

```
>>> 1.2 + 3.4
4.6
>>> 1.2.__add__(3.4)
4.6 
```

解析器需要额外的空间来处理整数

```
>>> 2.__add__(3)
            ^
SyntaxError: invalid syntax
>>> 2 .__add__(3)
5 
```

Yiorgos 展示了如何在不使用 lambda 函数或导入 functools 的情况下将 42 加到一个列表的所有元素中。

```
res = list(map(42 .__add__, l))     // note the extra space after 42 
```

五位数据科学家，Arthur、Silvia、Fred、Kiefer 和 Gyö rgy，分别是 Q、Pandas (Python)、R、Julia 和 Matlab 方面的专家，正从一个会议中赶来，并与小组进行接触。他们并不真正理解为什么这样的任务对任何人来说都是一个问题，因为一种体面的向量编程语言解决了这个开箱即用的问题。除了赋值之外，它们都写了相同的代码:

```
 l + 42 
```

Silvia 建议无论如何都要使用 Numpy(对于变量 l 和 res ),因为它使用起来更简单，速度更快，需要的内存也更少。她与徐就链表、Python 数组、Numpy 数组、元组、生成器等的优缺点进行了长时间的深入讨论。亚瑟听得很困惑，不明白为什么会有这么多选择。

在 Q 中只有一个列表，每个 Q 程序员都会以相同的方式执行这个任务

```
q) res: l + 42 
```

他提到有人认识一个人，他听说有人在 Q 中使用了 do-while 循环...-但这可能只是一个都市传说。

Fred 劫持了这个话题，并承认当他第一次尝试使用 Matlab 和 Julia 时，他感到困惑。当按元素添加两个向量时，使用+符号工作得很好，但是通过*符号进行向量乘法会产生错误。Gyö rgy 解释说，向量编程语言的设计者需要确定谁是主要用户。*符号既可以指[点积](https://en.wikipedia.org/wiki/Dot_product)(一般情况下为[矩阵乘法](https://en.wikipedia.org/wiki/Matrix_multiplication))也可以指逐元素乘法。数据科学家更经常使用元素操作，因为向量是从数据表的列中导出的。许多其他工程师更喜欢严格的矩阵运算。q、Numpy 和 R 面向第一类用户，Matlab 和 Julia 面向另一类用户。然而，所有五种编程语言都支持这两种操作。

```
# q/kdb+
q) v1 * v2                  # element-wise
q) v1 mmu flip enlist v2    # matrix multiplication
q) v1 wsum v2               # another form for vectors

# Python Numpy
>>> v1 * v2
>>> np.matmul(v1, v2)
>>> np.dot(v1, v2)
>>> np.inner(v1, v2)

# R
> v1 * v2
> v1 %*% v2

# Matlab
v1 .* v2
v1 * v2.'

# Julia
julia> v1 .* transpose(v2)
julia> v1 * v2 
```

多元化经理伊莎贝尔皱起了眉头。她担心这场辩论，希望没有人的感情受到伤害。当她在 LinkedIn 上读到，即使在软件工程领域，某些决策也不是非黑即白的，但辩论往往是“宗教性的”时，她感到很惊讶。开发主管詹姆斯向她保证，一切都在控制之中，这不是一场辩论，而是一场分享知识的头脑风暴会议。

詹姆斯更担心的是其他事情:如果这个简单的任务有九种不同的解决方案，那么一个复杂的问题有多少种可能的解决方案呢？！？随着复杂性的增加，可能组合的数量呈指数增长。

质量管理的 Deekshant 欢迎讨论。他希望确保人们在前进的道路上始终如一地解决这个问题。这将导致更稳定的代码和更快的事件解决。他问“谁自愿为这个临时会议创建会议记录，并创建一个指导原则以供将来参考？”。至此，团体悄然解散。

*免责声明。这是一部虚构的作品。名字、人物、企业、事件和偶发事件要么是作者想象的产物，要么是以虚构的方式使用的。与真实人物或公司的任何相似之处纯属巧合。*