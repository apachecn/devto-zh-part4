# 在 Phoenix 模板中使用组件

> 原文：<https://dev.to/codegram/using-components-in-phoenix-templates-5do1>

*最初发表于 [Codegram 的博客](https://www.codegram.com/blog/phoenix-templates-components)。*

作为一名主要使用 Rails 的后端开发人员，**我发现 [Rails 的部分内容](https://guides.rubyonrails.org/layouts_and_rendering.html#using-partials)令人困惑**。别误会，它们很有用。但是他们很难共事。根据我的经验，大多数时候这些部分会接受参数，但是如果不查看部分内容本身，就无法知道它们接受什么参数。这让我很难过。

我通常写类似于前端框架组件的部分。减去逻辑部分，因为我还没有找到在 Rails 视图中包含真正组件的方法。和我要找的最像的是[开拓者的`cells`](http://trailblazer.to/gems/cells/) (真的很喜欢！)，但它们仍然不是我想要的。

我们最近参与了一个项目，该项目基于一个引导模板，在 Phoenix 上构建了一个管理仪表板。这种模板通常在 Bootstrap 之上添加它们自己的类，这些组件需要额外的 HTML 结构，这样一切看起来都像预期的那样。

“哦，这是组件的完美用例！”，我想。

## 组件化凤凰模板

嗯，是的。这是部分的典型用例。但是 Phoenix 不像 Rails 那样明确支持 partials。因为 Phoenix 非常灵活，你可以[写一个`ComponentView`并把你的共享布局转储到那里](https://blog.danielberkompas.com/2017/01/17/reusable-templates-in-phoenix/) :

```
<%= render ComponentView, "tabs.html" do %>
  <%= render ComponentView, "tab.html", name: "All Products" %>
  <%= render ComponentView, "tab.html", name: "Featured" %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

`tabs.html`文件包含 tabs 元素的包装器布局，而`tab.html`包含单个选项卡的布局。

你可以稍微改变一下这个想法，将其简化为:

```
<%= component "tabs.html" do %>
  <%= component "tab.html", name: "All Products" %>
  <%= component "tab.html", name: "Featured" %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个和 Rails 相似的特性，但是因为我们对所有的组件使用了一个单一的视图，所以所有的组件相关的助手都将在那里编写。一起。

[![Concerned darkwraith meme.](img/4579448889698dc57e86d7470aea096f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r3rSZaAN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codegram.com/blog/phoenix-view-components-concerned.jpg)

## 多个视图

在探索组件的替代实现时，我找到了 Kim Lindholm 的 Phoenix 组件文件夹报告。如果您遵循 README 文件中的建议，您将能够拥有多个组件视图，从而可以隔离帮助器方法。这使得我们可以写这样的东西:

```
# lib/my_app_web/components/tabs/tabs_view.ex
defmodule MyAppWeb.Components.TabsView do
  alias MyAppWeb.Components.ComponentHelpers
  use MyAppWeb, {:view, ComponentHelpers.view_opts(:tabs)}
end

# in your template:
<%= component :tabs do %>
  <%= component :tabs, :tab, name: "All Products" %>
  <%= component :tabs, :tab, name: "Featured" %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

这让我们拥有了 Rails 所拥有的相同功能...同样的问题:**一个组件接受什么参数？**

## 使用 helper 方法知道需要的参数

事实上，我看不到，一眼，需要建立一个组件的参数困扰着我。我可以让它保持原样，真的。毕竟，我已经用 Rails 这样开发了很长时间。但是 Elixir 有一个强大的工具包，可以从代码中的文档生成静态文档，我想使用它。

最后，我创建了助手方法。他们中的一些人包装了对`component`方法的调用，其他人直接创建了模板语法:

```
def icon(%{icon_name: name, icon_color: color}) do
  content_tag :span, class: "icon-holder" do
    ComponentHelpers.component(:icon, color: color, icon: name)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以使用[`~E`符号](https://hexdocs.pm/phoenix_html/2.13.3/Phoenix.HTML.html#sigil_E/2)来简化所需的代码:

```
def icon(%{icon_name: name, icon_color: color}) do
  ~E"""
  <span class="icon-holder"> #{ComponentHelpers.component(:icon, color: color, icon: name)} </span>
  """
end 
```

Enter fullscreen mode Exit fullscreen mode

有了它，我可以编写静态文档，我的 IDE 可以捕获它，并给我关于所需参数的提示。

## 结论

这对我来说是一种全新的方法，我可能需要更多的机会来看看它是如何成熟的，所以我还没有最终决定如何在 Phoenix 中编写组件。不过，我喜欢最后一种方法，即使用助手方法包装我的组件以获得良好的文档。

你对这种方法有什么想法？你用过类似的东西吗？我错过了什么吗？