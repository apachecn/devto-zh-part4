# 使用 Elixir 和 Phoenix 创建一个分页器

> 原文：<https://dev.to/ricardoruwer/create-a-paginator-using-elixir-and-phoenix-1hnk>

我写这篇文章的原因和我写创建 sitemap.xml 的原因一样，我只能找到使用 libs 的解决方案，但这很简单，我们不需要 lib 来做这件事。所以我做了这个解决方案，希望对你有帮助。

首先，我们可以创建一个类似于`lib/myapp/paginator.ex` :
的新文件

```
defmodule MyApp.Paginator do
  @moduledoc """
  Paginate your Ecto queries.

  Instead of using: `Repo.all(query)`, you can use: `Paginator.page(query)`.
  To change the page you can pass the page number as the second argument.

  ## Examples

      iex> Paginator.paginate(query, 1)
      [%Item{id: 1}, %Item{id: 2}, %Item{id: 3}, %Item{id: 4}, %Item{id: 5}]

      iex> Paginator.paginate(query, 2)
      [%Item{id: 6}, %Item{id: 7}, %Item{id: 8}, %Item{id: 9}, %Item{id: 10}]

  """

  import Ecto.Query

  alias MyApp.Repo

  @results_per_page 12

  def paginate(query, page) when is_nil(page) do
    paginate(query, 1)
  end

  def paginate(query, page) when is_binary(page) do
    paginate(query, String.to_integer(page))
  end

  def paginate(query, page) do
    results = execute_query(query, page)
    total_results = count_total_results(query)
    total_pages = count_total_pages(total_results)

    %{
      current_page: page,
      results_per_page: @results_per_page,
      total_pages: total_pages,
      total_results: total_results,
      list: results
    }
  end

  defp execute_query(query, page) do
    query
    |> limit(^@results_per_page)
    |> offset((^page - 1) * ^@results_per_page)
    |> Repo.all()
  end

  defp count_total_results(query) do
    Repo.aggregate(query, :count, :id)
  end

  defp count_total_pages(total_results) do
    total_pages = ceil(total_results / @results_per_page)

    if total_pages > 0, do: total_pages, else: 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将创建用于前端的逻辑。所以我们可以创建一个类似于`lib/myapp_web/helpers/paginator_helper.ex` :
的文件

```
defmodule MyAppWeb.Helpers.PaginatorHelper do
  @moduledoc """
  Renders the pagination with a previous button, the pages, and the next button.
  """

  use Phoenix.HTML

  def render(conn, data, class: class) do
    first = prev_button(conn, data)
    pages = page_buttons(conn, data)
    last = next_button(conn, data)

    content_tag(:ul, [first, pages, last], class: class)
  end

  defp prev_button(conn, data) do
    page = data.current_page - 1
    disabled = data.current_page == 1
    params = build_params(conn, page)

    content_tag(:li, disabled: disabled) do
      link to: "?#{params}", rel: "prev" do
        "<"
      end
    end
  end

  defp page_buttons(conn, data) do
    for page <- 1..data.total_pages do
      class = if data.current_page == page, do: "active"
      disabled = data.current_page == page
      params = build_params(conn, page)

      content_tag(:li, class: class, disabled: disabled) do
        link(page, to: "?#{params}")
      end
    end
  end

  defp next_button(conn, data) do
    page = data.current_page + 1
    disabled = data.current_page >= data.total_pages
    params = build_params(conn, page)

    content_tag(:li, disabled: disabled) do
      link to: "?#{params}", rel: "next" do
        ">"
      end
    end
  end

  defp build_params(conn, page) do
    conn.query_params |> Map.put(:page, page) |> URI.encode_query()
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

基本上这就是我们所需要的。

当页面不可用时，它将是`disabled`。我还加了一些这样的 CSS:

```
.paginator-list li[disabled] a {
  opacity: .4;
  pointer-events: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们假设我们有一个方法来列出一个博客中的所有帖子，它会是这样的:

```
alias MyApp.Paginator

def list_paged_posts(params) do
  Paginator.paginate(Post, params["page"])
end 
```

Enter fullscreen mode Exit fullscreen mode

在我们的控制器中，你可以使用这个:

```
def index(conn, params)
  posts = Post.list_paged_posts(params)

  render(conn, "index.html", posts: posts)
end 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 HTML 中你可以使用这个:

```
<%= for post <- @posts.list do %>
  <%= post.title %>
<% end %>

<%= MyApp.Helpers.PaginatorHelper.render(@conn, @posts, class: "paginator-list") %> 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已:)

但是不要忘记测试！因此，我们可以创建文件`test/myapp/paginator_test.exs` :

```
defmodule MyApp.PaginatorTest do
  use MyApp.DataCase

  alias MyApp.Post
  alias MyApp.Paginator

  describe "when the page is nil" do
    test "sets the page to the first page" do
      create_posts(1)

      paginator = Paginator.paginate(Post, nil)

      assert paginator.current_page == 1
    end
  end

  describe "when the page is a string" do
    test "sets the page to an integer" do
      create_posts(1)

      paginator = Paginator.paginate(Post, "1")

      assert paginator.current_page == 1
    end
  end

  test "paginate as 12 results per page" do
    create_posts(15)

    paginator_first_page = Paginator.paginate(Post, 1)
    assert length(paginator_first_page.list) == 12

    paginator_second_page = Paginator.paginate(Post, 2)
    assert length(paginator_second_page.list) == 3
  end

  test "prints pagination info" do
    posts = create_posts(10)

    paginator = Paginator.paginate(Post, 1)

    assert paginator.current_page == 1
    assert paginator.results_per_page == 12
    assert paginator.total_pages == 1
    assert paginator.total_results == 10

    Enum.each(posts, fn post ->
      assert post in paginator.list
    end)
  end

  defp create_posts(quantity) do
    for n <- 1..quantity do
      # Here you create a new Post!
      post_fixture()
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

还有在`test/myapp_web/helpers/paginator_helper_test.exs`中的 paginator_helper 的测试文件，类似这样:

```
defmodule MyAppWeb.Helpers.PaginatorHelperTest do
  use MyAppWeb.ConnCase

  import Phoenix.HTML, only: [safe_to_string: 1]

  alias MyApp.Post
  alias MyAppWeb.Helpers.PaginatorHelper

  describe "render/3" do
    test "renders the paginator" do
      conn = get(build_conn(), "/?q=paginator+elixir")
      paginated_results = Posts.list_paged_posts(%{page: 1})

      paginator =
        conn
        |> PaginatorHelper.render(paginated_results, class: "paginator")
        |> safe_to_string()

      assert paginator ==
               "<ul class=\"paginator\">" <>
                 "<li disabled>" <>
                 "<a href=\"?page=0&amp;q=paginator+elixir\" rel=\"prev\"><</a>" <>
                 "</li>" <>
                 "<li class=\"active\" disabled>" <>
                 "<a href=\"?page=1&amp;q=paginator+elixir\">1</a>" <>
                 "</li>" <>
                 "<li disabled>" <>
                 "<a href=\"?page=2&amp;q=paginator+elixir\" rel=\"next\">></a>" <>
                 "</li>" <>
                 "</ul>"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。希望对你有帮助:)