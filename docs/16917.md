# 真实世界凤凰|用户角色|显式方式

> 原文：<https://dev.to/kabisasoftware/real-world-phoenix-user-roles-the-explicit-way-21bf>

欢迎回来！在本系列的最后一集中，我们实现了用户认证。现在让我们看看如何通过在系统中实现不同的用户角色来实现用户授权。

我们用了 [Pow！](https://github.com/danschultzer/pow)我们的认证，该库也有一个用户角色系统的实施指南。让我们看看这能让我们走多远，我想更进一步，为这些不同的用户角色实现不同的注册流程。

但是和往常一样，我们不应该操之过急，让它变得太复杂，所以我们将首先实现基本的用户角色。我们将创建一个注册流程，匿名访问者可以简单地选择注册为教师或学生。我已经知道我希望能够在未来定义多个角色，所以我会在创建我要使用的字段时考虑到这一点，但我们不会让事情变得太复杂，所以现在 1 个角色将在注册时设置。

### 迁移和模式

我们需要存储用户角色，因此我们将添加一个迁移来存储用户表中的角色列表:

```
defmodule StudentManager.Repo.Migrations.AddRolesToUsers do
  use Ecto.Migration

  def change do
    alter table(:users) do
      add :roles, {:array, :string}, default: ["student"]
    end
  end
end 
```

在这次迁移中，我利用 Postgres 数组类型在数据库中存储角色列表。当然，也可以将角色存储在不同的表中，并将它们作为关联进行添加，但是现在这样就可以了，我们只需验证用于存储和更新用户角色信息的变更集中允许的角色值。

我们还需要将这个新字段添加到我们的用户模式中，以便能够在我们的应用程序中使用它。在这里，我还添加了用户角色值的验证。下面的方法是这个概念的一个天真的实现，因为这是一个潜在的非常危险的在变更集中添加角色的方法。我将在本文稍后解释何时实现特定的用户角色变更集。

```
defmodule StudentManager.Accounts.User do
  use Ecto.Schema
  use Pow.Ecto.Schema

  schema "users" do
    field(:roles, {:array, :string}, default: ["student"])

    pow_user_fields()

    timestamps()
  end

  def changeset_role(user_or_changeset, attrs) do
    user_or_changeset
    |> Changeset.cast(attrs, [:roles])
    |> Changeset.validate_inclusion(:roles, ~w(student teacher))
  end
end 
```

### 测试

在第一篇博文中，我根本没有提到测试，但它是构建一个可靠的应用程序的一个非常重要的部分，这个应用程序易于重构，并且可以放心地更改。我不想深入讨论关于一个适当的、成熟的测试设置的太多细节(我将在以后的一篇单独的文章中介绍)，我想测试一下这个用户角色添加，只是为了检查上面的内容是否成立，我们是否真的能够向用户添加这些角色。
现在，当我在第一篇文章中创建和设置这个应用程序时，我简单地提到了我将把用户添加到帐户上下文中。现在，我们还没有设置任何上下文，所以让我们在创建用户之后利用 Phoenix generators 来设置它。我相信 Dan 已经处理了基本用户级字段和变更集的基本测试，所以我们主要关心的是测试我们的定制。测试 API 层(即上下文层)是测试实际行为的一个非常好的方法。但是，当我们已经定义了 contet 的一部分时，我们真的可以运行生成器吗？让我们来看看:

```
mix phx.gen.context Accounts User users

# outputs:

The following files conflict with new files to be generated:

  * lib/student_manager/accounts/user.ex

See the --web option to namespace similarly named resources

Proceed with interactive overwrite? [Yn] 
* creating lib/student_manager/accounts/user.ex
lib/student_manager/accounts/user.ex already exists, overwrite? [Yn] n
* creating priv/repo/migrations/20190616212819_create_users.exs
* creating lib/student_manager/accounts.ex
* injecting lib/student_manager/accounts.ex
* creating test/student_manager/accounts_test.exs
* injecting test/student_manager/accounts_test.exs

Remember to update your repository by running migrations:

    $ mix ecto.migrate 
```

这真的很棒。一旦我们进入`interactive overwrite`，它会问我们是否要替换任何现有的文件。在我们的例子中，那只是一个`user.ex`文件，我们不想覆盖它！您还可以看到，它创建了一个新的迁移，但由于我们已经有了这个迁移，我们可以简单地删除该文件。

现在这给了我们一个开始测试我们的`accounts`上下文的起点。我们实际上将删除一些样板测试，因为我们现在不需要它们，并保留我们现在想要测试的子集。这是测试剩下的内容:

```
defmodule StudentManager.AccountsTest do
  use StudentManager.DataCase

  alias StudentManager.Accounts

  describe "users" do
    alias StudentManager.Accounts.User

    @valid_attrs %{email: "email@example.com", password: "supersecretpassword", confirm_password: "supersecretpassword"}
    @invalid_attrs %{email: "bademail@bad", password: "short", confirm_password: "shot" }

    def user_fixture(attrs \\ %{}) do
      {:ok, user} =
        attrs
        |> Enum.into(@valid_attrs)
        |> Accounts.create_user()

      user
    end

    test "create_user/1 with valid data creates a user" do
      assert {:ok, %User{} = user} = Accounts.create_user(@valid_attrs)
    end

    test "create_user/1 with invalid data returns error changeset" do
      assert {:error, %Ecto.Changeset{}} = Accounts.create_user(@invalid_attrs)
    end

    test "create_user/1 creates a user with a default role of student" do
      {:ok, user} = Accounts.create_user(@valid_attrs)
      assert user.roles == ["student"]
    end

    test "create_user/1 validates a correct role" do
      assert {:error, %Ecto.Changeset{}} = Accounts.create_user(Map.put(@valid_attrs, :roles, ["pilot", "teacher"]))
    end
  end
end 
```

现在，我们可以继续添加 ExMachina(如果您来自 Ruby，那么它相当于 FactoryBot 的灵丹妙药...)，但是现在，让我们不要:)所以我们现在有能力保存附加到用户的用户角色。太好了！我还想做两件事，所以请再忍耐一会儿。

### 自定义注册

当访问这个应用程序时，应该很容易让潜在的学生和教师注册，注册过程应该专注于他们的角色。这意味着几件事。首先，我们应该为他们设定角色，因为你不希望他们自己进入角色。其次，学生注册和老师注册相比，我们想要收集的信息是非常不同的，对吗？对于老师来说，我可能想知道他们教什么乐器，他们是否有现有的学生(可能在注册时导入)，他们是否有教学的地点等等。这可能会变得非常广泛，绝对不是您希望学生使用的注册流程。对于学生来说，这应该很简单。你叫什么名字，电子邮件，你有老师吗？或者你正在附近找老师吗？举个例子。

因此，在没有实现注册过程的所有细节的情况下。让我们至少创建两种注册方式。我在这里要介绍的主要内容是使用特定变更集实现这一目的的方法。这个例子应该非常清楚我在这里的意思:

```
def teacher_registration_changeset(user_or_changeset, attrs) do
  user_or_changeset
  |> changeset(attrs)
  |> change(%{roles: ["teacher"]})
end

def student_registration_changeset(user_or_changeset, attrs) do
  user_or_changeset
  |> changeset(attrs)
  |> change(%{roles: ["student"]})
end 
```

还记得我们上面添加用户角色的第一个实现吗:

```
def changeset_role(user_or_changeset, attrs) do
  user_or_changeset
  |> Changeset.cast(attrs, [:roles])
  |> Changeset.validate_inclusion(:roles, ~w(student teacher))
end 
```

您会注意到，我没有使用这个变更集，而是显式地使用了`change/2`方法，并通过我们正在创建的用户来设置用户角色。天真的实现带来的危险是，用户可能有机会设置自己的用户角色。我们希望在应用程序中明确控制这个值。

现在，在不涉及前端的情况下，让我们在我们的帐户上下文中测试和创建这两个 api 端点:

所以对于测试来说，应该是这样的:

```
 test "create_teacher/1 creates a user with the teacher role set" do
      {:ok, user} = Accounts.create_teacher(@valid_attrs)
      assert Enum.member?(user.roles, "teacher")
      refute Enum.member?(user.roles, "student")
    end

    test "create_student/1 creates a user with the student role set" do
      {:ok, user} = Accounts.create_student(@valid_attrs)
      assert Enum.member?(user.roles, "student")
      refute Enum.member?(user.roles, "teacher")
    end 
```

为此，我们需要在 accounts 上下文中创建这两个函数:

```
 @doc """
  Creates a teacher.

  ## Examples

      iex> create_teacher(%{field: value})
      {:ok, %User{}}

      iex> create_teacher(%{field: bad_value})
      {:error, %Ecto.Changeset{}}

  """
  def create_teacher(attrs \\ %{}) do
    %User{}
    |> User.teacher_registration_changeset(attrs)
    |> Repo.insert()
  end

  @doc """
  Creates a student.

  ## Examples

      iex> create_student(%{field: value})
      {:ok, %User{}}

      iex> create_student(%{field: bad_value})
      {:error, %Ecto.Changeset{}}

  """
  def create_student(attrs \\ %{}) do
    %User{}
    |> User.student_registration_changeset(attrs)
    |> Repo.insert()
  end 
```

所有测试都通过了！我知道你在想什么。为什么只根据角色复制这些功能！但是，是的，我喜欢现在的明确性，它可能会在内容方面分歧更大。所以我会保持原样。测试也非常好，简洁和快速！太好了！

### 灿灿还是可以的！

好吧，你还在这里...太好了！在我离开你之前还有一件事。在任何应用程序中引入角色的整个想法是，你想要限制某些用户做他们不应该做的事情。所以叫`authorization`！因此，在这种情况下，大多数人开始谷歌或搜索 hex.pm 寻找现成的解决方案，他们会发现确实有很多这样的解决方案。但是让我们后退一步，阅读我们已经添加的用于认证的`pow`库中的优秀指南([见上一篇文章](https://www.theguild.nl/real-world-phoenix-lets-auth-some-users/))，再想一想，并意识到 Plug 实际上是我们自己实现该功能所需要的一切，没有添加任何依赖！我知道在仙丹王国有一种趋势，让库维护者在我们的工具箱中获得更多的工具，但有时这些工具不在那里的原因只是因为我们不需要这些依赖，因为添加我们自己是如此简单。在我看来，最大的好处是你完全理解代码在做什么，因为这些都是你自己写的！我觉得是双赢！

实现一个插件非常简单，如果你想阅读这方面的内容，请务必访问 elixirschool 上的这篇[优秀教程！现在我们将使用`pow`的维护者丹在本](https://elixirschool.com/en/lessons/specifics/plug/)[指南](https://hexdocs.pm/pow/user_roles.html#content)中提供的建议实现，暂时就这样吧。当我们发现应用程序中需要更多自定义控件的地方时，我们肯定会扩展和完善它，但现在我认为我已经耗尽了你一天的精神力量，并带着这个插件离开:

```
defmodule StudentManagerWeb.Plugs.AuthorizationPlug do
  @moduledoc """
  This plug ensures that a user has a particular role.

  ## Example

      plug StudentManagerWeb.Plugs.AuthorizationPlug, [:student, :teacher]

      plug StudentManagerWeb.Plugs.AuthorizationPlug, :teacher

      plug StudentManagerWeb.Plugs.AuthorizationPlug, ~w(student teacher)a
  """
  alias StudentManagerWeb.Router.Helpers, as: Routes
  alias Phoenix.Controller
  alias Plug.Conn
  alias Pow.Plug

  @doc false
  @spec init(any()) :: any()
  def init(config), do: config

  @doc false
  @spec call(Conn.t(), atom()) :: Conn.t()
  def call(conn, roles) do
    conn
    |> Plug.current_user()
    |> has_role?(roles)
    |> maybe_halt(conn)
  end

  defp has_role?(nil, _roles), do: false
  defp has_role?(user, roles) when is_list(roles), do: Enum.any?(roles, &has_role?(user, &1))
  defp has_role?(user, role) when is_atom(role), do: has_role?(user, Atom.to_string(role))
  defp has_role?(%{role: role}, role), do: true
  defp has_role?(_user, _role), do: false

  defp maybe_halt(true, conn), do: conn
  defp maybe_halt(_any, conn) do
    conn
    |> Controller.put_flash(:error, "Unauthorized access")
    |> Controller.redirect(to: Routes.page_path(conn, :index))
  end
end 
```

现在我相信这应该是足够的信息了。下次我们将继续这些注册范围，看看我们如何利用所有这些来轻松管理我们的注册过程！

谢谢！