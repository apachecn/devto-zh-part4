# 在 Elixir 和 Phoenix 中创建动态 sitemap.xml

> 原文：<https://dev.to/ricardoruwer/create-dynamic-sitemap-xml-in-elixir-and-phoenix-3eid>

我为一家建筑公司创建了一个网站，他们可以在那里出售他们的财产，我用 Elixir 和 Phoenix 框架建立了这个网站。

所以我在谷歌上搜索如何实现 sitemap.xml，但我只能找到这个名为`sitemap`的库，所以我试了一下，但是...

在我的网站上，每个创建的属性都有一个单独的页面，在这个 lib 中，每当我创建一个属性时，我都必须运行一个命令来生成一个新的 XML 文件。此外，我不能更改`<lastmod>`,因为它总是使用我生成站点地图的日期。

所以我想创建我自己的动态 sitemap.xml，我会和你分享我是怎么做的，非常简单:)

首先，我在文件`router.ex` :
中创建了我的新路线

```
get "/sitemap.xml", SitemapController, :index 
```

Enter fullscreen mode Exit fullscreen mode

然后我用代码
创建了一个新文件`lib/myapp_web/controllers/sitemap_controller.ex`

```
defmodule MyAppWeb.SitemapController do
  use MyAppWeb, :controller

  plug :put_layout, false

  alias MyApp.Properties

  def index(conn, _params) do
    properties = Properties.list_properties()

    conn
    |> put_resp_content_type("text/xml")
    |> render("index.xml", properties: properties)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

酷:)现在我们必须创建文件`lib/myapp_web/templates/sitemap/index.xml.eex`，这将是我们的网站地图:

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc><%= Routes.home_url(@conn, :index) %></loc>
    <changefreq>weekly</changefreq>
    <priority>0.5</priority>
  </url>
  <url>
    <loc><%= Routes.about_url(@conn, :index) %></loc>
    <changefreq>never</changefreq>
    <priority>0.3</priority>
  </url>
  <url>
    <loc><%= Routes.apartment_url(@conn, :index) %></loc>
    <changefreq>weekly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc><%= Routes.house_url(@conn, :index) %></loc>
    <changefreq>weekly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc><%= Routes.lot_url(@conn, :index) %></loc>
    <changefreq>weekly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc><%= Routes.contact_url(@conn, :index) %></loc>
    <changefreq>never</changefreq>
    <priority>0.5</priority>
  </url>
  <%= for property <- @properties do %>
    <url>
      <loc><%= Routes.property_url(@conn, :show, property.slug) %></loc>
      <lastmod><%= format_date(property.updated_at) %></lastmod>
      <changefreq>weekly</changefreq>
      <priority>1</priority>
    </url>
  <% end %>
</urlset> 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到，我手动添加了我网站上的所有路线:家，关于，公寓，房子，地段和联系方式。然后我为我创建的每个属性添加了所有的动态页面。

我用`updated_at`得到了带有正确日期的`<lastmod>`。但是我们仍然需要创建这个名为 format_date 的函数，所以我们创建了文件`lib/myapp_web/views/sitemap_view.ex` :

```
defmodule MyAppWeb.SitemapView do
  use MyAppWeb, :view

  def format_date(date) do
    date
    |> DateTime.from_naive!("Etc/UTC")
    |> DateTime.to_date()
    |> to_string()
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在你可以在谷歌控制台上添加这个 URL: `yoursite.com/sitemap.xml`，它会很好地工作:)

但是别忘了为此增加一些测试！所以我们可以在文件`test/myapp_web/controllers/sitemap_controller_test.exs` :
上这样做

```
defmodule MyAppWeb.SitemapControllerTest do
  use MyAppWeb.ConnCase

  describe "GET /sitemap.xml" do
    test "accesses the sitemap in format xml", %{conn: conn} do
      property = property_fixture()

      conn = get(conn, "/sitemap.xml")

      assert response_content_type(conn, :xml)
      assert response(conn, 200) =~ ~r/<loc>.*#{property.slug}<\/loc>/
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数`property_fixture()`中，你必须创建一个新的属性。

然后是对我们在`test/myapp_web/views/sitemap_view_test.exs` :
中观点的一个考验

```
defmodule MyAppWeb.SitemapViewTest do
  use MyAppWeb.ConnCase, async: true

  alias MyAppWeb.SitemapView

  test "format_date/1" do
    assert SitemapView.format_date(~N[2019-07-08 13:15:00]) == "2019-07-08"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！:)