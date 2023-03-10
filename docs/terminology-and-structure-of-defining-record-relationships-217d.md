# 定义记录关系的术语和结构

> 原文：<https://dev.to/mjb2kmn/terminology-and-structure-of-defining-record-relationships-217d>

在将一个数据条目与另一个数据条目匹配时，寻找一些关于命名和结构的意见。
具体来说，使用情形是将文件与主机相匹配，但这一方法也适用于其他关系。

磁盘上的文件包含带有分隔符的 yaml 前端内容，其后是文件的内容，非常类似于 dev.to 上的帖子。在前端内容中，我需要列出要匹配的主机属性和不应匹配的属性，并定义大于/小于测试。重要的是，命名和结构要易于系统管理员理解和理解。

## 基础上下文

没有匹配标准的简化示例，这将是所有示例中使用的静态上下文:

```
path: /etc/hosts
mode: 0644
user: root
group: root
---
127.0.0.1 localhost
::1 ip6-localhost 
```

## 第一选项

现在添加只匹配具有“Linux”的`OS.Type`和大于`18.00`但不匹配“prlvm”的`Platform`的`Hosts`:
的条件

```
path: /etc/hosts
mode: 0644
user: root
group: root
assert:
  - os.type: linux
  - os.version: 18.00+
refute:
  - platform: prlvm
---
127.0.0.1 localhost
::1 ip6-localhost 
```

这里的`assert`键包含必须匹配的主机属性和值的列表。`os.version`需要特殊处理才能作为浮点值进行比较，即使它是作为字符串存储和传输的，并且操作符`+`和`-`需要被解析出来并进行相应的计算。我认为从用户的角度来看，这基本上是清晰和简洁的，但是对诸如`os.version`这样的值的特殊处理可能会在以后的道路上使这变得非常复杂。

我还在考虑分别用`match`和`except`来代替`assert`和`refute`，因为它们对系统管理员来说可能更直观。

## 第二选项

另一种方法是减少抽象，使用程序中发生的匹配的更直接的表示。我觉得这样更准确，代价是更啰嗦。

```
path: /etc/hosts
mode: 0644
user: root
group: root
match:
  equal:
    - os.type: linux
  greaterthan:
    - os.version: 18.00
  not:
    - platform: prlvm
---
127.0.0.1 localhost
::1 ip6-locahost 
```

这将产生相同的最终结果，但是在处理它的应用程序中具有更简单的逻辑。我们可以合理地假设在`greaterthan`键下提交的值可以被视为数字，无需特殊解析。我们可以更清楚地区分`equal`和`contains`，甚至添加像`startswith`这样的操作符。我认为这开始看起来像弹性搜索过滤器，我从来没有一个大风扇，但它确实使后端编码更安全和更容易，我认为更明确地为用户设置期望。

在最后一个例子中，`match`可以改为`select`或其他术语。

最终，我试图使它对系统管理员终端用户最友好，同时对开发人员来说仍然相当安全、可测试且不过分复杂。

## 更新

我正在进行第二个选择。我认为清晰的好处值得增加冗长。虽然基线的复杂性高于其他选项，但我相信复杂性不会随着记录的数量和大小而显著增加。它还需要应用程序中更多的逻辑，但不是更复杂的逻辑，这应该使程序更容易维护和测试。