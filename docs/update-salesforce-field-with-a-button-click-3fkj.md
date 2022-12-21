# 点击按钮更新 Salesforce 字段(Salesforce Classic)

> 原文：<https://dev.to/rachelsoderberg/update-salesforce-field-with-a-button-click-3fkj>

在我开始之前，我想指出这篇文章中的信息很快就会过时。Salesforce 正在逐步淘汰 Salesforce Classic，并将每个人都转移到 Lightning Experience，因此他们正在逐步淘汰 JavaScript 按钮和操作。我计划发布一篇后续文章，介绍使用 Lightning 自定义组件重新构建您的 JavaScript 按钮的过程，这是我在公司的组织中更新它们的过程中，他们执行相同任务的新首选方法。

# 创建按钮

Salesforce 有两种不同类型的对象-标准和自定义。Salesforce 直接提供标准对象，而自定义对象由组织管理员创建。以下是为这两种类型的对象创建新按钮的步骤:

**标准物体**

1.  单击“设置”,在左侧工具栏中找到并展开“构建”>“自定义”>{您的对象}。
2.  在{您的对象}下，单击“按钮、链接和动作”。
3.  单击新建按钮或链接。

**自定义对象**

1.  单击“设置”，在左侧工具栏中找到并展开“构建”>“创建”>“对象”。
2.  在自定义对象列表中找到并单击{您的对象}。
3.  向下滚动到按钮、链接和操作部分，然后单击新建按钮或链接。

从现在开始，标准对象和自定义对象的步骤应该是相同的:

1.  填写标签，名称应该会自动填充。如果需要，包括描述。
2.  选择您想要的显示类型。这可能是自定义字段中的链接、对象详细信息页面上的按钮或显示在相关列表中的按钮。
3.  “执行 JavaScript”是您选择的行为，这将触发一个与 Lightning Experience 兼容问题的警告。我们将暂时忽略这一点，稍后当我们切换到 Lightning 时更新。
4.  为内容源选择“OnClick JavaScript”。
5.  保存您的新按钮。

# 用 JavaScript 更新字段

您已经设置好了一个新按钮，并准备就绪——现在剩下的就是添加更新字段的功能。如果你熟悉 JavaScript，这将是一件轻而易举的事，但如果不熟悉也没关系！*(我对 JS 不是很精通，所以如果我的术语有任何错误，我道歉，并欢迎任何更正！)*

1.  如果您保存并关闭了按钮，请从对象的“按钮、链接和操作”部分重新打开它，然后单击“编辑”。
2.  所需的第一步是在函数内容编辑器的顶部添加对工具箱的引用: *{！require script("/soap/Ajax/19.0/connection . js ")}*。
3.  实例化一个新的 SObject，传入你的对象名。
4.  将新的 SObject.id 设置为您的对象。Id 字段。
5.  更新 SObject。字段要修改的每个字段的名称。您可以使用字段类型和插入字段下拉列表从对象中查找特定字段。
6.  用以下内容结束文件:result = sforce . connection . update([您的 s object])；
7.  检查语法以确保 JavaScript 功能正确，然后保存。
8.  将您的新按钮添加到对象的页面布局中，并单击它进行测试！

上述步骤本身可能会令人困惑，因此以下是我的组织的自定义 RMA 对象上的一个按钮示例:

```
 {!REQUIRESCRIPT("/soap/ajax/19.0/connection.js")} 

  var rma = new sforce.SObject("RMA__c");
  rma.id = "{!RMA__c.Id}";
  rma.Printed__c = new Date().toISOString();
  rma.Printed_By__c = "{!User.Name}";

  result = sforce.connection.update([rma]); 
```

我希望这份关于点击按钮时更新 Salesforce 字段的简要介绍能够启发您为您的 Salesforce 工作流添加一些额外的功能。请在接下来的几周里关注我的闪电体验按钮更新！

* * *

如果你想在社交媒体上与我交流，来 Twitter 或 T2 LinkedIn 打个招呼吧！