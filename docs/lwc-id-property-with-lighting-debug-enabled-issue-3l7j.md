# LWC-照明调试启用问题的 Id 属性

> 原文：<https://dev.to/brettmn/lwc-id-property-with-lighting-debug-enabled-issue-3l7j>

[https://www.youtube.com/embed/VW6mp38wVrU](https://www.youtube.com/embed/VW6mp38wVrU)

你好，我是布雷特和 WIPDeveloper.com。前几天在开发一个 Lightning web 组件时，我遇到了这个问题，当导入 Salesforce 用户 ID 并将其分配给组件中一个名为 ID 的属性时。这是一个非常简单的组件，因为这就是它在 JavaScript 中所做的一切。它在 HTML 中所做的就是显示 ID。现在，这很简单。你认为不应该有任何问题。页面上什么都没有写。

当您启用 lightning 调试模式时，问题就会出现。因此，如果我进入调试模式，启用它，并刷新页面上的组件，我开始看到这个，`render threw an error in 'c:importUserId Trial' [Assert, Violations: Failed to construct '<c-import-user-id-trial>': The results must not have attributes.]`并花了一些时间来解决它。在推特上看到了一些对此的回应。我认为问题在于重要声明本身。显然，它在我打开 lightning 调试模式之前就已经工作了。所以如果我关掉它，刷新页面，它又可以工作了。但是如果我想要调试调制解调器，这不是一个解决方案。因此，我将启用它，确保它仍然是一个问题。的确如此。现在，我将把属性名从 it 改为其他名称。所以感谢你，用户，我保存它，推这个，刷新页面。也许还有第二次。

哦，我应该更新 HTML 模板。

刷新页面。我们现在可以看到 RTD 身份证。我们可以在浏览器中打开开发人员控制台，看到我们处于快速调试模式，因为我们从 Salesforce 获得了所有这些精彩的消息。耶。因此，如果你遇到渲染器抛出一个错误，断言违例检查，看看你是否有任何属性，称为公正的 ID，ID 和相应的调整。据我所知，这不应该被阻止。基于我看到的一些回复。这不应该是一个问题，除非是一个 API 修饰的属性，但这不是我遇到的情况。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

灯光调试启用问题的帖子[LWC Id 物业最早出现在](https://wipdeveloper.com/lwc-id-property-with-lighting-debug-enabled-issue/)[WIPDeveloper.com](https://wipdeveloper.com)上。