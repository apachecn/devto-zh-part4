# 长生不老药和凤凰的多对多关联

> 原文：<https://dev.to/ricardoruwer/many-to-many-associations-in-elixir-and-phoenix-21pm>

我正在创建一家建筑公司的网站，所以我有两个模式:`properties`和`amenities`。一个酒店可以有许多设施(如游泳池、烧烤架、游乐场等)，一个设施可以属于许多酒店。

另一个和这个类似的流行例子是`posts`和`tags`。

所以我的想法是:我可以创建许多便利设施，在属性表单中，我在复选框中列出这些便利设施，以便用户检查该属性有哪些。

我在尝试这样做的时候经历了一些糟糕的时刻，所以这就是为什么我创建了这个帖子，所以我可能会帮助别人...

好了，我创建了这个名为`properties_amenities` :
的模式

```
defmodule MyApp.PropertiesAmenities do
  use Ecto.Schema

  import Ecto.Changeset

  @primary_key false
  schema "properties_amenities" do
    belongs_to :property, MyApp.Properties.Property
    belongs_to :amenity, MyApp.Amenities.Amenity
  end

  def changeset(struct, params \\ %{}) do
    struct
    |> cast(params, [:property_id, :amenity_id])
    |> validate_required([:property_id, :amenity_id])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后我将它添加到`properties`模式中:

```
schema "properties" do
  # ...

  many_to_many(:amenities, MyApp.Amenities.Amenity, join_through: MyApp.PropertiesAmenities)

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

和`amenities`模式:

```
schema "amenities" do
  # ...

  many_to_many(:properties, MyApp.Properties.Property, join_through: MyApp.PropertiesAmenities)

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我创建了一个助手来在属性表单上列出所有的便利设施，所以在文件`lib/myapp_web/helpers/checkbox_helper.ex` :
中

```
defmodule ConstrutoraLcHiertWeb.Helpers.CheckboxHelper do
  use Phoenix.HTML

  @doc """
  Renders multiple checkboxes.

  ## Example

      iex> multiselect_checkboxes(
             f,
             :amenities,
             Enum.map(@amenities, fn c -> { c.name, c.id } end),
             selected: Enum.map(@changeset.data.amenities,&(&1.id))
           )
      <div class="checkbox">
        <label>
          <input name="property[amenities][]" id="property_amenities_1" type="checkbox" value="1" checked>
          <input name="property[amenities][]" id="property_amenities_2" type="checkbox" value="2">
        </label>
      </div
  """
  def multiselect_checkboxes(form, field, options, opts \\ []) do
    {selected, _} = get_selected_values(form, field, opts)
    selected_as_strings = Enum.map(selected, &"#{&1}")

    for {value, key} <- options, into: [] do
      content_tag(:label, class: "checkbox-inline") do
        [
          tag(:input,
            name: input_name(form, field) <> "[]",
            id: input_id(form, field, key),
            type: "checkbox",
            value: key,
            checked: Enum.member?(selected_as_strings, "#{key}")
          ),
          value
        ]
      end
    end
  end

  defp get_selected_values(form, field, opts) do
    {selected, opts} = Keyword.pop(opts, :selected)
    param = field_to_string(field)

    case form do
      %{params: %{^param => sent}} ->
        {sent, opts}

      _ ->
        {selected || input_value(form, field), opts}
    end
  end

  defp field_to_string(field) when is_atom(field), do: Atom.to_string(field)
  defp field_to_string(field) when is_binary(field), do: field
end 
```

Enter fullscreen mode Exit fullscreen mode

并将其添加到`lib/myapp_web.ex` :

```
def view do
  quote do
    # ...

    import MyAppWeb.Helpers.CheckboxHelper

    # ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我将`amenities`返回到属性的形式，所以，在`properties_controller#new`中我添加了:

```
def new(conn, _params) do
  # ...

  amenities = Amenities.list_amenities()

  render("new.html", changeset: changeset, amenities: amenities)
end 
```

Enter fullscreen mode Exit fullscreen mode

并使用新函数来呈现`new.html.eex`文件中的多选复选框:

```
<div class="form-group">
  <%=
    multiselect_checkboxes(
      f,
      :amenities,
      Enum.map(@amenities, fn a -> { a.name, a.id } end),
      selected: Enum.map(@changeset.data.amenities,&(&1.id))
    )
  %>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在视图正在呈现复选框:)

好了，现在我们必须创建一个新的函数来创建属性和设施之间的关联，为此，我在包含所有与属性相关的函数的文件中创建了这个函数:

```
defp maybe_put_amenities(changeset, []), do: changeset

defp maybe_put_amenities(changeset, attrs) do
  amenities = Amenities.get_amenities(attrs["amenities"])

  Ecto.Changeset.put_assoc(changeset, :amenities, amenities)
end 
```

Enter fullscreen mode Exit fullscreen mode

与此功能相关的文件有:

```
def get_amenities(nil), do: []

def get_amenities(ids) do
  Repo.all(from a in MyApp.Amenities.Amenity, where: a.id in ^ids)
end 
```

Enter fullscreen mode Exit fullscreen mode

并在创建新属性的函数中添加了这个新方法`maybe_put_amenities`，就像这样:

```
def create_property(attrs) do
  %Property{}
  |> Property.changeset(attrs)
  |> maybe_put_amenities(attrs)
  |> Repo.insert()
end 
```

Enter fullscreen mode Exit fullscreen mode

所以现在，当访问`properties/new`页面时，它将呈现表单和所有的便利设施。当我们选择便利设施并点击提交按钮时，它将进入`properties_controller#create`并执行代码，如:

```
Properties.create_property(params) 
```

Enter fullscreen mode Exit fullscreen mode

它将执行我们的函数`maybe_put_amenities`，该函数将列出所有使用函数`Amenities.get_amenities/1`和`put_assoc`找到的便利设施。然后保存它。一切都结束了。它工作了。

但是我们仍然可能有一个问题，当我们试图提交表单，但它得到了一个错误。例如，如果用户选择了一些便利设施，点击提交，但它得到了一个错误，我们再次呈现了`new`页面，以前选择的便利设施将不再被选择，所以为了解决这个问题，我们可以在`properties_controller.ex` :
中这样做

```
def create(conn, %{"property" => params}) do
  case Properties.create_property(params) do
    {:ok, property} ->
      conn
      |> put_flash(:info, "Success!")
      |> redirect(to: "/")

    {:error, changeset} ->
      data = Properties.load_amenities(changeset.data)
      amenities = Amenities.list_amenities()

      conn
      |> put_flash(:error, "Error!"))
      |> render("new.html", changeset: %{changeset | data: data}, amenities: amenities)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

并在负责属性的文件中创建这个新方法:

```
def load_amenities(property), do: Repo.preload(property, :amenities) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。看起来有很多步骤，但我希望我能帮助别人:)