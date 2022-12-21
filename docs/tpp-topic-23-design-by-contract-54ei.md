# TPP 主题 23:合同设计

> 原文：<https://dev.to/steadbytes/tpp-topic-23-design-by-contract-54ei>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-23-exercises/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 思考要点:如果 DBC 如此强大，为什么没有得到更广泛的应用？想出合同很难吗？它是否让你想到了你现在宁愿忽略的问题？是不是逼着你去思考！？显然，这是一个危险的工具！

契约式设计引发了类似于动态类型与静态类型之争的争论:

*   代码简洁性
    *   动态语言*可以更简洁吗*
*   迭代速度
    *   动态语言通常更快(尽管有限制)
    *   静态语言需要更多的预先考虑
*   创建*通用*代码的简易性
    *   在动态语言中通常更容易

我强调以上几点都不是绝对的真理；当然，双方都有例外，它们也不一定是*我的观点*。

为了使 DBC 有效，需要知道系统的约束*并考虑*。通常，在开发过程中，这两种方法中的一种或两种都没有使用。此外，我认为有一个**概念**，即(类似于静态类型)DBC‘固化’了代码的实现，从而阻碍了敏捷、迭代的开发。然而，如果 DBC 得到认真执行，情况就不是这样了。由于
软件变更的需求应该是一种正常的实践，所以实现应该与合同和变更合同所施加的约束相分离。然而，实现这一点需要更多的前期工作。

## 练习 14

> 设计一个厨房搅拌机的界面。它最终将成为一个基于网络、支持物联网的搅拌机，但目前我们只需要界面来控制它。它有十种速度设置(0 表示关闭)。不能空操作，一次只能改变一个单位的速度(即从 0 到 1，从 1 到 2，不能从 0 到 2)。
> 
> 下面是方法。添加适当的前置条件和后置条件以及一个不变量。

```
int getSpeed()
void setSpeed(int x)
boolean isFull()
void fill()
void empty() 
```

完整代码和测试见 [dbc_blender](https://github.com/SteadBytes/study/tree/master/the-pragmatic-programmer-20th/23/exercises/dbc-blender) 。

我选择在 Clojure 中实现这一点，因为它内置了对由[条件映射](https://clojure.org/reference/special_forms#_fn_name_param_condition_map_expr)和优秀的 [`spec`](https://clojure.org/guides/spec#_specing_functions) 提供的前置和后置条件的支持，这是对数据施加约束的理想选择(也因为我喜欢 Clojure)。

为了更好地适应 Clojure 的函数式方法，我对提议的 OOP 风格接口做了一些修改:

*   blender 被模仿成 [`map`](https://clojure.org/reference/data_structures#Maps) 而不是一个类

```
 =>  {::speed  5  ::full  true} 
```

*   `getSpeed`和`isFull`没有实现，因为搅拌机`map`的按键可以直接使用

```
 ; getSpeed  =>  (::speed  {::speed  5  ::full  true})  5  ; isFull  =>  (::full  {::speed  5  ::full  true})  true 
```

*   `setSpeed`、`fill`、`empty`功能不是`void`；取而代之的是返回一个完整的混合器`map`，更新以反映期望的变化，从而避免状态突变

在深入任何实现之前，这里是任何代码
示例将在其中工作的名称空间定义:

```
(ns  dbc-blender.core  (:gen-class)  (:require  [clojure.spec.alpha  :as  s]  [clojure.math.numeric-tower  :as  math])) 
```

首先，需要确定搅拌机速度的有效范围。问题陈述了
速度必须一次增加一个单位；表明一个`integer`数据类型是有意义的(尽管
需要进一步加强)。此外，速度不能为负(至少在我见过的所有搅拌机上)，速度值有一个合理的限制。

```
; turn it up to 11!  (def  max-speed  11)  ; speed is an integer and in correct range  (s/def  ::speed  (s/and  int?  #(and  (>=  %  0)  (<=  %  max-speed)))) 
```

接下来，blender `map`有一些约束:

*   需要两个键`::speed`和`::full`
*   `::full`必须是`boolean`类型
*   搅拌机应该只有装满时才打开。这在练习中没有明确说明，但这是我选择应用的一个合理的设计约束

```
(s/def  ::full  boolean?)  (s/def  ::blender  (s/and  (s/keys  :req  [::speed  ::full])  #(if  (>  (::speed  %)  0)  ; should only be on when full  (::full  %)  true))) 
```

`set-speed`约束条件:

*   将有效的`::blender`和有效的`::speed`作为输入
*   用更新的`::speed`值返回新的有效`::blender`
*   `::speed`只能增加一个单位

```
(defn  set-speed  [blender  x]  {:pre  [(s/valid?  ::blender  blender)  (s/valid?  ::speed  x)  (::full  blender)  (=  (math/abs  (-  (::speed  blender)  x))  1)]  ; increase in single increments  :post  [(=  (::speed  %)  x)  ; speed was set  (s/valid?  ::blender  %)]}  (assoc  blender  ::speed  x)) 
```

`fill`约束条件:

*   将有效的`::blender`作为输入
*   返回一个新的有效的已满的`::blender`
*   搅拌机不能已经满了
*   搅拌机无法打开
    *   潜在危险
    *   弄得一团糟

```
(defn  fill  [blender]  {:pre  [(s/valid?  ::blender  blender)  (=  (::speed  blender)  0)  ; can't fill a spinning blender  (not  (::full  blender))]  ; can't fill an already full blender  :post  [(s/valid?  ::blender  %)  (::full  %)]}  ; blender was filled  (assoc  blender  ::full  true)) 
```

`empty`约束条件:

*   将有效的`::blender`作为输入
*   返回一个新的有效的空的`::blender`
*   搅拌机不能已经是空的
*   搅拌机无法打开(与`fill`的原因相同)

```
(defn  empty  [blender]  {:pre  [(s/valid?  ::blender  blender)  (=  (::speed  blender)  0)  ; can't empty a spinning blender  (::full  blender)]  ; can't empty an empty blender  :post  [(s/valid?  ::blender  %)  (not  (::full  %))]}  ; blender was emptied  (assoc  blender  ::full  false)) 
```

看看[测试](https://github.com/SteadBytes/study/blob/master/the-pragmatic-programmer-20th/23/exercises/dbc-blender/test/dbc_blender/core_test.clj)中关于接口正确和不正确使用
的综合例子，以及它们是如何被指定的契约强制执行的。

## 习题 15

> 数列 0，5，10，15，…，100 中有多少个数字？

Twenty-one

我一开始对这个问题略感困惑；一部简单的连续剧和 DBC 有什么关系？然而，它的目的是挑战读者考虑该系列的精确的
规格。根据作者提供的解决方案:

> 如果你说 20，你只是遇到了栅栏错误(不知道是要计算栅栏还是栅栏之间的空间)

答案为 20 意味着起始值或结束值的一系列*互斥*:

*   0, 5, 10, 15, ..., 95
*   5, 10, 15, ..., 100

这里有一些 Python 来演示这些，记住`range`函数
是*开始包含*和*结束独占* :

```
>>> len(range(0, 105, 5)) # correct 21

>>> len(range(0, 100, 5)) # incorrect: excludes the final element 100 20

>>> len(range(5, 105, 5)) # incorrect: excludes the starting element 0 20 
```