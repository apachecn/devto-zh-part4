# 表单、Chrome 和自动填充

> 原文：<https://dev.to/jbradford77/forms-chrome-and-autofill-iaa>

我最近遇到了一个不寻常的问题，有点令人沮丧，所以我希望这篇文章能帮助那些陷入困境的人。

我有一个表单，根据用户在两个选择字段中的选择，从大约 50 人的列表中选择一两个人。我收到了名单上一个人的电子邮件，说她收到了所有的表格。我仔细检查了几次基于选择来切换人员的代码，并且为了更好地衡量，重写了一次。所有测试都没问题。

尽管如此，她还是收到了所有的表格。我开始手动测试表单，发现 chrome 中的自动填充设置决定了组织名称的选择实际上是家庭州的字段。我猜它这样做是因为其中一个组织的名字包括一个州名。

我第一次尝试修复它是在字段上设置 autocomplete =“off”。那什么也没做。在查找解决方案时，我发现 chrome 覆盖了 autocomplete =“off”。

要找到 chrome 与表单字段关联的所有内容，请进入 chrome://flags，搜索# show-auto fill-type-predictions，并将其设置为 Enabled。重新启动 chrome，进入你的表单，悬停在上面。您将看到一个包括启发式属性的列表。这是 chrome 将尝试自动填充的内容。

Autocomplete 有一组您可以使用的标签，这里列出了[和](https://html.spec.whatwg.org/multipage/form-control-infrastructure.html#autofilling-form-controls:-the-autocomplete-attribute)。我添加了 organization-title，但仍然没有修复它，甚至没有更新 chrome 中的启发式类型。

最终让它正常工作的方法是让字段只读，直到用户将焦点放在它上面。