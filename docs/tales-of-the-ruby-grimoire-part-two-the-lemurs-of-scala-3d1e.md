# 红宝石魔法书的故事-第二部分-斯卡拉狐猴

> 原文：<https://dev.to/baweaver/tales-of-the-ruby-grimoire-part-two-the-lemurs-of-scala-3d1e>

这是东南红宝石 2019 演讲的文本版本，也是传奇红宝石魔法书的许多故事中的第一个，这是一本关于红宝石黑暗魔法的伟大而可怕的书。

我把它分成几个部分，这样就不会让人不知所措，因为最初的演讲非常形象。如果您想跳到其他部分，目录在这里:

**目录**

1.  第一部分——魔法书
2.  第二部分——斯卡拉狐猴
3.  [第三部分 Javascript 的狐猴](https://dev.to/baweaver/tales-of-the-ruby-grimoire-part-three-the-lemurs-of-javascript-5ac9)
4.  第四部分——哈斯克尔的狐猴
5.  [第五部分——论魔法的本质](https://dev.to/baweaver/tales-of-the-ruby-grimoire-part-five-on-the-nature-of-magic-g63)

# 《红宝石魔法书的故事》——第二部分——斯卡拉狐猴

其中 Red 从 Scala 的狐猴那里学到了闭包和占位符参数的艺术。

## 介绍斯卡拉狐猴

[![Crimson holding Scala book](img/83c49a9e1ee41c8919debcb12f6291bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zI2AtBPI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6q0dx54f3qznki0jtot.png)

“我们首先要向斯卡拉狐猴学习，”克瑞森说

[![Lemurs of Scala](img/f071cc0a5810b29095caabb6cebc8b5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5TmPpfrB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/35ckyiv9vzbtnw1hobi2.png)

“斯卡拉的狐猴戴着他们的圆锥形帽子，尖顶指向天空，披着长袍，长袍的颜色随着他们展示风格的改变而改变。Ruby 有灵活性，Scala 有更多，它们带来的经验让我们深入了解这些灵活性。”克瑞森说，“他们带来了闭包的艺术，还有一个叫做占位符参数的咒语。”

## 占位符自变量

[![Rose showing Scala](img/fdfcfd5662a7bf07b9822915db5737de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0IMEvKxt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wcczqa6fpfw7ljgfgnhs.png)

占位符参数是指下划线可以作为函数参数的简写，比如:

```
// Scala

List(1, 2, 3).map(_ + 1)
=> List(2, 3, 4) 
```

Enter fullscreen mode Exit fullscreen mode

现在在 Ruby 中，你很可能会写这样的东西:

```
# Ruby

[1, 2, 3].map { |x| x + 1 }
=> [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

...但是，如果我告诉你，在 Ruby 中做一些类似于 Scala 的事情是完全可能的，会怎么样呢？

## 关闭

[![Rose showing Closures](img/f624cc84bcbf168057da856caaee08a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IyvV8TDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrrvdw2woygjpg1askpc.png)

...但首先我们需要了解一种叫做“结束”的艺术。

```
# Ruby

adds = -> a {
  -> b { a + b }
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑一个采用参数`a`的函数，并返回一个采用参数`b`的函数。外部函数接受一个值`a`，并返回一个带参数的新函数`b`。

但是请注意！`a`当前对返回的函数可见，因此函数“记住”了`a`的值。这是一个封闭，它记得它被创造的环境，或者更确切地说，它记得它周围能看到的任何东西。

```
# Ruby

add_one = adds.call(1)
add_one.call(2)
=> 3 
```

Enter fullscreen mode Exit fullscreen mode

所以当我们用`1`调用`adds`时，我们得到一个新的函数，它记得`a`是`1`。从`adds`返回的函数用值`2`调用，它返回`3`，记住上面的`1`！

```
# Ruby

add_one = adds.call(1)
[1, 2, 3].map(&add_one)
=> [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用`&` ( `to_proc` )
提供这个函数来绘制地图

```
# Ruby

[1, 2, 3].map(&adds.call(1))
=> [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以直接内联这段代码！

这对我们有什么帮助？我们需要更多的技巧才能看出来。

## Ruby 中的运算符

[![Red using Ruby Operators](img/2022e29d3e369fb1868f075e9bf6f24a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lx3pcoq---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wevvfhik9l3o4loui26e.png)

让我们看看操作符在 Ruby 中是如何工作的。

它怎么知道这段代码在做什么？:

```
1 + 2 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，像这样的操作符只是这个的语法糖:

```
1.+(2) 
```

Enter fullscreen mode Exit fullscreen mode

操作符无非就是方法！如果我们看一下，它的实现可能是这样的:

```
class Integer
  def +(b)
    fix_plus(self, b)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

...如果你睁大眼睛，假装 C 代码是 Ruby，并且忽略一些细节。这些现在都不重要。请记住，这是一个运营商的全部。

## 制造我们自己的占位符论据

[![Red and Rose working together to make Placeholder arguments](img/00c7654e053e5e1dacb336290fdd5d06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i77nvIGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xboes782wylu7ojfcx44.png)

有了这两个技巧，我们就有足够的知识来创建我们自己的占位符参数，我们只需要多一点 Ruby 的魔力，我们就能跳一曲美妙的 Scala 之舞了！

如果我们自己制作一个类会怎么样？:

```
class PArg

end 
```

Enter fullscreen mode Exit fullscreen mode

...并在其上定义了加法运算符，但不是作为实例方法，不，是 singleton！

```
class PArg
  def self.+(a)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们为什么要做这样的事？因为它允许我们这样做:

```
PArg + 1 
```

Enter fullscreen mode Exit fullscreen mode

没有真正的规则规定运营商必须返回什么，为什么，我们可以做任何我们喜欢的事情，这正是我们要做的！

还记得闭包吗，在那里我们做了一个加法器。占位符参数也在等待一个值，不是吗？

如果我们让加法运算返回一个函数，等待应该在等式“左边”的数字，会怎么样？

```
class PArg
  def self.+(a)
    -> b { a + b }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们的加法运算符现在的工作方式有点像上面的闭包。如果我们把它放在一个等式里会发生什么？

```
[1, 2, 3].map(&PArg + 1)
=> [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，糖也延伸到优先级，这意味着`+`将在 `&` ( `to_proc`)之前*发生！*

如果我们觉得特别淘气，我们也可以做这样的事情:

```
_ = PArg

[1, 2, 3].map(&_ + 1)
=> [2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很像 Scala！...它还有一个坏习惯，就是对 REPLs 和其他一些 Ruby 代码做坏事，所以要小心做一些强调。

想象一下，我们编写其余可能的操作符，使用`method_missing`或`respond_to`在这里做更多的事情！如果我们聪明地使用堆栈构建器，我们甚至可以把它做成可以说`_ + 1 == 2`的东西，并和`select`一起使用！…但那是以后的事了，是魔法书的后一章。

## 黑暗魔法

[![Crimson and Red talking](img/c0d79bce1a7d3b1dbbcf62ba62ebf43a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R4xaWIuW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/me2c8jrgmu3q0gdsv4hg.png)

他们走时，瑞德停下来，不得不问。

“但这些不是黑魔法吗？感觉就像我们为了对语言做坏事而对语言做坏事！”瑞德大声说道

“好吧，你没有错，在某种程度上我们正在这样做！有些人称之为黑魔法，我可以简单地说他们在探索语言的可能性！想象一下这种东西的用途，有了它，你的 Ruby 会变得多么强大！”绯红反驳道。

瑞德点点头，于是他们继续前行。

# 第二部分结束

第二部分到此结束，随之而来的是一个特别黑暗的启示。这段代码既存在于 Ruby core 中，也存在于 gem 中。您可以在以下资源中了解更多信息:

*   [Ruby 2.7 编号的参数](https://dev.to/baweaver/ruby-2-7-numbered-parameters-1a79)
*   [Mf -修改器功能](https://github.com/baweaver/mf)
*   [Mf -滥用 Ruby 运算符优先级](https://medium.com/@baweaver/mf-abusing-rubys-operator-precedence-ccf3f071bad8)
*   [Sf 滥用运算符和方法缺失](https://medium.com/@baweaver/sf-abusing-operators-and-method-missing-ced78ab428cb)

不过要注意的是，除了 Ruby 2.7 官方发布的编号参数之外，这些都是实验性的，可能会在 12 月发布时将这些想法放入核心。

**目录**

1.  第一部分——魔法书
2.  第二部分——斯卡拉狐猴
3.  [第三部分 Javascript 的狐猴](https://dev.to/baweaver/tales-of-the-ruby-grimoire-part-three-the-lemurs-of-javascript-5ac9)
4.  第四部分——哈斯克尔的狐猴
5.  [第五部分——论魔法的本质](https://dev.to/baweaver/tales-of-the-ruby-grimoire-part-five-on-the-nature-of-magic-g63)