# 不可变数据结构

> 原文：<https://dev.to/martinhaeusler/immutable-data-structures-2m70>

函数式编程目前正在兴起，因为它能够从一开始就防止大量错误。函数范式的两个基石是**纯函数**和**不可变数据结构**。今天我们将看看不可变集合——以及为什么它们比你想象的要多。

# 等级的不变性

不变性实际上有几个不同的“层次”。很难将它们分开，因为术语经常被错误地使用。我试着尽可能的清晰和一致。

# 可变集合

可变集合是我们在 Java、C#和 JavaScript 等语言中最常见的集合。它们通常内置于标准库中。它们的主要特点是我们可以创建这些集合的一个实例，然后按照我们的意愿修改它。

```
List<String> list = new ArrayList<>();
list.add("Hello");
list.contains("Hello"); // -> true 
```

### 优点

*   非常高的灵活性。没有你不能用可变集合覆盖的用例。
*   快速插入和修改。

### 弊

*   通常当你接收一个集合作为方法参数时，你必须制作一个**防御副本**。一个完整的副本在内存和时间复杂度上都是`O(n)`。
*   如果你不创建防御性副本，你可能最终会使用一个集合并与另一段代码共享它。如果任何一方进行了更改，另一段代码就会看到该更改，从而有可能破坏它。这种错误非常微妙，很难调试。
*   可变集合不太适合并发程序。对共享可变集合的并发修改需要**适当的外部锁定**。

### 把它们用于...

*   不共享的局部变量。
*   本地聚集计算结果。
*   与任何基于反射的框架接触的领域(最突出的是对象关系映射器，如 [Hibernate](https://hibernate.org/) )。
*   由单个线程频繁更改的集合。
*   当所有其他类型的集合都不起作用时，作为备用方案。

# 不可修改的集合

不可修改的集合仅仅是底层可变集合上的**视图**。您不能修改视图，但是对底层可变集合的任何修改在视图中都是**可见的**。

```
List<String> original = new ArrayList<>();
original.add("Test");
List<String> view = Collections.unmodifiableList(original);

// changing the view is forbidden
view.add("x"); // -> Runtime exception: the view is unmodifiable!

// reading from the view is fine
view.contains("Test"); // -> true

// changes in the original collection...
original.add("Foo");
// ... are visible in the view
view.contains("Foo"); // -> true 
```

### 优点

*   如果您有一个“拥有”列表的对象，并且只是让其他人读取它，那么这是非常有用的。
*   如果您创建了一个不可修改的视图，并丢弃了对原始视图的所有引用，那么您最终会得到一个真正不可修改的集合(见下文)。

### 弊

*   不可修改视图的界面与原始集合相同。也就是说:`view.add(...)`将会是**有效**根据编译器。它将出现在您的代码完成中。如果你**不知道**你正在处理一个不可修改的视图，你会遇到很多运行时异常。不可修改的视图可以欺骗 API 的用户，让他们相信他们正在处理可变的集合。一些语言，比如 Kotlin，实际上就是因为这个原因，才区分了`List`和`MutableList`接口之间的**。**
*   没有有效的方法来创建包含附加变更的不可修改视图的“变更副本”。唯一的方法是**将整个视图内容复制**到一个新的(可变的)集合中，并在那里应用更改。那就是`O(n)`时空复杂度。
*   **巨大的陷阱:**如果您接收一个集合作为参数，并且您正在调用它，例如`Collections.unmodifiableList`，那么您将无法避免外部修改！请记住:您只创建了一个视图。您收到的原始收藏可能仍会从外部进行更改。因此这是**而不是**防御副本的替代品！

### 把它们用于...

*   以只读方式与世界共享您的对象状态。不要忘记在您的 API 中记录您返回的集合是不可修改的视图。

# 持久收藏

这一类(不)可变性极其重要，但经常被忽视，甚至被贴错标签。持久集合与“存储到磁盘”中的“持久性”没有任何关系。持久集合完全是**不可变结构**，除了它们提供**智能复制转换**。持久数据结构的主要原则是:

> 如果你修改了数据结构，你会得到一个新的数据结构，它包含了旧的数据以及你的修改。

听起来很熟悉？这正是 [ImmutableJS](https://immutable-js.github.io/immutable-js/) 所做的。这个库的名字本身就极具误导性，因为它实际上提供的是持久集合，这比严格不可变的集合有用得多。对于 Java 来说，有一个例子(恰当地命名)[集合](https://pcollections.org/)库:

```
PSet<String> original = HashTreePSet.empty(); 
PSet<String> mod1 = original.plus("Hello");
PSet<String> mod2 = mod1.plus("World");

// the original does not know what happened in mod1 and mod2
original.contains("Hello"); // -> false
original.contains("World"); // -> false
original.size(); // -> 0

// mod1 knows the original and itself, but does not know about mod2
mod1.contains("Hello"); // -> true
mod1.contains("World"); // -> false
mod1.size(); // -> 1

// mod2 knows original and mod1, as well as its own modification
mod2.contains("Hello"); // -> true
mod2.contains("World"); // -> true
mod2.size(); // -> 2 
```

考虑持久化集合的最简单的方法是:做出一个改变，获得一个包含该改变的**副本**(**副本**是关键词)。您可能会认为这是非常低效的，因为复制会带来运行时和空间复杂性的`O(n)`成本。虽然这种天真的“复制和更改”方法会满足接口，但这实际上**而不是**持久集合中发生的事情。诀窍在于所有现有的数据都不会改变；因此，我们可以随心所欲地重复使用它。在上面的例子中，`mod2`将在内部**重用`mod1`的内容**。没有实际的复制在进行，但你永远不会意识到这一点，因为在创建了`mod2`之后，你再也不能**改变** `mod1`。

### 优点

*   真实可靠的不变性。永远不需要防御性副本。
*   还是比较容易上手的。
*   在并发读/写下是安全的，写/写并发有时可能需要额外的同步。
*   持久集合上的所有操作都是纯函数。没有副作用，没有惊喜。

### 弊

*   持久集合的嵌套可能很难实现(hello， [Redux](https://redux.js.org/) ！).
*   如果对单个持久数据结构进行大量修改，性能会受到影响。创建一个可变副本，应用您的所有修改，然后创建一个持久版本可能会更有效(导致`O(n) + O(n)`空间和时间复杂性)。
*   如果您没有意识到您正在使用一个持久集合，您可能会忘记将`list.plus("test")`的返回值赋给一个变量，然后想知道为什么`"test"`不在您的列表中。
*   出于兼容性的原因，一些集合框架，如 PCollections，实现了常规的(可变的)集合接口(例如，`PSet<T>`扩展了`Set<T>`)。因此，持久化集合继承了变异方法，比如`void add(E element)`，它在被调用时抛出运行时异常。

### 把它们用于...

*   表示读写访问下的共享状态。
*   单个生产者和多个并发消费者的场景。
*   您必须计算基于初始解决方案的多个备选解决方案的场景。

# (真正意义上的)不可变集合

真正不可变的集合只通过它们的 API 提供读访问，并且必须在创建时填充。之后，他们的内容被密封，并设置在石头上永远。你可以把真正不可变的集合想象成“没有写时复制帮助器的持久集合”。

据我所知，Java 中没有真正不可变的集合接口。在 Kotlin 中，您可以这样做:

```
// "listOf" creates a truly immutable list in Kotlin.
val list = listOf("Test")
list.contains("Test") // -> true 
list.add("foo") // -> compile error: interface "List" has no method "add" 
```

### 优点

*   易于高效实施(所有数据都必须提前知道)
*   迭代/分析可能比持久收集更快

### 弊

*   对于真正不可变的集合，不存在不能与持久集合一起工作的用例。
*   非常有限的灵活性。

### 把它们用于...

*   定义常数。

# 关闭思绪

我希望您喜欢这次(im)可变集合之旅。有时很难恰当地交流这个话题，因为术语经常被误用。我希望这篇博文能在将来帮助你区分(im)可变集合的类型，它们的优缺点，以及何时使用它们。