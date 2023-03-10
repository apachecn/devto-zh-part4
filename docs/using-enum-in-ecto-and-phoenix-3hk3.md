# 在 Ecto 和 Phoenix 中使用枚举

> 原文：<https://dev.to/sushant12/using-enum-in-ecto-and-phoenix-3hk3>

注意:

互联网上充斥着没有经验的程序员写的编程文章。这篇文章也是如此，所以不要全信。

=====================================================================

如果你正在读这篇文章，那么你很有可能知道 enum 是什么，以及你为什么想要它。所以，我不会浪费你的时间来解释它们。让我们开门见山吧。

```
defmodule Stipe.Repo.Migrations.AlterDailyUpdate do
  use Ecto.Migration

  def up do
    execute("CREATE TYPE daily_update_status AS ENUM('In Progress', 'In Testing', 'Done');")
    execute("ALTER TABLE daily_updates ALTER COLUMN status DROP DEFAULT;")
    execute("
      ALTER TABLE daily_updates 
              ALTER COLUMN status TYPE daily_update_status 
                USING 
                  CASE status 
                    WHEN NULL then 'In Progress'
                    WHEN 0 then 'In Progress'
                  end :: daily_update_status;
    ")
    execute("
      ALTER TABLE daily_updates
        ALTER COLUMN status SET DEFAULT 'In Progress';  
      ")
  end

  def down do
    execute("ALTER TABLE daily_updates ALTER COLUMN status DROP DEFAULT;")
    execute("
        ALTER TABLE daily_updates
          ALTER COLUMN status TYPE INT 
            USING 
              CASE status
                WHEN NULL then 0
                WHEN 'In Progress' then 0
                WHEN 'In Testing' then 1
                WHEN 'Done' then 2
              end :: integer;
      ")
    execute("
        ALTER TABLE daily_updates
          ALTER COLUMN status SET DEFAULT 0;
      ")
    execute("DROP TYPE IF EXISTS daily_update_status;")
  end
end 
```

这段代码是一个迁移，它改变了表`DailyUpdates`的结构。之前，我已经用 integer 设置了`status`列，但是现在我想使用 postgres enum。

每当我使用`execute`时，我喜欢定义`up`和`down`函数，因为当我们使用`execute`时，迁移是不可逆的。

`up`函数的第一行是创建一个枚举数据类型。然后，它会删除上次迁移中设置的任何默认值。然后将 enum 数据类型分配给`status`列。如果状态列的数据为 0 或 NULL，则它将被映射。否则，迁移将会失败。

`down`功能正在恢复迁移。

现在，我们已经完成了迁移，让我们来看看我们的模式。

```
defmodule Stipe.Standup.DailyUpdate do
  use Ecto.Schema
  import Ecto.Changeset
  alias Stipe.Accounts.User

  schema "daily_updates" do
    field :remarks, :string
    field :started_on, :date
    field :status, :string
    field :task_number, :string
    field :time_spent, :decimal
    belongs_to :user, User
    timestamps()
  end

  def statuses do
    ["In Progress": "In Progress", "In Testing": "In Testing", Done: "Done"]
  end

  @doc false
  def changeset(daily_update, attrs) do
    daily_update
    |> cast(attrs, [:task_number, :status, :time_spent, :started_on, :remarks])
    |> cast_assoc(:user)
    |> assoc_constraint(:user)
    |> validate_required([:task_number, :time_spent, :started_on, :remarks])
  end
end 
```

通常我们将`status`字段定义为字符串类型。我还定义了`statuses`函数，该函数返回我们支持的状态列表。我们可以使用这个函数来填充表单。

为了使用 template 中的 statuses 函数，我们将为 Stipe 起别名。站立。每日更新视图。

```
defmodule StipeWeb.DailyUpdateView do
  use StipeWeb, :view
  alias Stipe.Standup.DailyUpdate
  import Stipe.Utils.Date
end 
```

在我们的模板中，我们可以调用`DailyUpdate.statuses`

```
<%= form_for @changeset, @action, fn f -> %>
  <%= if @changeset.action do %>
    <div class="alert alert-danger">
      <p>Oops, something went wrong! Please check the errors below.</p>
    </div>
  <% end %>

  <%= label f, :task_number %>
  <%= text_input f, :task_number %>
  <%= error_tag f, :task_number %>

  <%= label f, :status %>
  <%= select f, :status, DailyUpdate.statuses %>
  <%= error_tag f, :status %>

  <%= label f, :time_spent %>
  <%= number_input f, :time_spent, step: "any" %>
  <%= error_tag f, :time_spent %>

  <%= label f, :started_on %>
  <%= date_input f, :started_on %>
  <%= error_tag f, :started_on %>

  <%= label f, :remarks %>
  <%= text_input f, :remarks %>
  <%= error_tag f, :remarks %>

  <div>
    <%= submit "Save" %>
  </div>
<% end %> 
```

它将列出支持的状态，并在编辑时选择所选的状态。