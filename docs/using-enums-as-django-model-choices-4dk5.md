# 使用枚举作为 Django 模型选择

> 原文：<https://dev.to/bencleary89/using-enums-as-django-model-choices-4dk5>

# 我们这个快速概览的目标是让我们的模型选择更有组织性和可读性，是的，这需要更多的工作，但回报是值得的。

### 注

因为所提供的代码通常不适合生产，所以它是一个示例，展示了一些可以用来解决特定问题或任务的技术，允许您在自己的代码和工作流中实现这些技术。永远先学会它，不要只是复制和粘贴😅如果你这样做了，确保你添加了测试，错误处理等等

### Python 中的枚举

我们可以使用 python 内置的 Enum 类来创建清晰的、可重用的常量，不仅用于模型，还可用于其他地方。如果你不确定枚举的概念，你可以在这里查看 python 文档。

### 为什么？当元组工作正常时…

说得好……它确实工作得很好，我不是说你不应该把它作为一个选项。这是你工具箱里的另一种方法或工具，当你需要它或它合适的时候，你可以使用它。

### 推理

枚举最适合用来表示一组不可变的值，例如，这可能是一周中的某几天、几个月，或者是应用程序中的任意一组常量。

### 场景

我们想象中的 crm 系统 Cake CRM 目前使用一个客户类型元组。我们遇到了一个问题，工程师在比较中使用了错误的值，我们还想在应用程序的其他地方使用这些值。

### 举例

我们目前的模式是这样的: