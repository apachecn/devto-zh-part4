# 仙丹和凤凰中的 SVG 图标

> 原文：<https://dev.to/ricardoruwer/svg-icons-in-elixir-and-phoenix-1hi4>

以下是我能找到的在 Elixir 和 Phoenix 框架中使用 SVG 图标的最佳方式:

首先，你可以创建一个文件`lib/myapp_web/helpers/icon_helper.ex` :

```
defmodule MyAppWeb.Helpers.IconHelper do
  @moduledoc """
  Give some icons to be used on templates.
  """

  use Phoenix.HTML

  alias MyAppWeb.Router.Helpers, as: Routes

  def icon_tag(conn, name, opts \\ []) do
    classes = Keyword.get(opts, :class, "") <> " icon"

    content_tag(:svg, class: classes) do
      tag(:use, "xlink:href": Routes.static_path(conn,img/icons.svg#" <> name))
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的文件`lib/myapp_web.ex`中，您可以导入这个助手:

```
def view do
  quote do
    # ...

    import MyAppWeb.Helpers.IconHelper

    # ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在文件`assets/statimg/icons.svg` :
中创建我们的图标

```
<?xml version="1.0" encoding="utf-8"?>

  
    <path d="M213.333,0C95.518,0,0,95.514,0,213.333s95.518,213.333,213.333,213.333 c117.828,0,213.333-95.514,213.333-213.333S331.157,0,213.333,0z M174.199,322.918l-93.935-93.931l31.309-31.309l62.626,62.622 l140.894-140.898l31.309,31.309L174.199,322.918z"/>
  

  
    <path d="M21.205,5.007c-0.429-0.444-1.143-0.444-1.587,0c-0.429,0.429-0.429,1.143,0,1.571l8.047,8.047H1.111 C0.492,14.626,0,15.118,0,15.737c0,0.619,0.492,1.127,1.111,1.127h26.554l-8.047,8.032c-0.429,0.444-0.429,1.159,0,1.587 c0.444,0.444,1.159,0.444,1.587,0l9.952-9.952c0.444-0.429,0.444-1.143,0-1.571L21.205,5.007z"/>
  
 
```

Enter fullscreen mode Exit fullscreen mode

我在这里添加了两个图标，分别是`active`和`right-arrow`，但是你可以随意添加。

现在你所有的视图都有了`icon_tag/3`，所以你可以在你的 HTML 中使用:

```
<%= icon_tag(@conn, "active", class: "something") %> 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
<%= icon_tag(@conn, "right-arrow", class: "something") %> 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在你的 CSS 文件中添加类似于:
的东西

```
.icon {
  fill: currentColor;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后 SVG 图标将被你的 CSS 中使用的颜色填充:)
(只要你不在你的`icons.svg`中的`<path>`里面使用一个`fill="color"`)

现在，作为结束，我们可以为`IconHelper`模块创建一个测试文件:

```
defmodule MyAppWeb.Helpers.IconHelperTest do
  use MyAppWeb.ConnCase, async: true

  import Phoenix.HTML, only: [safe_to_string: 1]

  alias MyAppWeb.Helpers.IconHelper

  test "icon_tag/2 renders a svg icon" do
    image =
      MyAppWeb.Endpoint
      |> IconHelper.icon_tag("active", class: "something")
      |> safe_to_string()

    assert image ==
             "" <>
               "<use xlink:href=img/icons.svg#active\">" <>
               ""
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

谢谢[@ andriefn](https://github.com/andrielfn)教我:)