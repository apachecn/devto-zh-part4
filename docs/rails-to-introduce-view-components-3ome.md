# 引入视图组件的 Rails

> 原文：<https://dev.to/andy/rails-to-introduce-view-components-3ome>

当我在等待测试通过我的最新公关，我决定赶上我的收件箱。我订阅了一个 Ruby 时事通讯，看到了这个有趣的标题:*介绍 ActionView::Component: View 组件即将来到 Rails？*链接到 Rails 上的以下 PR:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 引入对第三方组件框架的支持 #36388](https://github.com/rails/rails/pull/36388) 

[![joelhawksley avatar](img/0eab7c961e85763967939c7d210011f4.png)](https://github.com/joelhawksley) **[joelhawksley](https://github.com/joelhawksley)** posted on [<time datetime="2019-06-03T14:16:28Z">Jun 03, 2019</time>](https://github.com/rails/rails/pull/36388)

*注:该公关最初的标题为:`Introduce support for ActionView::Component`。我已经更新了内容，以更好地反映我们最终发布的变化，使用 GitHub 库的新名称`ViewComponent`，并删除了我们不再使用的验证。- @joelhawksley，2020 年 3 月*

# 引入对第三方组件框架的支持

本次 PR 引入了对第三方组件框架的结构支持，包括 [ViewComponent](https://github.com/github/view_component) ，GitHub 用于构建视图组件的框架。

具体来说，它修改了`ActionView::RenderingHelper#render`以支持将一个对象传入到`render`即`responds_to` `render_in`，使我们能够在 Rails 中将视图组件构建为对象。

从三月份开始，我们就在 GitHub 的产品中运行这个补丁的一个变种，现在已经有十几个组件在一百多个呼叫站点中使用。

PR 包括一个示例组件(`TestComponent`)，它非常类似于我们在 GitHub 中使用的基本组件。

我在 RailsConf 谈论了我们的项目，在那里我们从社区获得了很多很好的反馈。几个人要求我们把它上游变成铁路。

## 为什么

在 GitHub 的 Rails monolith(有超过 4000 个模板)中处理视图时，我们遇到了几个关键的棘手问题:

### 检测

目前，Rails 鼓励通过集成或系统测试来测试视图。这阻碍了我们彻底测试我们的视图，因为运行路由/控制器层的开销很大，而不仅仅是视图。这也经常导致部分视图被测试，降低了视图干涸的好处。

### 代码覆盖率

许多常见的 Ruby 代码覆盖工具不能正确地处理视图的覆盖，这使得审计我们的测试有多彻底变得困难，并导致我们的测试套件中的缺口。

### 数据流

与对象上的方法声明不同，视图不声明它们预期接收的值，这使得很难弄清楚呈现它们需要什么样的上下文。当我们在不同的上下文中重用一个视图时，这经常会导致微妙的错误。

### 标准

我们的观点经常达不到我们对 Ruby 类最基本的代码质量标准:长方法、深度条件嵌套和神秘客人比比皆是。

## [t1](#viewcomponent)视图组件

`ViewComponent`致力于通过改进 Rails 视图层来解决这些棘手问题。

### 建筑构件

组件是`ViewComponent`的子类，住在`app/components`里。

它们包括一个与组件具有相同基本名称的边车模板文件。

### 举例

给定组件`app/components/test_component.rb`:

```
class TestComponent < ViewComponent
  def initialize(title:)
    @title = title
  end
end
```

Enter fullscreen mode Exit fullscreen mode

和模板`app/components/test_component.html.erb`:

```
<span title="<%= @title %>"><%= content %></span>
```

Enter fullscreen mode Exit fullscreen mode

我们可以在视图中将其呈现为:

```
<%= render(TestComponent.new(title: "my title")) do %>
  Hello, World!
<% end %>
```

Enter fullscreen mode Exit fullscreen mode

它返回:

```
<span title="my title">Hello, World!</span>
```

Enter fullscreen mode Exit fullscreen mode

## 检测

组件是基于它们的 HTML 输出直接进行单元测试的。测试助手支持使用水豚断言:

```
def test_render_component
  render_inline(TestComponent.new(title: "my title")) { "Hello, World!" }

  assert_text "Hello, World"
  assert_selector "span[title='my title']"
end
```

Enter fullscreen mode Exit fullscreen mode

## 好处

### 检测

`ViewComponent`允许对视图进行单元测试。我们的单元测试运行在大约 25 毫秒/测试，相比之下，集成测试运行在大约 6 秒/测试。

### 代码覆盖率

`ViewComponent`至少部分兼容代码覆盖工具。我们已经看到 SimpleCov 取得了一些成功。

### 数据流

通过明确定义呈现组件所需的上下文，我们发现它们比片段更容易重用。

## 已有实现

远远不是一个新奇的想法。Ruby 中视图组件的流行实现包括但不限于:

*   [开拓者/细胞](https://github.com/trailblazer/cells)
*   [干态 Rb/干态视图](https://github.com/dry-rb/dry-view)
*   [komposable/komponent](https://github.com/komposable/komponent)
*   [activeadmin/arbre](https://github.com/activeadmin/arbre)

## 在行动

我已经创建了一个指向这个分支的演示库。

## 合著-作者

一个由工程师和我们设计系统团队成员组成的跨职能工作组参与了这项工作，包括但不限于:@natashau、@tenderlove、@shawnbot、@emplums、@broccolini、 [@jhawthorn](https://dev.to/jhawthorn) 、@myobie 和@zawaideh。

此外，社区的许多成员也分享了他们对`ViewComponent`的想法，包括但不限于:@cgriego、@xdmx、 [@skyksandr](https://dev.to/skyksandr) 、@jcoyne、@dylanahsmith、[、@kennyadsl](https://dev.to/kennyadsl) 、@cquinones100、@erikaxel、@zachahn 和@trcarden。

[View on GitHub](https://github.com/rails/rails/pull/36388)

我看到的一些有趣的亮点是:

> ## 表现
> 
> 在早期的基准测试中，我们已经看到了现有渲染管道的性能改进。对于嵌套渲染深度为 10 级的测试页面，我们看到速度比 partials 提高了约 5 倍:
> 
> ```
> Comparison:
>            component:     6515.4 i/s
>              partial:     1251.2 i/s - 5.21x  slower 
> ```
> 
> *轨道 6 . 1 . 0α，`joelhawksley/actionview-component-demo`，/基准路线，途经`RAILS_ENV=production rails s`，用`evanphx/benchmark-ips`* 测量
> 
> ## 测试
> 
> 组件允许对视图进行单元测试。我们的单元测试运行在大约 25 毫秒/测试，相比之下，集成测试运行在大约 6 秒/测试。

我以前从未听说过视图组件，至少在 Rails 方面没有。虽然看起来很酷；你有什么想法？