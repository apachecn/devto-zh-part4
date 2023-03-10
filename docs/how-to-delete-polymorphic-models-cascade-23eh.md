# 如何删除多态模型级联

> 原文：<https://dev.to/jetrockets/how-to-delete-polymorphic-models-cascade-23eh>

如果您在 Rails 应用程序中使用多态模型，如示例

```
class Trade < ActiveRecord::Base
  has_many :gl_entries, as: :source, dependent: :destroy
end

class GlEntry < ActiveRecord::Base
  belongs_to :source, polymorphic: true
end 
```

Enter fullscreen mode Exit fullscreen mode

您将无法添加级联删除记录的常用外键。但是这可以使用数据库来实现。

为此，您需要在数据库中编写一个触发器，为每条记录运行删除功能。

```
CREATE FUNCTION deleteGlEntriesOfTrade()
  RETURNS TRIGGER
  SET SCHEMA 'public'
  LANGUAGE plpgsql 
  AS $$
  BEGIN
    DELETE FROM gl_entries WHERE source_id = OLD.id AND source_type = 'Trade';
    RETURN OLD;   
  END;
  $$;

CREATE TRIGGER deleteTradesGlEntriesTrigger 
  BEFORE DELETE ON trades
  FOR EACH ROW EXECUTE PROCEDURE deleteGlEntriesOfTrade(); 
```

Enter fullscreen mode Exit fullscreen mode

创建迁移并使用:)