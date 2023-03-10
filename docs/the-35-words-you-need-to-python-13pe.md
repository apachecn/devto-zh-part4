# Python 需要的 35 个单词

> 原文：<https://dev.to/yawpitch/the-35-words-you-need-to-python-13pe>

## 在一开始是 Python 的关键词

让我们面对现实吧，学习*写*计算机程序是*难*。这样做需要掌握许多专业技能和行话，其中大部分都是陌生的，这也意味着培养一种看待世界的思维方式，这种思维方式起初可能看起来完全像是*外星人*。这是一个漫长的旅程，但流利是从学习如何阅读计算机程序开始的，而这真的不应该那么难。

> 程序必须写给人们阅读，并且只是附带地给机器执行。
> 
> <cite>哈罗德·阿别森</cite>

Python 编程语言的一个定义特征是它被设计成人类可读的。在很大程度上，它实现了这个承诺，然而，在帮助其他人理解 Python 几年后，我开始意识到这种说法有一个重要的警告；这真的只适用于拥有*重要*和*专业*英语词汇的人。

事实是，你需要对英语语法和文字游戏的“规则”有足够的掌握，以便一眼就能识别和理解用英语写的单词*，而这些单词*实际上不是你在英语词典中能找到的*单词。像 **def** (一个*缩写*)、 **elif** (一个*组合词*)和**非本地**(一个*新词*)这样的非标准术语比比皆是，所以即使对于那些母语英语相当流利的人来说，学习 Python 的任务也很像是试图学习一门与他们的母语略有关联的外语。对于那些不太流利的人来说，学习 Python 就像在 T21 学习一门外语一样，因此要困难得多。*

为了让这个任务变得简单一点，在这篇文章和接下来的文章中，我将尝试阐明 Python 流利性的基本单位的含义——以及背后的词源学历史。在第一部分中，我们将从这些单元中最基本的开始，Python 的 35 个*关键词* <sup id="fnref1">[1](#fn1)</sup> 。

没错；开始做有意义的工作时，你实际上需要了解的 Python 词汇只有 35 个关键词。它不是最小的语言，但它离最大的语言还差得很远*，只需将其与在非编程语言中达到基本的*母语*流利所需的大约 10，000 个单词相比较。*

### 首先，有些约定俗成

Python 是一种被称为面向语句的语言；但是什么是*是一个声明呢？嗯，为了这篇文章的目的，我们只是说在 Python 中，一个语句是一行*代码，它做*一些*的事情。具体来说，它做什么取决于该语句的构件。**

 *但是这些积木是什么呢？好吧，让我们快速而粗略地定义它们，因为我们将在后面的帖子中更详细地讨论它们。我将使用大写字母，以便更容易从视觉上区分这些抽象形式和我们稍后将讨论的具体实例。

**KEYWORD**

A reserved *word* the meaning of which *cannot* be changed by the user. We will visit all 35 of these in the next section of this article.

**OPERATOR**

A reserved *symbol* that indicates an action to be performed. For example, `=` is the *assignment* OPERATOR and `+` is the *addition* OPERATOR. There are quite a few others, but we’ll save them for the next post. A small number of KEYWORDs behave like OPERATORs, and I’ll point those out below.

这些都是 Python 提供的，你不能直接改变它们的含义，这意味着它们有些不灵活。要做大多数工作，你需要更灵活的东西，这就是为什么 Python 给了你表示任何东西的能力。

**OBJECT**

An individual *thing* you can interact with. Unlike KEYWORDs and OPERATORs, you can directly manipulate these, though the degree to which you can manipulate them depends on what *type* of OBJECT they are. You can also use KEYWORDs to define entirely new *types*, which makes them a very expressive way of building new *things* of your own. So expressive, in fact, that practically speaking *everything* you interact with in Python will be an OBJECT.

不过，这可能有点抽象，很难理解。现在只需要知道对象可以分为三大类。

**VALUE**

An OBJECT that represents a single, concrete *thing*; for the purposes of this discussion what that thing actually *is* is irrelevant, but as an example, `4` is a VALUE of the [**int**](https://docs.python.org/3/library/functions.html#int "Read about the int type") (short for *integer*) type and `hello` is a VALUE of the [**str**](https://docs.python.org/3/library/functions.html#func-str "Read about the str type") (short for *string*) type. These are both examples of *primitive* types, which have a single meaningful value, but there are also *composite* types for describing things the meaning of which is defined by more than one *attribute*. A real-world example would be a rectangle, which cannot be defined without both height and width. As you’ll see below three special KEYWORDs all behave like VALUEs, though as before you cannot change their meaning.

**COLLECTION**

An OBJECT that groups together or contains other OBJECTs; there are *many* different types of COLLECTIONs in Python, but for the moment all we care about is that a COLLECTION *contains* zero or more OBJECTs. For example the statement `[2, 3, 4]` creates a COLLECTION of the type [**list**](https://docs.python.org/3/library/stdtypes.html#list "Read about the list type") that holds three VALUEs inside of it. A COLLECTION can contain *any* OBJECT, so you can *nest* a COLLECTION inside another COLLECTION.

**CALLABLE**

An OBJECT that represents some action to perform: it performs that action when you call it with some number of *arguments* then it *returns* (or gives back) an OBJECT. For instance [**sum**](https://docs.python.org/3/library/functions.html#sum "Read about the sum builtin function") is a CALLABLE and when we call it using `sum([2, 3, 4])` it gives us back the VALUE `9`. There are several different kinds of CALLABLE, and we’ll touch on them in more detail below.

不过，每次需要引用同一个对象时都键入它，效率不会很高。能够间接提及事物*往往非常有帮助*。

**NAME**

Any word that *is not* a KEYWORD, and that is used as an *alias* to *refer to* some specific OBJECT. Unlike a KEYWORD the meaning of a NAME *may* change over the course of a program, which is why these are often – if a little incorrectly – thought of as *variables*. There are several ways to create new NAMEs (and one to destroy them), as we’ll see below, but as a simple example in `number = 2` the *assignment* OPERATOR `=` creates the NAME **number** and assigns it to refer to the VALUE **2**. When later that is followed by `number += 2`, however, the *augmented assignment* OPERATOR `+=` will re-assign **number** to refer to **4**.

现在我们已经定义了所有简单的构建模块，可以开始将它们组织成复合结构了。

**EXPRESSION**

Any composite form of one or more of the above that can be *evaluated* to an OBJECT. For example, `4`, `2 + 2`, `1 * 3 + 1` and `sum([1, 1, 1, 1])` are all EXPRESSIONs that evaluate to `4`. The EXPRESSION represents the smallest discrete unit of work in Python.

**STATEMENT**

Any single line of code that is composed of at least one of the above. These can get quite complex, but to *do* anything they’ll usually need to include KEYWORDs and/or OPERATORs plus EXPRESSIONs. You’ve already met a *useful* STATEMENT in `number = 2`. If you read each STATEMENT in a program out in turn you can track the program as it does its work.

这涵盖了任何给定的*行*代码，但是目前我们还需要定义几个更高级的结构:

**BLOCK**

At least two STATEMENTs that are bound together; the first STATEMENT will end in a **:** character and indicates the start of the BLOCK. The second and all further STATEMENTs inside that BLOCK will be indented further right than the initial STATEMENT, to indicate that they *belong* to the same BLOCK. The last such indented STATEMENT represents the end of the BLOCK.

**MODULE**

A single Python .py file; it’s composed of some number of STATEMENTS. All Python programs are comprised of at least one MODULE. As you’ll see below we write all of our functionality inside MODULEs, and we use KEYWORDs and NAMEs to *import* functionality from other MODULEs.

您需要熟悉许多其他概念，但是有了这些构建模块，我们可以研究 Python 相对较少的词汇表中的所有 35 个单词，从而理解任何 Python 程序的框架。

## 对着关键词

### 一事无成的人

[pass](#pass "Read the definition of “pass”") [🗎](https://docs.python.org/3/tutorial/controlflow.html?highlight=pass#pass-statements "See the documentation for “pass”")

A placeholder; technically known as Python’s *null operation*, [`pass`](#pass "Read the entry for “pass”") does nothing whatsoever; it exists solely to allow you to write a syntactically valid BLOCK.

> 这里的一般意思来自中古英语动词，通过古法语从拉丁语 ***passus*** 借用而来，暗示*“经过【一个地方，不停地】”*。更具体地说，Python 中的意思是从桥牌等顺序玩牌游戏中借用的，在这种游戏中，如果你不想在轮到你的时候做任何事情，你就把游戏的控制权 ***交给下一个玩家，什么也不做。***

这样做的需要很简单；一旦你在 Python 中开始一个块，它*必须*包含*至少一个*缩进语句被认为是有效的语法。`pass`语句的存在是为了让你在准备好开始写有意义的语句之前写一个有效的块结构*。* 

```
STATEMENT:
    pass 
```

因为它主要在构建程序的粗略结构的早期使用，所以你很少会在工作代码中看到`pass`，但是知道它的存在是很好的。

### 三即物体

接下来的三个关键字是专门化的，因为它们的行为都像一个原始值。这意味着它们可以被赋值给一个名称，保存在一个集合中，并且可以是表达式求值的结果。它们也是唯一以大写字母开头的关键词，这使得它们很容易区分。

#### 布尔值

这两个在大多数程序中使用，如果不是全部的话，并且无论何时执行[布尔逻辑](https://en.wikipedia.org/wiki/Boolean_algebra)都是必不可少的。

[True](#true "Read the definition of “True”") [🗎](https://docs.python.org/3.7/library/stdtypes.html#boolean-values "See the documentation for “True”")

Indicator of logical *truth* and the opposite of [`False`](#false "Read the entry for “False”") ; behaves like the *integer* `1`, except that it will always be displayed as [`True`](#true "Read the entry for “True”") .

> 源自古英语形容词 ***triewe*** ，有德语词根；总的意思是“值得信任”和“与事实相符”。然而，在逻辑中，它的具体含义实际上只是“非假的”，在计算机编程中，它通常代表二进制数字 1。

[False](#false "Read the definition of “False”") [🗎](https://docs.python.org/3.7/library/stdtypes.html#boolean-values "See the documentation for “False”")

Indicator of logical *untruth* and the opposite of [`True`](#true "Read the entry for “True”") ; behaves like the *integer* `0`, except that it will always be displayed as [`False`](#false "Read the entry for “False”") .

> 源自古英语晚期，借自古法语***faus*T3】来自拉丁语**T5】falsus**；一般的意思是“假的、不正确的、错误的或欺骗性的”。在逻辑中，这个意思并不邪恶，它只是意味着“不真实的”，在计算机编程中，它通常代表二进制数字 0。**

在一些低级语言中，你可能只使用`1`和`0`来表示这些，但是在 Python 中，它们被赋予了特殊的关键字，以表明你在使用*逻辑*含义，而不是*数字*含义。

理解这一点很重要:在 Python *中，每个*对象(因此是值和集合，因此是每个表达式)都有一个*逻辑*值，因为它被认为在逻辑上等同于真或假。测试该逻辑值的状态被称为[真值测试](https://docs.python.org/3/library/stdtypes.html#truth-value-testing)，正如您将在下面看到的那样，像`and`、`or`和`if`这样的关键字都依赖真值测试来进行操作。

我将在后面的文章中更深入地讨论真值测试的细节，但是现在你只需要知道大多数东西在默认情况下都被认为是`True`，除了像`0`、`None`和`False`这样的“无值”值本身，以及像`[]`这样的“无内容”集合。

#### 空值

这最常用于表示没有任何其他值。

[None](#none "Read the definition of “None”") [🗎](https://docs.python.org/3/library/stdtypes.html#the-null-object "See the documentation for “None”")

A special name for nothing whatsoever; technically Python’s *null object*; it’s considered equivalent to [`False`](#false "Read the entry for “False”") for truth-value testing, but essentially represents no value at all. Is very commonly used in Python, and will always appear as [`None`](#none "Read the entry for “None”") .

> 一个中古英语代词来自古英语 ***南*** ，意为“不是一个”或“不是任何”。然而，编程中的含义更多地与 ***null*** 有关，这意味着“不是[任何]东西”或只是“什么都没有”。Python 选择使用 ***none*** 是因为这是一个更常见的单词。这也有助于将它与 C 编程语言中使用的特殊值 NULL 区分开来，后者具有类似的含义，但行为方式非常不同。

起初，让*成为明确代表*无*的*的概念可能看起来有点奇怪，但是当你开始构建*有用的*代码时，对`None`的需求就变得显而易见了。

### 三为决策

能够辨别某样东西是被认为是`True`还是`False`是没有多大用处的，除非你有办法基于这种知识采取不同的行动。为此*大多数*编程语言都有一些*条件运算*的概念。在 Python 中，有三个专用于条件任务的关键字。

[if](#if "Read the definition of “if”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#the-if-statement "See the documentation for “if”")

Starts a conditional BLOCK by checking the *truth-value* of the EXPRESSION that follows it; the STATEMENT(s) indented underneath the [`if`](#if "Read the entry for “if”") will be executed only if the EXPRESSION is considered [`True`](#true "Read the entry for “True”") .

> 源自古英语 ***gif*** 的中古英语连词，意为“在事件中”或“是否”，或者奇怪的是，只是“ ***if*** ”。有许多斯堪的纳维亚/日耳曼的亲戚，可能是通过古挪威语“怀疑、犹豫”而来。一般的用法是使一个单词或短语以另一个为真为条件，如“如果下雨，就打开你的伞”。计算中的感觉更正式，但本质上是一样的；*如果*【这个条件为真】，那么【做一些动作】。

[elif](#elif "Read the definition of “elif”")

Optionally *continues* a conditional by adding another BLOCK; if present it *must* follow either the initial [`if`](#if "Read the entry for “if”") or another [`elif`](#elif "Read the entry for “elif”") . Behaves exactly like an [`if`](#if "Read the entry for “if”") , except that its conditional EXPRESSION will only be evaluated when no previous [`if`](#if "Read the entry for “if”") /[`elif`](#elif "Read the entry for “elif”") STATEMENT has evaluated as [`True`](#true "Read the entry for “True”") .

> 不是一个正式的英文单词，而是一个将 ***else*** 和 ***if*** 缩成一个单独的人造单词 ***elif*** 。它一起表示“否则，如果”或“作为替代，如果”，两者都暗示由 ***elif*** 控制的动作取决于一些先前测试的结果。所以，在计算中，“如果 ***else*** 【检查一些先决条件后】 ***如果*** 【这个其他条件为真】，那么【做一些其他动作】”。

[else](#else "Read the definition of “else”")

Optionally *terminates* a conditional by adding a final BLOCK; if present it *must* follow the last [`if`](#if "Read the entry for “if”") /[`elif`](#elif "Read the entry for “elif”") in the BLOCK. If no previous [`if`](#if "Read the entry for “if”") /[`elif`](#elif "Read the entry for “elif”") STATEMENT evaluated to [`True`](#true "Read the entry for “True”") then the indented STATEMENT(s) below [`else`](#else "Read the entry for “else”") will be run.

Can also be used to terminate blocks started with other KEYWORDs; see [`for`](#for "Read the entry for “for”") , [`while`](#while "Read the entry for “while”") , and [`try`](#try "Read the entry for “try”") below.

> 源自古英语 ***elles*** 的副词，意为“[去做]而不是[某个其他动作]”或“作为替代”，或只是“否则”。在计算中，它的意思是“[检查是否有任何先决条件为真] ***否则*** 【执行一些最终动作】”。它用于在没有更好、更具体的操作时采取一些默认或后备操作。

条件是许多 Python 编程的关键，需要它来更好地解释后面的一些关键字，所以我将提供一些它们如何工作的例子。

有时你只想在满足某些条件的情况下采取*任何*行动；这是最简单的形式:

```
if EXPRESSION:
    STATEMENT 
```

然而，许多情况是二元的，所以如果条件是*而不是*满足:
，你总是想要采取一些回退措施

```
if EXPRESSION:
    STATEMENT_A
else:
    STATEMENT_B 
```

在复杂的情况下，您可能需要基于互斥条件的任意数量的替代操作，以及后备:

```
if EXPRESSION_A:
    STATEMENT_A
elif EXPRESSION_B:
    STATEMENT_B
else:
    STATEMENT_C 
```

对于中间的“非此即彼”的情况，你有时会看到另一种形式，称为*三元运算符*形式。这很有用，主要是因为与标准的`if`条件不同，它的行为就像一个表达式，并且它计算的值可以直接赋给一个名字:

```
NAME = STATEMENT_A if EXPRESSION else STATEMENT_B 
```

哪种写法更短:

```
if EXPRESSION:
    NAME = STATEMENT_A
else:
    NAME = STATEMENT_B 
```

当我们看下面类似操作符的关键字时，我们会发现这很有用。

### 五即符

接下来的五个关键字都像一个操作符，表示对对象和/或表达式执行的操作。

#### 布尔逻辑运算符

这些用于根据事物的*真值*进行有意义的比较。

[not](#not "Read the definition of “not”") [🗎](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not "See the documentation for “not”")

A *unary* OPERATOR that inverts the *truth-value* of whatever follows it, as in `not EXPRESSION`.

Can be used to invert the meaning of another KEYWORD; see [`is not`](#is "Read the entry for “is not”") and [`not in`](#is-in "Read the entry for “not in”") below.

> 源自古英语 ***nawiht*** 的中古英语副词，意为“无”或“零”。今天的一般意思否定(或翻转)了它后面的单词或短语的意思。比较“我有苹果”和“我有**T5 而不是 T7】苹果”。但在编程中，具体含义来自于[逻辑否定](https://en.wikipedia.org/wiki/Negation)，因而 ***而非*** 否定 true 为 false，反之亦然。**

这是 Python 的*布尔否定*运算符，每当你需要一个事物的真值的反义词时使用。它是*一元*，这意味着它作用于它右边的任何东西。

`not`的用法很简单:

```
not EXPRESSION 
```

如果`EXPRESSION`被认为是`True`，那么`not EXPRESSION`评估为`False`，否则评估为`True`。

如果你认为`not`像下面这样工作就更容易理解了*`if`:* 

```
False if EXPRESSION else True 
```

[and](#and "Read the definition of “and”") [🗎](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not "See the documentation for “and”")

A *binary* OPERATOR that checks the *truth-value* of two things, evaluating to the thing on the left if it tested [`False`](#false "Read the entry for “False”") , otherwise to the thing on the right.

> 古英语中的一个连词，与日耳曼词根有模糊的含义“因此”或“下一个”，用于组合两个词或短语，如“咖啡 ***和*** 茶”。然而，Python 中的意思完全来自于[逻辑合取](https://en.wikipedia.org/wiki/Logical_conjunction)，暗示要么*组合的两个*为真，要么整个组合为假。

这是 Python 的*布尔合取*运算符；当您需要测试`and`的两侧是否被视为`True`时使用。是*短路*操作；如果左边的表达式被认为是`False`，那么整个操作被认为是`False`，右边的表达式根本不会被计算。与`not`不同，它不一定计算到`True`或`False`，而是计算到左侧(如果考虑到`False`)或右侧。

因而用法:

```
EXPRESSION_A and EXPRESSION_B 
```

可以认为是这样工作的*三进制* `if` :

```
EXPRESSION_B if EXPRESSION_A else EXPRESSION_A 
```

这就是为什么有一天你会惊讶地发现`True and 1`的计算结果是`1`，而`1 and True`的计算结果是`True`。

[or](#or "Read the definition of “or”") [🗎](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not "See the documentation for “or”")

A *binary* OPERATOR that checks the *truth-value* of two things, evaluating to the thing on the left if it tested [`True`](#true "Read the entry for “True”") , otherwise to the thing on the right.

> 源自古英语连词***o***，意为“要么”，暗示两个观念结合在一起的任何一个都是可以接受的，如“咖啡 ***或*** 茶”。在 Python 中，这个意思来自于[逻辑析取](https://en.wikipedia.org/wiki/Logical_disjunction)，暗示着它所组合的事物中的*一个*为真或者整个组合为假。

这是 Python 的*布尔析取*运算符；当你需要测试`or`的*侧的*是否被认为是`True`时使用。是*短路*操作；如果左边的表达式被认为是`True`，那么整个操作被认为是`True`，右边的表达式根本不会被计算。此外，与`not`不同，它不一定计算到`True`或`False`，而是计算到左侧(如果考虑到`True`)或右侧。

因而用法:

```
EXPRESSION_A or EXPRESSION_B 
```

可以认为是这样工作的*三进制* `if` :

```
EXPRESSION_A if EXPRESSION_A else EXPRESSION_B 
```

当你发现`True or 1`的计算结果是`True`而`1 or True`的计算结果是`1`时，这种微妙之处就能抓住你。

#### 身份检查操作员

[is](#is "Read the definition of “is”") [🗎](https://docs.python.org/3/reference/expressions.html#is "See the documentation for “is”")

A *binary* OPERATOR that tests if the OBJECT on the left has the same [identity](https://en.wikipedia.org/wiki/Identity_(object-oriented_programming) "Read about identity in programming") as the OBJECT on the right and then evaluates to either [`True`](#true "Read the entry for “True”") or [`False`](#false "Read the entry for “False”") .

Can be inverted by [`not`](#not "Read the entry for “not”") to become the [`is not`](#is "Read the entry for “is not”") operator.

> 源自日耳曼语词干 ****es-*** 的古英语动词； ***是*** 的第三人称单数现在表示形式，所以一般表示“成为【一个事物】”。在 Python 中，它的意思是特定于*身份*的，暗示着更像“成为【某种独特的东西】”。

用法很简单:

```
EXPRESSION_A is EXPRESSION_B 
```

和`not`的用法是:

```
EXPRESSION_A is not EXPRESSION_B 
```

身份的概念有点抽象，但是可以这样想:汤姆和鲍勃是双胞胎，他们有相同的身高、年龄和体重，他们的生日相同，但是他们*没有相同的身份。汤姆是汤姆，鲍勃是鲍勃，但汤姆不是鲍勃。*

在 Python 的大多数实现中，对象的身份可以被认为是它在内存中唯一的地址，因为*你使用的每一个*对象都有它自己唯一的地址，`is`通常只对判断一个名称是否指向一个特定的对象有用:

```
NAME is OBJECT
NAME is not OBJECT 
```

或者测试两个不同的名称是否指向内存中的同一个对象。

```
NAME is OTHER_NAME
NAME is not OTHER_NAME 
```

有*有*但是也有一些特例:比如`True`、`False`、`None`都是内存中的[单态](https://en.wikipedia.org/wiki/Singleton_pattern)，也就是说在*任何* Python 程序中*永远*只有一个`True`副本。在很大程度上，这只是一个节省空间的细节，你不需要担心，但它解释了为什么在 Python 中，当检查某个东西是否被认为是*等价于*到`True`时，我们使用`VALUE == True`和*而不是*T5】。测试*身份*是*不是*与测试*值*相同。

由于这个原因，`is`的使用是有限的和特定的，这就是为什么你很少看到`is`和`is not`在实践中被使用。

#### 成员资格测试操作员

[in](#in "Read the definition of “in”") [🗎](https://docs.python.org/3/reference/expressions.html#membership-test-operations "See the documentation for “in”")

A *binary* OPERATOR that tests if the OBJECT on the left is a member of the COLLECTION on the right and then evaluates to either [`True`](#true "Read the entry for “True”") or [`False`](#false "Read the entry for “False”") . Also known as Python’s *inclusion operator*.

Can be inverted by [`not`](#not "Read the entry for “not”") to become the [`not in`](#in "Read the entry for “not in”") *exclusion operator*.

Also used with [`for`](#for "Read the entry for “for”") , see below.

> 古英语单词 ***在*** 中的一个中古英语合并词，意为“在之中”，而 ***inne*** ，意为“在内”或“内部”。这个合并词有很多用法和意思，但这里的一般意义来自介词形式，它暗示着某个事物包含在某个更大的事物中，如“一本书中的一页**或一本书中的一页或一个图书馆中的一本书。在 Python 中，当检查一个项目是否包含在一组项目中时，它专门用于*成员测试*。**

`in`的通常用法是测试一个对象是否是一个特定容器的成员:

```
OBJECT in CONTAINER 
```

或者测试对象是否是容器
的成员

```
OBJECT not in CONTAINER 
```

很容易认为可以将`is`和`in`一起使用，但这是无效的语法。记住这一点很有帮助，因为`is`、`is not`、`in`和`not in`都是二元操作符，它们*必须*在任一侧有一个对象或表达式，*不是*另一个关键字。

### 四个用于循环

上面的关键词给了你做简单决策和采取基本行动所需的一切，但是当你需要重复做某事时，它们就没用了。这就是*循环*的用武之地。在 Python 中，以下四个关键字给了你做这件事所需要的一切。

#### 开始一个循环

Python 中有两种开始循环的方式，它们在概念上非常相似，但是您选择哪种方式在很大程度上取决于您到底需要对该循环做什么。

##### 循环直到达到某种条件

[while](#while "Read the definition of “while”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#the-while-statement "See the documentation for “while”")

Starts a loop BLOCK by testing a the truth-value of an EXPRESSION; will iterate continuously until EXPRESSION evaluates to False.

> 源自古英语单词 ***hwile*** 表示“一段持续时间”，但在这里我们使用的是连词形式，暗示“[在]时间内[某事为真]”或“只要[某事为真]”。在编程语言中，该关键字总是与要进行*测试*的事情和要进行*操作*的事情相关联，因此其含义变成了“ ***，而*** 【测试为真】，【做某事】”。

`while`循环块的形式总是相同的:

```
while EXPRESSION:
    STATEMENT 
```

如果表达式的计算结果为`True`，那么语句将被执行。当到达缩进块的末尾时，控制立即返回到顶部，然后再次测试表达式，依此类推。只要表达式的值为`True`,整个缩进的块就会一次又一次地运行。

`while`循环也可以*可选地*由`else`块
终止

```
while EXPRESSION:
    [...]
else:
    STATEMENT 
```

在这种情况下，如果`while`循环一直运行到完成(其测试评估为`False` ) *而*没有遇到`break`，则`else`块内的语句将被执行。当一个`while`循环已经自然退出，需要进行一些清理动作时，这是很有用的。

`while`是最基本的循环形式，但是如果不小心使用的话就有点危险了。这是因为任何一个*总是*求值为`True`的表达式都会永远运行*。由于这个原因，设计一个表达式，使得它最终*得到*的结果`False`是很重要的。*

 *##### 循环遍历集合的成员

[for](#for "Read the definition of “for”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#the-for-statement "See the documentation for “for”")

Starts a loop BLOCK that will iterate *once* over a COLLECTION, visiting every item in it.

Can also be marked with [`async`](#async "Read the entry for “async”") , to start an [`async for`](#async "Read the entry for “async for”") loop, see below.

> 源自德语 ***für*** 的古英语单词，有多种含义；一般的意思取自介词“代表(某事物)执行动作”的意思。不过，在计算中，这个意思实际上是从单词 ***与每个******(意思是“一组中的每个【项目】)或*(意思是“一组中的所有”】的缩写而来的，以形成每个**** *的 ***或每个*** 的 ***，这两者都表示“执行一个”在源于 ALGOL [***的编程语言中，***](https://en.wikipedia.org/wiki/For_loop) 传统上是这种循环最常见的名称，少数语言中使用 ***do*** 。Python 的名字来源于 ALGOL 的传统用法 via C，然而将 Python 的版本描述为一个[***foreach***](https://en.wikipedia.org/wiki/Foreach_loop)循环更准确，因为没有显式计数器，被循环的对象必须是 [*iterable*](https://en.wikipedia.org/wiki/Iteration) 。****

 *`for`的使用始终包括给集合中的每个对象`in`分配一个用户指定的名称。

```
for NAME in COLLECTION:
    STATEMENT 
```

这样`for`集合中的每一项的名字都会引用那个项*在*块的范围内；这允许语句使用名称来作用于那个东西。

如果你认为`for`是`while`循环的一种特殊形式:
，这就更容易理解了

```
while [items remain in COLLECTION to visit]:
    NAME = [increment to the next item]
    STATEMENT 
```

但是显然,`for`循环要简单得多，因为您不需要担心实现必要的机制来跟踪循环的开始和停止条件。您会发现，无论何时访问集合的单个内容，您都可以并且通常应该使用一个`for`循环，而不是冒险使用一个失控的`while`循环。

`for`循环也可以*可选地*由`else`块
终止

```
for NAME in COLLECTION:
    [...]
else:
    STATEMENT 
```

在这种情况下，如果`for`循环已经运行到完成*而*没有遇到`break`，那么`else`块内的语句将被执行。当一个`for`循环已经自然退出，需要进行一些清理动作时，这是很有用的。

#### 控制一个循环

你不总是想简单地运行循环直到完成；可能有很好的理由提前退出，或者跳过一轮循环。

[break](#break "Read the definition of “break”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-break-statement "See the documentation for “break”")

Used to immediately interrupt the current loop iteration, ending the BLOCK it is found within. For this reason must *only* be used within a loop BLOCK.

> 源自古英语单词 ***brecan*** ，它有几种形式和含义。名词形式一般表示“损坏、破坏或使不可用”，如“to ***打断*** 一条腿”。然而，在这里，我们使用了“中断[一个连续序列]”的替代含义，如在“以 ***断开*** 一个电路”中。在编程中，它特别意味着从循环内部中断循环。

`break`语句总是自成一行，它*必须在`for`或`while`循环中使用*。最常见的用法是在达到某个特定条件时立即停止循环:

```
while True:
    if EXPRESSION:
        break
    STATEMENT

for NAME in COLLECTION:
    if EXPRESSION:
        break
    STATEMENT 
```

如果在某些情况下需要在循环正常完成之前停止循环，这将非常有用。

[continue](#continue "Read the definition of “continue”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-continue-statement "See the documentation for “continue”")

Immediately skips the rest of the current loop BLOCK, allowing the loop to continue on to the next iteration. For this reason must *only* be used within a loop BLOCK.

> 一个中古英语动词，经由古法语 ***continuer*** 从拉丁语 ***continuare*** 借用而来。这里的一般意思是“向前或向前”、“继续”或“进行”。在编程中，它意味着使一个循环开始执行下一次迭代，跳过跟随它的任何指令。

`continue`语句总是自成一行，并且必须在`for`或`while`循环中使用。最常见的用法是当达到某个特定条件时，立即跳到循环的下一次迭代:

```
while True:
    if EXPRESSION:
        continue
    STATEMENT

for NAME in COLLECTION:
    if EXPRESSION:
        continue
    STATEMENT 
```

如果在某些情况下需要跳过当前循环，这一点特别有用；例如，如果您只想每隔一次迭代执行一次。

### 三个用于导入其他东西

所有这些，加上我们将在后面的文章中讨论的*内置*函数，足以让您开始使用 Python 作为一种*脚本语言*，在这里您将别人用您自己的代码编写的东西粘合在一起，以完成您想要完成的任务。但是你需要能够访问那些“别人写的东西”来这样做。这就是 Python 的*导入机制*的作用。

[import](#import "Read the definition of “import”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-import-statement "See the documentation for “import”")

Used to bring the functionality of an external MODULE into your own code.

> 一个来自中古英语的动词*，经由古法语从拉丁语 ***导入*** 。大致意思是“从国外把[货物]带入/携带到这个国家”。在计算中，它的意思是把用同一种语言编写的另一个程序导出的某些功能带入当前程序。*

 *最常见的用法非常简单，关键字后面跟着你要访问的模块:

```
import MODULE 
```

也可以导入多个模块，用逗号分隔:

```
import MODULE_A, MODULE_B 
```

出于组织的目的，你也可以用圆括号将它们括起来:

```
import (MODULE_A, MODULE_B) 
```

导入的模块中的任何名称都可以在你自己的程序中使用 MODULE.NAME 形式的*点访问*模式来访问。因此，例如，如果你想得到一个圆的周长:

```
import math

radius = 3
circumference = math.tau * radius 
```

通常你会在一个模块的顶部找到所有的`import`用法，这使得确定这样的功能来自哪里变得非常容易。

[from](#from "Read the definition of “from”")

Modifies [`import`](#import "Read the entry for “import”") to allow you to import specific NAMEs from within an external MODULE.

Can also be used with [`raise`](#raise "Read the entry for “raise”") and [`yield`](#yield "Read the entry for “yield”") , see below.

> 源自古英语 ***fram*** 的一个中古英语单词，这里我们用介词形式，有“离开或远离(某物)的运动”的一般意义；在计算中我们使用一个更具体的含义“取自 ***来源于*** ”。

用于修改`import`从一个模块导入一个特定的名字，而不是*整个*模块:

```
from MODULE import NAME 
```

如果您希望导入多个名称，可以用逗号分隔:

```
from MODULE import NAME_A, NAME_B 
```

出于组织的目的，你也可以用圆括号将它们括起来:

```
from MODULE import (NAME_A, NAME_B) 
```

在所有情况下，你都可以直接使用导入的名称，例如，如果你想要一个圆的面积:

```
from math import tau

radius = 3
area = tau * radius ** 2 / 2 
```

使用`from`的主要原因是不需要在代码中散布对一个模块的大量引用，但是最好是在模块有很多名字要导出，而你只想使用一两个名字的时候使用。

[as](#as "Read the definition of “as”")

Modifies [`import`](#import "Read the entry for “import”") to create an alternative NAME (or alias) for an imported NAME.

Can also be used with [`except`](#except "Read the entry for “except”") and [`with`](#with "Read the entry for “with”") , see below.

> 源自古英语 ***eallswā*** 意为“就这样”或简单地说“都这样”，这使其成为**T5 也** 的简化形式。这里的用法来自副词形式，意思是“[以某种其他事物的方式或角色]行动”。在 Python 中它非常明确的意思是“[从这里开始引用这个东西] ***作为*** 【这个代替】”。

因为`from`的存在，所以有两种用法:

```
import MODULE as MODULE_ALIAS
from MODULE import NAME as NAME_ALIAS 
```

`as`的目的是允许你`import`某个模块或名字，但是用其他名字来引用它。如果原始名称特别长，会与代码中已经使用的名称冲突，或者可能只是在上下文中使用一些额外的信息，这将非常有用。

```
from math import pi as half_as_good_as_tau 
```

### 五为例外情况

现在你已经有了基本的东西，你正在进入更复杂的领域。如果您发现自己到达了代码中的一个点，处于明显的错误状态，并且不想继续下去，会发生什么呢？这就是[异常处理](https://en.wikipedia.org/wiki/Exception_handling)发挥作用的地方。我们将在后面的文章中更详细地讨论标准异常，但现在我们只能说异常是一种特殊类型的值，它表示程序中出现的特定问题。那个问题*可能*是一个*错误*，在这种情况下，你可能想退出程序，或者它可能是一个信号，表明发生了一些*不寻常的*但在预料之中的事情。无论哪种方式，您都需要能够从您自己的代码中发出这些信号，并捕捉和响应由其他代码发出的信号。

#### 表示有问题

[raise](#raise "Read the definition of “raise”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-raise-statement "See the documentation for “raise”")

Used to raise a specified EXCEPTION, which will cause the program to stop immediately and exit if not handled by an [`except`](#except "Read the entry for “except”") BLOCK.

If used without an argument inside an [`except`](#except "Read the entry for “except”") or [`finally`](#finally "Read the entry for “finally”") BLOCK, re-raises the EXCEPTION being handled by the BLOCK.

> 一个中古英语单词，有多种含义，但在这种情况下，它来自动词形式，意思是“直立起来，建造或构造”或“使更高”。计算中的含义更具体地来自一个更新的意义，即“提及[一个问题、争论或争论]以供讨论”，如“对 ***提高*** 对[一个问题]的关注”。在其他几种编程语言中 ***throw*** 的用法有类似的意思。

用法是*通常*会是:

```
raise EXCEPTION 
```

你可能很少会看到*链式*形式:

```
raise EXCEPTION from OTHER_EXCEPTION 
```

这用于指示被引发的异常是由(或*来自*)其他异常引起的*。这并不经常使用，但是当您试图处理由其他代码引发的异常但不知何故却无法处理时，这有时会很有用。*

最后一个*在*里面一个`except`块你可以简单的:

```
raise 
```

这将重新引发该块中当前正在处理的任何异常。

#### 表示不满足特定条件

[assert](#assert "Read the definition of “assert”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement "See the documentation for “assert”")

Used to test if some EXPRESSION is considered True, and if not raise an AssertionError.

> 源自拉丁语 ***assertus*** ，具有“宣布、保护或主张”的一般意义。在程序设计中，它特别意味着指定在代码中的特定点必须满足某个条件，如果不满足就出错。

`assert`的用法通常是:

```
assert EXPRESSION 
```

如果表达式的计算结果不为`True`，它将简单地`raise`一个 AssertionError。

另一种形式允许您指定消息:

```
assert EXPRESSION, "something is wrong!" 
```

并且该消息将被合并到 AssertionError 中。

`assert`语句的存在是为了让你测试那些*必须*为`True`才能让你的程序继续工作的东西(我们称之为你的*不变量*)。这在开发和调试时非常有帮助，但在生产代码中根本不应该依赖*，因为运行程序的人可以通过将`-O`命令行选项传递给 Python 来选择禁用所有的`assert`语句。基本上你可以想象`assert`是这样工作的:* 

```
if [the -O command line flag was not passed]:
    if not EXPRESSION:
        raise AssertionError 
```

…除了如果`-O`标志被传递，那么`assert`语句将简单地被`pass`代替，并且不会发生任何事情。

#### 捕捉信号并对其作出反应

工具箱中有了`raise`和`assert`，您知道如何发出异常信号，但是您如何捕捉异常并对其做出反应(或忽略)？这就是 [Python 的异常处理](https://docs.python.org/3/tutorial/errors.html#handling-exceptions)机制发挥作用的地方。

[try](#try "Read the definition of “try”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#the-try-statement "See the documentation for “try”")

Starts an exception handler BLOCK; *must* be followed either an [`except`](#except "Read the entry for “except”") BLOCK, an [`else`](#else "Read the entry for “else”") block or a [`finally`](#finally "Read the entry for “finally”") BLOCK in order to be valid syntax.

> 一个从古法语单词 ***借用来的动词，特里尔*** ，意为“测试”、“实验”或“尝试做”。Python 中的意思本质上是一样的，“[开始一个]测试[对一些*可能*出错的东西]”。

[except](#except "Read the definition of “except”")

Optionally *continues* an exception handler BLOCK by catching EXCEPTIONs; can (and *should*) be limited to specific types of EXCEPTION. More than one of these can follow a [`try`](#try "Read the entry for “try”") and each will be checked in turn until either the EXCEPTION is handled or no more [`except`](#except "Read the entry for “except”") statements remain.

> 从中古法语 ***excepter*** 和拉丁语***excepus***中借用的一个动词；原意是“接受”，但更一般的用它来“排除某物”或“反对某物”，如“除苹果外的每一种水果”。Python 中的意思有点模糊，但可以认为是“捕捉、捕获或捕获[异常]”。事实上在许多其他语言中 ***赶*** 是用于同样的目的；Python 的 ***除了*** 是那个规则的例外。

[finally](#finally "Read the definition of “finally”")

Optionally *cleans up* an exception handler BLOCK to provide a means of *always* performing some action whether or not the EXCEPTION was handled. *Must* appear after any [`except`](#except "Read the entry for “except”") BLOCKS that are present, as well as the optional [`else`](#else "Read the entry for “else”") BLOCK if that is also present. If no [`except`](#except "Read the entry for “except”") BLOCK is present then [`finally`](#finally "Read the entry for “finally”") *must* terminate the exception handler.

> 源自中古英语 ***fynaly*** 意为“在最后或结论处”或只是“最后”。它意味着在一个序列中要做的最后一件事，这也是这里的意思。

异常处理是您遇到的第一种情况，在这种情况下，一个块*必须跟随着另一个块*，规则比您见过的任何情况都要复杂。例如，有*两种*不同的*最小语法有效的*形式的异常处理程序:

```
try:
    BLOCK
finally:
    STATEMENT 
```

这种形式实际上并不处理 BLOCK 中引发的任何异常，它只是确保语句在任何情况下都可以运行。任何未处理的异常都将继续“引发”,直到它被处理或使程序崩溃。这对于无论是否有错误都希望执行相同操作(如关闭文件或数据库连接)的情况非常有用。

第二种形式*处理异常:* 

```
try:
    BLOCK
except:
    STATEMENT 
```

**注意**:因为这个表单会捕捉*任何*异常，包括几个被 Python 用来执行关键操作的异常，所以实际上，**永远不要**使用上面的表单。而是使用*最小安全*形式:

```
try:
    BLOCK
except EXCEPTION:
    STATEMENT 
```

这确保了异常处理程序*仅*处理指定的异常*类型*。Python 中内置异常的[层次结构很复杂，涉及到我们无法在本文中讨论的概念，但是现在只需要知道`except Exception`是您应该在生产代码中使用的最不*具体的`except`语句。*](https://docs.python.org/3/library/exceptions.html#exception-hierarchy)

然而，这种最低限度的安全形式并不是特别有用，因为语句实际上并不知道它正在处理什么异常。大多数时候你会看到这样的东西:

```
except EXCEPTION as NAME:
    STATEMENT 
```

它使用`as`来提供一个别名，这个别名可以用来检查实际引发的异常。这非常有助于准确地看到哪里出了问题，这将帮助您决定是否可以忽略该问题或需要以某种方式做出响应。

您还可以指定多个不同类型的异常来捕获，如果您想要以*相同的*方式:
来响应这些表单中的任何一个，这将会很有帮助

```
except (EXCEPTION_A, EXCEPTION_B, EXCEPTION_C) as NAME:
    STATEMENT 
```

如果你想以*不同的*方式响应它们，你可以堆叠`except`块:

```
except EXCEPTION_A as NAME:
    STATEMENT_A
except EXCEPTION_B as NAME:
    STATEMENT_B 
```

您也可以选择将`else`与异常处理程序一起使用，只要至少使用了一个`except`语句，并且只要`else`位于 any `finally` :
之前

```
try:
    BLOCK_A
except EXCEPTION as NAME:
    BLOCK_B
else:
    STATEMENT 
```

然而这种情况相对较少，因为当在`try`块*中没有引发异常并且在*块中没有遇到`break`、`continue`或`return`语句时，缩进在`else`下的语句将只执行*。*

如您所见，与 Python 的其他部分相比，异常处理有一些相当密集的语法。一个完整的异常处理程序在一个程序中完成一些相当复杂的事情，比如与数据库交互，可能会涉及到所有这些部分。

```
try:
    [connect to database]
    [query the database]
except ConnectionError as error:
    [log the error]
else:
    [log success]
finally:
    [close the connection] 
```

但是，值得庆幸的是，它通常并不复杂，并且您通常只需要在发生真正异常的情况下处理异常。

### 四位为书写功能

现在，您已经获得了编写任意复杂的程序所需的所有结构，具备了决策、循环和处理错误的能力，您最大的问题是将这些结构组织成可重用的单元。例如，您不希望每次连接和查询数据库时都键入复杂的异常处理程序，因为这将很快导致不可管理的重复。

编程的关键口头禅之一是[不要重复自己](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)(又名**干**)；样板文件越少越好，所以你希望能够创建[子程序](https://en.wikipedia.org/wiki/Subroutine)，这是*代码重用*的最基本形式。在 Python 中，最常见的子程序形式是*函数*，它有两种主要形式。

#### 匿名(未命名)函数

许多教授(和学习)Python 的人倾向于跳过匿名函数，或者将它们视为一种高级特性，但这实际上只是因为它们命名不当。“匿名”部分仅仅意味着函数在创建时没有给定一个特定的名称。这听起来不像是最容易重用的东西——你是对的——但是在我们开始讨论*命名的*函数之前理解它们是有用的，因为它们从根本上来说更简单，也更受约束。

不幸的是，已经背负着一个糟糕和混乱名字的匿名函数应该*在 Python 中得到一个更糟糕的名字。*

[lambda](#lambda "Read the definition of “lambda”") [🗎](https://docs.python.org/3/reference/expressions.html#lambda "See the documentation for “lambda”")

Used to define a CALLABLE [anonymous function](https://en.wikipedia.org/wiki/Anonymous_function "Read about anonymous functions") and its *signature*.

> 古希腊字母表的第 11 个字母，***λ****；它在英语中没有一般意义。在 Python 中使用它是因为匿名函数是[阿隆佐·邱奇的λ演算](https://en.wikipedia.org/wiki/Lambda_calculus)的基本单元，它提供了现代计算的许多数学基础。作为一种荣誉，那很好；实际上，如果λ有一个更有趣的名字，它会被更多地使用。*

 *尽管名为`lambda`，但它实际上是 Python 中概念上最简单的函数形式，因为你可以把它看作是创建延迟求值表达式的一种方式，该表达式用特定的形式来表述:

```
lambda : EXPRESSION 
```

其计算结果为一个可调用对象，该可调用对象只有在自身被调用时才会计算冒号后的表达式。要调用它，您可以使用所有 Python 调用通用的*调用语法*，但是由于`lambda`本身是一个表达式，因此您需要用括号将它括起来。

```
(lambda : EXPRESSION)() 
```

然后*瞧*，你刚刚写的所有东西都会立刻被你内心的表达所取代。

因此，这三者完全相同:

```
x = (lambda : 2 + 2)()
x = 2 + 2
x = 4 
```

但是仅仅延迟一个表达式并不是最有用的工具。所以`lambda`引入了*函数签名*的概念:你可以在冒号的左边添加一个名字，名字引用的东西将能够在内部表达式求值时使用。这个名字被称为函数的*参数*。

这里有一个只有一个参数的`lambda`:

```
lambda NAME: EXPRESSION 
```

这里有一个有两个参数的:

```
lambda NAME_A, NAME_B: EXPRESSION 
```

当我们想要使用`lambda`时，我们只需要调用它，为签名中的每个参数传递一个具体的值:

```
(lambda NAME_A, NAME_B: EXPRESSION)(VALUE_A, VALUE_B) 
```

如果我们想计算一个矩形的面积，你可以想象它会很有用:

```
lambda width, height: width * height 
```

但是，同样，如果我们不把它命名为:
，我们将会写很多

```
area = lambda width, height: width * height
square = area(2, 2)
rectangle = area(3, 5) 
```

太好了！现在的`square`是`4`，`rectangle`是`15`；我们已经有了代码重用的基础！

但是当表达式开始变长时，情况会变得相当糟糕。除此之外，还有一些非常明显的限制，因为我们实际上*不能*在`lambda`内执行多种语句，更不用说任意的块了。他们有他们的位置，但是也许有更好更灵活的方法？

既然我们已经将所谓的*匿名*(意思是“没有名字”)函数赋给了一个名字，那么让我们看看我们应该*通常*如何用 Python 编写函数。

#### 命名函数

名为的*函数基于`lambda`的思想，但是将它们带到了一个*更加灵活的地方，允许你非常容易地重用大块的代码。它们也很难掌握，因为它们的计算不完全像一个表达式。事实上，默认情况下，它们将总是计算为`None`,除非你明确告诉它们不这样做。**

[def](#def "Read the definition of “def”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#function-definitions "See the documentation for “def”")

Used to define a *named* function and its *signature*, the indented BLOCK that follows can then be re-used by calling that NAME using the `function()` syntax.

If used inside a [`class`](#class "Read the entry for “class”") defines a named *method* instead, which is called using the `class.method()` syntax.

Can also be marked with [`async`](#async "Read the entry for “async”") , to start an [`async def`](#async "Read the entry for “async def”") , see below.

> ***一词的缩写定义了*** ，它来自中古英语 ***deffinen*** 的古法语和拉丁语词根。它是一个动词，意思是“指定或确定(一个单词或短语的意思)”。在 Python 中，它专门用于创建命名的子例程。在其他语言中 ***定义*** ， ***fn*** ， ***趣味*** ， ***func*** ， ***功能*** ， ***let*** 常被代替使用。

因为`def`可以用来创建任意复杂的可调用函数，所以需要一些解释。本质上，它是用来创建一个引用可调用对象的新名称。事实上，从现在开始，让我们用函数来准确地表示:

```
def FUNCTION():
    STATEMENT 
```

这定义了一个可以使用`FUNCTION()`调用的函数，该函数将执行缩进块中的每一条语句。如您所见，它在概念上等同于您之前看到的“named”`lambda`表单。比较这两种形式:

```
def FUNCTION(NAME_A, NAME_B): STATEMENT
FUNCTION = lambda NAME_A, NAME_B : STATEMENT 
```

你会看到它们与*非常*相似，除了`def`版本可以运行任意数量的语句 <sup id="fnref2">[2](#fn2)</sup> 。

这些语句将在函数的*局部作用域*内执行，这意味着函数内的*任何*名不能被函数外的*任何代码直接*看到或访问。在这个例子中没有这样的名字，但是一旦我们改变函数
的*签名*就会有了**

```
def FUNCTION(NAME):
    STATEMENT 
```

就像`lambda`一样，我们可以有多个参数:

```
def FUNCTION(NAME_A, NAME_B):
    STATEMENT 
```

让我们用`lambda` :
来构建之前做的`area`函数

```
def area(width, height):
    width * height

square = area(2, 2)
rectangle = area(3, 5) 
```

除了，等一下…我刚才不是说过`def`函数在被调用时计算为`None`吗？是的，现在`square`和`rectangle`都被分配给了`None`。我们已经计算了两个区域，但是一旦`area`的*局部作用域*关闭，我们就再次丢弃它们。现在，不管怎样，我们该如何从`area`的中得到*的面积呢？*

##### 停止功能并返回一个值

[return](#return "Read the definition of “return”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-return-statement "See the documentation for “return”")

Used to immediately give up control and end execution of the function at the point at which it is encountered. If followed by an EXPRESSION, that is evaluated first and the resulting OBJECT is given back to the caller of the function. If no EXPRESSION is present [`None`](#none "Read the entry for “None”") is returned instead. Has no meaning outside a function, thus if present at all it *must* be inside a BLOCK that follows a [`def`](#def "Read the entry for “def”") .

> 源自古法语***returner***的中古英语动词，意为“转回”或“回到(原来的位置)”。在计算中，它有两个含义:
> 
> 1.  不及物动词的意思是“将控制权交还(或放弃)给调用过程”，如“当函数退出时， ***将返回*** 控制权”。
> 2.  传递性的意思是“将[一些数据]传递回调用过程”，如“此函数将 ***返回*** 当前时间”。
> 
> 在 Python 中，这两个意思是结合在一起的，因为函数总是将 ***返回*** 给调用者，同时控制*和*数据。

`return`最基本的用法立即结束函数，将`None`返回给调用者:

```
return 
```

这种形式相对很少使用；如前所述，默认情况下，函数*总是*计算为`None`；因此你可以想象`return`是每个函数的*的隐式最后一行。*

因此，更常见的是:

```
return EXPRESSION 
```

它立即计算表达式并将结果对象返回给调用者。

你也可以使用`return`来回传多个对象:

```
return EXPRESSION_A, EXPRESSION_B 
```

依次计算每个表达式，结束函数，并返回一个 [**元组**](https://docs.python.org/3/library/functions.html#func-tuple) (一种固定长度的集合)，其中包含每个表达式的一个对象。

这种多重形式也相对罕见，因为它可能对代码用户来说有点意外，所以需要更多的文档工作，但是它可以方便您不想被其他人使用的内部函数。

你会注意到`return` *在函数求值的时候终止了*函数。函数中该点以下的任何内容基本上都不存在(只有一个例外，异常处理程序中的`finally`块)。那么，当您需要返回数据，但之后又继续工作时，您会如何处理呢？

##### 暂停功能并返回一个值

[yield](#yield "Read the definition of “yield”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-yield-statement "See the documentation for “yield”")

Used to immediately pause execution and temporarily give up control at the point at which it is encountered. If followed by an EXPRESSION, that is evaluated first and the resulting OBJECT is yielded back to the caller of the function; if no EXPRESSION is present [`None`](#none "Read the entry for “None”") is yielded instead. Has no meaning outside a function, thus if present at all it *must* be used inside a BLOCK that follows a [`def`](#def "Read the entry for “def”") .

Can be modified by [`from`](#from "Read the entry for “from”") to form [`yield from`](#yield "Read the entry for “yield from”") , see below.

> 从词源学的角度看，这个单词是这个列表中最奇怪的一个；源自中古英语 ***yielden*** 和古英语 ***gieldan*** ，两者都是“付钱”的意思，与古挪威语 ***gjald*** 和德语 ***geld*** 的词根相同，都是“钱”的意思。今天 ***geld*** 的意思是一种古老的强迫征税或赎金的形式，但它也有“阉割”的意思。历史上，税是国王向英国人征收的一种税。这项税收是为了支付一波又一波的丹麦维京人，大概是为了不阉割英国人(或者至少是他们的国王)。所有这些在这里都不直接重要，但可能会解释为什么计算中的含义源自“让路和放弃控制”，如“ ***向迎面而来的交通产生*** ”和“回馈【结果或投资回报】”，如“该基金每年有 5%的*”。在这两种情况下，暗示着这种情况还没有结束，并且很可能再次发生:你 ***让出*** ，然后你等待，然后你 ***又让出*** 。*
> 
>  *在 Python 中，这有助于记住一个寓言:`return`是死亡，并且只来一次，而`yield`是税收，并且可能只有当死亡到来时才结束。*

 *`yield`的用法与`return`类似:

```
yield 
```

```
yield EXPRESSION 
```

```
yield EXPRESSION_A, EXPRESSION_B 
```

然而*任何使用`yield`的*函数在被调用时实际上会返回一种特殊的集合，称为生成器。与普通集合不同，生成器不会同时在内存中保存所有的项目，而是根据需要通过运行函数“生成”每个项目，直到遇到`yield`并给出一个项目。生成器在该点暂停执行，允许使用生成器的代码处理该项。当需要时，它可以请求下一项，此时生成器继续运行到下一个`yield`，依此类推，直到没有更多的`yield`语句需要运行或者遇到一个`return`。

这就是允许下一个表单:

```
yield from GENERATOR 
```

这是一个特殊的用例，允许你编写一个生成器，它将从另一个生成器的*中依次`yield`每一项。*

对生成器的全面解释超出了本系列文章的范围，因为它们是一个相当高级的主题。现在，知道如果你遇到`yield`你正在看一个发电机就足够了。

### 三个用于操纵名称空间

现在你已经理解了函数的基础，你需要了解[名称空间](https://en.wikipedia.org/wiki/Namespace)，因为当你开始构建更复杂的程序时，它们变得非常重要。

最简单地说，名称空间本质上只是 Python 存储您创建的任何名称的地方，因此每当 Python 需要查找该名称所引用的对象时，就会使用它们。每个名称空间都有一个*范围*，它限制了名称空间“存活”的时间，以及其中的任何名称对于给定的语句是否是*可读的*和/或*可写的*。然而，名称空间形成了一个层次结构，所以规则可能有点难记。

在任何给定的命名空间中，语句可以:

1.  *读取*和*写入*在自己的名称空间中定义的任何名称
2.  *读*，而不是*写*，任何在封闭的*父*命名空间中定义的名字
3.  无论*读*还是*写*任何在*兄弟*或*子*名称空间中定义的名称

最顶层是 Python 自己的名称空间，其中包含 Python 为您提供的所有*内置*名称。这个名称空间*不能被写入*，其中的任何名称基本上都是永久存在的。

接下来是*全局*名称空间，这是您正在工作的模块的名称空间；它成为在该模块中创建的所有其他命名空间的父级。这里定义的任何名字*通常*在程序运行期间都有效。

接下来，当你创建的每个可调用函数被调用时，它会得到自己唯一的*本地*命名空间*；在这里创建的任何名字只有在 CALLABLE 运行时才有效，当 CALLABLE 运行结束时，名字和它所引用的对象都将被销毁，除非使用`return`或`yield`将其传递回调用范围。*

因为您可以定义一个嵌套在另一个可调用对象内的可调用对象，所以您可以构建一个任意深度的名称空间层次结构，因此任何给定的名称都可能存在于任意数量的名称空间中，并且离您实际尝试使用该名称的位置越来越远。不过，这很快就会变得难以处理，这就是为什么我们*试图*限制我们在实际代码中的嵌套数量。

新名字的赋值非常简单:除非被下面的某个关键字修改，否则名字总是在赋值语句出现的范围内被赋值。

但是名字的*用法*有点不太明显:当在语句中使用时，Python 首先在直接的*本地*范围中搜索名字。如果它找不到名字，那么它搜索直接的父(称为*非本地*)作用域，然后它继续搜索每个连续的父作用域，直到它找到名字或者不能解析它并出错。

所有这些通常有些不可见的机制的存在，是为了允许您使用对您工作的范围来说适当且清晰的名称来编写代码。只要它不是一个关键字，并且你不需要使用祖先作用域中的名字，你就可以在本地使用任何你想要的名字，不用担心它会覆盖自己作用域之外的 T2。

大多数时候，你不想摆弄那台机器，但偶尔也有一个很好的理由。或者一个让你困惑的坏消息。

#### 从任何地方写到最上面

在极其罕见的情况下，你想以一种通常不被允许的方式直接操纵*全局*名称空间。

[global](#global "Read the definition of “global”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-global-statement "See the documentation for “global”")

Used to declare a NAME as part of the *global* namespace; the NAME *cannot* have been used previously in the same namespace. In effect this allows a *local* STATEMENT to both create and assign to a *global* NAME it otherwise could only read. *Can* be used within the global namespace, but has no effect.

> 从法语中借来的形容词，有“世界范围内”和“普遍”的一般含义。在计算中，它的意思是“一个程序的所有部分都可以访问的名字”。在 Python 中，它既是最顶层的*名称空间*，可以从所有函数和方法中*读取*，也是将*局部*名称绑定到 ***全局*** 名称空间的关键字，允许它从那个*局部*名称空间中*可写*。

用法相当简单；主要的限制是它不能出现在之后*这个名字已经被使用:* 

```
global NAME
NAME = EXPRESSION 
```

你应该知道`global`的使用是一种*立即* [代码气味](https://en.wikipedia.org/wiki/Code_smell)，因为它的使用*几乎总是*不良编码习惯的指示，因为它*通常*可以——并且几乎肯定*应该*被替换为传递和*返回值*的*参数。如果没有它，有些数据结构和动态代码生成任务是不可能构建的，但是如果你想使用它，那么不要使用它。*

#### 从子写给父

更常见的是，孩子需要告诉父母该做什么。

[nonlocal](#nonlocal "Read the definition of “nonlocal”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-nonlocal-statement "See the documentation for “nonlocal”")

Used exclusively inside *nested* functions / methods (also known as *closures*) to bind a *pre-existing* NAME in the *parent*’s namespace. This allows a STATEMENT in the child to have write access to a NAME defined in its parent. *Must* appear before any reference to that NAME in the local scope.

> 前缀 ***【非 T2】*** (“非”)和 ***【当地】*** (“属于某一特定地方”)组合而成的形容词。这主要是一个科技新词*(字面意思为“新词”)，没有真正的一般意义，只有特定领域的特定含义。在编程中，它表示一个[非局部变量](https://en.wikipedia.org/wiki/Non-local_variable)，意思是“一个既不能在*局部*也不能在*全局*命名空间中访问的名字”。在 Python 中，从嵌套函数的角度来看，它非常具体地应用于封闭函数的名称空间，并且也是将嵌套函数中的*本地*名称绑定到 ***非本地*** 名称空间的关键字，允许它在嵌套函数中是*可写的*。*

 *因为在嵌套函数中只有使用*，所以使用时总会涉及到一些封闭结构:* 

```
def OUTER_FUNCTION():
    NAME = VALUE
    def INNER_FUNCTION():
        nonlocal NAME
        NAME = EXPRESSION
    INNER_FUNCTION() 
```

需要理解的关键是，`nonlocal`的使用方式与`global`的*类似*，但目标更有限，即让嵌套函数的父函数中的变量可写。它也有类似的警告，尽管事实上它的影响并没有被整个*项目感觉到，这使得它本质上不那么危险。尽管如此，它的用途*非常*狭窄，而*相对*较少，所以当参数传递可以使用时，尽量不要滥用它。*

#### 用火杀死它

到目前为止，您已经看到了如何向名称空间添加名称，但是如何从名称空间中删除名称呢？

[del](#del "Read the definition of “del”") [🗎](https://docs.python.org/3/reference/simple_stmts.html#the-del-statement "See the documentation for “del”")

Used to delete a NAME (or NAMEs) from a namespace; if no other references exist to the OBJECT that NAME referred to, the underlying OBJECT is deleted as well.

Can also be used to delete an attribute of an OBJECT or a member (or members) of a COLLECTION *if* the specific type has allowed this operation.

> ***的缩写删除*** ，后者又是一个源自拉丁语 ***deletus*** 的动词。它的一般含义是“摧毁或根除”、“擦除或涂抹”和“彻底清除”，虽然在计算中的具体含义不那么激烈，但它有类似的效果。在 Python 中含义有点间接: ***del*** 用于删除*用户*直接控制的*引用*，而*可能*间接触发从*解释器*控制的进程内存中删除被引用的东西。

最常见的用法是提供一个单独的名称:

```
del NAME 
```

但是，提供多个名称也是有效的，用逗号分隔:

```
del NAME_A, NAME_B, NAME_C 
```

无论哪种情况，Python 都会从左到右进行，从发生了`del`调用的名称空间中删除每个名称。

因为效果仅限于与*最近的*名称空间，所以您必须使用`global`或`nonlocal`来删除本地名称空间之外的名称:

```
global NAME
del NAME 
```

您也可以使用`del`删除对象的属性和集合的成员，但是实际发生的情况取决于所涉及的对象/集合的类型。例如，**列表**集合允许您使用`del`删除其内容
的单个*索引*和*切片*

```
test = [1, 2, 3]
del test[0] # deletes the first item del test[:] # deletes all items 
```

当我们在后面的文章中讨论*内置*类型时，我们将对此进行进一步的探索。与此同时，让我们最终认识一下让您开始为自己定义这种特定类型行为的关键字。

### 一个用于定义新类型的对象

到目前为止，我对一个对象到底是什么一直很模糊。在[面向对象编程](https://en.wikipedia.org/wiki/Class_(computer_programming))范例中，也称为 OOP，一个对象基本上是一个*事物*，它既有*状态*(以命名*属性*的形式)又有*行为*(以可调用*方法*的形式)。相同*类型*的两个单独的事物对于那些属性可能有不同的具体值——我们称它们为相同*类型*的不同*实例*——但是它们共享相同的整体*接口*。然而，还有更多的东西。程序员应该能够定义部分接口，我们称之为*特征*，具有相似需求的类型可以以不同的方式实现，这样它们就可以共享一些共同的属性和行为(这种属性称为 [*多态性*](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) )。此外，这些特征应该能够通过[继承](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming))机制从更一般的类型传递到更具体的类型，就像父母将特征传递给他们的孩子一样。

你可以举一个自然界的例子；鸭子是一种鸟，它继承了所有鸟的某些特征。所有的鸟类都是一种动物，因此继承了所有动物共有的某些特征。一种动物是一种生命，等等。因此，霍华德，一只特定的鸭子，拥有生命、动物、鸟类和鸭子的所有属性和行为，所有这些都很好地集中在一个鸭子类型的实例中。

事实上，正是从这样一个生物分类的例子中，Python 使用了定义新类型对象的关键字。

[class](#class "Read the definition of “class”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#class-definitions "See the documentation for “class”")

Used to define a template for a new type of OBJECT.

> 一个通过法语***class***从拉丁语 ***classis*** 借来的名词，意思是“一个师、军队、舰队”、“武装起来的罗马人民”，以及，奇怪的是，具体地说，“塞尔维乌斯·图利乌斯改革为了征税而将罗马人民分成的六个等级中的任何一个”，这在很大程度上解释了为什么英语是如此难以掌握的语言。然而，我们在这里使用的一般含义是从进化分类学上粗略借用的，意指“共享某些可遗传特征的群体”。Python 中的特定含义来自面向对象编程，意味着“创建共享公共可继承状态(属性)和行为(方法)的对象的模板”。

在 Python 中，通过创建一个也是可调用的新名称来定义一个新类型；从现在开始我们称之为一门课。关键字`class`用于开始一个新的块，该块定义了那个类
的*实现*

```
class CLASS:
    pass 
```

现在可以调用该类来创建其类型的新实例:

```
NAME = CLASS() 
```

从上面可以看出，你可能认为这个实例没有*的*属性或行为，但实际上它有所有对象共享的*最小*接口(例如，它可以被打印出来并在比较中使用，尽管不是很有用)。它收到这个是因为*所有的*新`class`定义都隐式继承自 [**对象**](https://docs.python.org/3/library/functions.html#object) ，它是 Python 的*基类型*。

您还可以指定一个更通用的类(也称为*超类*)来继承:

```
class CLASS(SUPERCLASS):
    pass 
```

事实上，你经常想要从不止一个超类中继承，以便将它们的各种特征混合在一起。这是通过用逗号分隔超类来实现的:

```
class CLASS(SUPERCLASS_A, SUPERCLASS_B):
    pass 
```

当不止一个超类定义了相同的属性或方法名时，[多重继承](https://docs.python.org/3.7/tutorial/classes.html#multiple-inheritance)的概念会变得非常复杂，所以现在让我们保持我们的类简单，并假设没有这样的重叠。

仅仅定义类和它的超类而不给它一些自己的属性和方法是不寻常的，所以你通常会*至少看到一个自定义的*初始化器*方法，按照惯例这是通过定义特殊的*双下划线*(或“dunder】)*实例方法* **__init__** :
来完成的*

```
class CLASS:
    def __init__ (self, NAME):
        self.NAME = NAME 
```

如您所见，实例方法只是使用`def`进行的另一种函数定义。事实上，方法只是添加到类的名称空间中的一个函数。没错，`class`关键字还创建了一种新的本地名称空间，这种名称空间仍然附属于类，并允许使用*点访问*模式实例在*实例*或类本身上查找名称。注意上面方法定义中的**自身**参数名？这是按照惯例赋予任何实例方法的第一个*参数的名称，也是您访问实例本身的属性集的方式。*

这听起来有点抽象，所以让我们通过构建一个思考圆的类来演示一下。

```
from math import tau

class Circle:
    def __init__ (self, radius):
        self.radius = radius

    def diameter(self):
        return self.radius * 2

    def circumference(self):
        return self.radius * tau

    def area(self):
        return tau * self.radius ** 2 / 2 
```

现在如果我们创建一个代表*单位圆* :
的实例

```
unit = Circle(1) 
```

我们可以访问[和**打印**](https://docs.python.org/3/library/functions.html#print)**属性:** 

```
print(unit.radius) 
```

我们也可以调用它的任何一个方法，但是注意我们从来不需要把圆的半径作为参数传递，因为这些方法都可以通过 **self** :
来访问它

```
print(unit.diameter())  
print(unit.circumference())
print(unit.area()) 
```

因为它们是 Python 的[数据模型](https://docs.python.org/3/reference/datamodel.html)中*一切*的基础构建块，所以关于 Python 中的类，有很多*比[要多，并且有很多微妙之处可以进入它们的正确使用(和不正确的滥用)，所以我们将在本系列的后续文章中经常回到它们。尽管现在重要的事情是开始认识如何使用`class`来创建它们，以及在访问它们的属性和方法时如何使用实例。了解有相当多的“dunder”方法也很有帮助，这些方法用于实现许多常见的“底层”功能，这些功能支持我们将在后面的文章中开始遇到的所有](https://docs.python.org/3.7/tutorial/classes.html#classes)[内置函数](https://docs.python.org/3/library/functions.html)。*

### 一个用于在上下文中工作

有时候，有些操作您*总是*想要在特定的上下文中执行，比如提交数据库事务或关闭网络连接。这类事情*通常*涉及某种形式的*异常处理*或其他特定于任务的样板文件，这可能导致许多样板文件需要重复。一个简单的例子就是读取一个文件:打开文件必然意味着从底层操作系统获取一些资源，并且您总是希望释放这些资源，即使您无意中试图打开一个不存在或您没有权限读取的文件。该文件是一个*类型的*对象，它将总是携带管理资源的上下文需求，因此每次打开文件时，您都需要编写相同的`try`和`finally`样板文件*。*

除非你正在使用的语言提供了一种方便的方式来确保这一切发生。

[with](#with "Read the definition of “with”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement "See the documentation for “with”")

Starts a [*context manager*](https://docs.python.org/3/reference/datamodel.html#context-managers "Read about context managers in Python") BLOCK, which ensures that the indented STATEMENT(s) below it are performed within the context of the OBJECT being managed.

Can also be marked with [`async`](#async "Read the entry for “async”") , to start an [`async with`](#async "Read the entry for “async with”") , see below.

> 中古英语介词，其发音来自一个古英语术语*(“反对”)，但其现代含义来自另一个术语 ***mid*** (“与……有关联”)，后者又来自德语 ***mit*** 。Python 中的含义来源于 内的 ***，意为“在【某事物或事件】的范围或上下文内”。****

 *任何对`with`的使用都需要一个表达式，该表达式计算出的对象满足[上下文管理器类型](https://docs.python.org/3/library/stdtypes.html#context-manager-types)的接口，因此实现了 **__enter__** 和**_ _ exit _ _**“dunder”方法。从现在开始，我们将称这个表达式为 CONTEXT_MANAGER，以使例子更加清晰。

在最基本的用法中，你只需启动一个新程序块:

```
with CONTEXT_MANAGER:
    STATEMENT 
```

它将通过调用`CONTEXT_MANAGER. __enter__ ()`进入上下文，在上下文中执行语句，然后通过调用`CONTEXT_MANAGER. __exit__ ()`退出上下文。

但是大多数情况下，你会希望使用`as`将由`CONTEXT_MANAGER. __enter__ ()`返回的对象分配给一个别名，这样程序块就可以使用它:

```
with CONTEXT_MANAGER as NAME:
    STATEMENT 
```

这可以为您节省大量样板文件，尤其是对于许多已经实现了上下文管理器接口的标准对象。例如，读取文件内容的推荐方式:

```
with open(path) as src:
    contents = src.read() 
```

难道*比*要简单得多，否则你不得不写:

```
src = open(path)
try:
    contents = src.read()
finally:
    src.close() 
```

有时你会想在多个上下文管理器中工作:

```
with CONTEXT_MANAGER_A as NAME_A, CONTEXT_MANAGER_B as NAME_B:
    STATEMENT 
```

这与将一个`with`嵌套在另一个
中完全一样

```
with CONTEXT_MANAGER_A as NAME_A:
    with CONTEXT_MANAGER_B as NAME_B:
        STATEMENT 
```

在学习 Python 标准库的过程中，您将会遇到很多上下文管理器。现在你知道构建你自己的只是创建一个新的`class`和实现几个“dunder”方法的问题。

### 最后…两个异步工作

根据定义，你单独用`def`编写的任何函数或方法都是*同步的*，这意味着当你调用它的时候，你的运行代码必须停止所有其他的事情，然后*等待*，不管需要多长时间，在你的代码的其余部分可以被执行之前，你的函数或者`return`或者`yield`控制回它。对于大多数发生在本地机器上的任务来说，这是明智的，也是非常好的，尤其是当你在继续下一步之前需要答案的时候。

然而，在许多现代编程中，经常需要与那些*不能快速响应的东西进行交互，比如与可能在另一个世界的服务器进行网络交互，有时在等待它们响应的时候，你可能会有大量的*工作要做。你仍然*需要*答案，但你最终*需要*。解决这个问题的一种方法是通过[异步编程](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming))，从 Python 3.5 开始，它已经成为 Python 语言的一级部分。**

异步编程是一个相当专业的高级主题，所以使用它已经超出了本系列的范围。它也是这门语言的一个新的补充，它的用法在不同的版本之间有所波动。我不会试图总结这些变化，我将在下面描述在 Python 3.7 中使用这些关键字的基础知识。然而，如果你很好奇，这里有一个很好的异步引物，看起来是最新的。

因为异步代码与同步代码做同样的工作，但是*以不同的方式调度*该工作的执行，所以只需要在语言中添加两个新的关键字。

[async](#async "Read the definition of “async”") [🗎](https://docs.python.org/3/reference/compound_stmts.html#coroutine-function-definition "See the documentation for “async”")

Used to mark another KEYWORD as one that works asynchronously. As such, [`async`](#async "Read the entry for “async”") *cannot* appear on its own.

With [`def`](#def "Read the entry for “def”") as [`async def`](#async "Read the entry for “async def”") to define an asynchronous function, also known as a COROUTINE.

With [`for`](#for "Read the entry for “for”") as [`async for`](#async "Read the entry for “async for”") to loop over an *asynchronous iterator* inside an [`async def`](#async "Read the entry for “async def”") .

With [`with`](#with "Read the entry for “with”") as [`async with`](#async "Read the entry for “async with”") to use an *asynchronous context manager* inside an [`async def`](#async "Read the entry for “async def”") , see below.

> 你可能已经猜到了，这是现代英语单词 ***asynchronous*** 的缩写，由拉丁语词根 ***a-*** (“不是”)和 ***syn-*** (“在一起”)与 ***Khronus*** (古希腊的*时间*的拟人)组合而成。它明确表示“不同时发生”。在 Python 中，它更具体地将操作标记为“与调用者不同时发生”，这允许调用者等待该操作的结果，该操作将在未来的某个时间点发生。

因为`async`的含义与它所标记的关键字联系在一起，所以它的用法总是相同的:

```
async KEYWORD 
```

然而，与我们见过的其他任何关键字不同，`async`的用法将*总是*以新协程的定义开始:

```
async def COROUTINE():
    STATEMENT 
```

其他两种形式的`async`都允许你在协程中与*其他*协程一起工作*，因此它们只能存在于*和`async def`中的*。*

例如，你可以使用`async for`来循环一个[异步迭代器](https://docs.python.org/3/reference/datamodel.html#asynchronous-iterators)，比如一个生成器协程(它只是一个使用`yield`而不是`return`的协程)。

```
async def COROUTINE():
    async for item in GENERATOR_COROUTINE:
        STATEMENT 
```

您还可以使用`async with`在[异步上下文管理器](https://docs.python.org/3/reference/datamodel.html#asynchronous-context-managers) :
的上下文中执行工作

```
async def COROUTINE():
    async with CONTEXT_MANAGER_COROUTINE as NAME:
        STATEMENT 
```

事实上，每个协程都需要等待其他协程，这就是为什么有另一个关键字只能在`async def`中使用。

[await](#await "Read the definition of “await”") [🗎](https://docs.python.org/3/reference/expressions.html#await-expression "See the documentation for “await”")

Used to suspend the execution of the COROUTINE it is found *within* and waits for the COROUTINE to its right to complete; can only be used inside an [`async def`](#async "Read the entry for “async def”") .

> 源自中古英语动词 ***awaiten*** (【等待】)源自古法语***await ier***/**/T9】agait ier**(【埋伏着等待、观看或观察】)。一般意义上比 ***等*** 更主动，更敌对，这是它的一个变形。在异步编程中，这意味着“[暂停执行]并等待[某件事情完成]”。

关键字`await`总是被用来调用`async def` :
中的协程

```
async def OUTER_COROUTINE():
    await COROUTINE() 
```

这将暂停外部协程的执行，并等待直到被调用的协程最终返回控制。被调用的协程返回的任何值都通过`await`传递，所以你可以把`await COROUTINE()`看作一个异步表达式，它将*最终*计算出协程在被调用时返回的任何值。

当然，现在你已经有了做异步工作的基础，你实际上如何*执行*这样的工作呢？从 Python 3.7 开始，仍然需要使用由 [asyncio](https://docs.python.org/3/library/asyncio.html) 模块提供的功能，其细节在这些文章的范围之外。详见我之前提到的[异步引物](https://realpython.com/async-io-python/)。

## 咻……

现在你有了它们，Python 的 35 个关键词:它们本身不足以让你流利地使用 Python，但是如果你已经读到这里并消化了它，你就已经开始真正地*理解*(而不仅仅是*使用*)*的框架，这是一种发展最快的通用编程语言*中正在发生的事情。在本系列的下一篇文章中，我们将远离*单词*，转而关注*符号*，深入探究 Python 的稍微小一点，但谢天谢地*要简单得多的*操作符*列表*。

* * *

* * *

1.  Python 3.7 中有 35 个关键字，这是在撰写本文时该语言的当前主要版本。新的关键词很少被添加，而被删除的就更少了，但是无论你在哪个版本中，你都可以使用`from keyword import kwlist; print(kwlist)`来查看当前的列表。 [↩](#fnref1)

2.  将`def`放在一行中使得与`lambda`的等价性更加明显，但是为了可读性，不要经常这样做。 [↩](#fnref2)**************