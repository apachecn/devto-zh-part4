# 在 Lucky 中使用自定义 id

> 原文：<https://dev.to/jwoertink/using-custom-ids-in-lucky-4ibh>

如果您正在使用 [Lucky](https://www.luckyframework.org/) ，并且您有一个非默认的模式，那么您可能需要创建一个自定义主键。

我不得不为一个应用程序这样做，在这个应用程序中，我的主键是用 postgres 定义的，比如`character varying(18)`。仅仅做`String`是不够的，因为 Avram 将这些定义为`text`。我是这样做的。

```
# src/models/concerns/custom_id.cr
alias CustomID = String

module Avram::Migrator::Columns::PrimaryKeys
  class CustomIDPrimaryKey < Avram::Migrator::Columns::PrimaryKeys::Base
    def initialize(@name)
    end

    def column_type : String
      "character varying(18)"
    end
  end
end

module Avram::Migrator::Columns
  class CustomIDColumn < Avram::Migrator::Columns::Base
    @default : String? = nil

    def initialize(@name, @nilable, @default)
    end

    def column_type
      "character varying(18)"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后在我的模型中，我现在可以这样做:

```
# src/models/user.cr
class User < BaseModel
  skip_default_columns
  table do
    primary_key id : CustomID
    column created_on : Time
    column updated_on : Time
  end
end 
```

Enter fullscreen mode Exit fullscreen mode