# 将 Rails 中的标签从 ActsAsTaggableOn 迁移到 PostgreSQL 数组

> 原文：<https://dev.to/jetrockets/migrate-tags-in-rails-to-postgresql-array-from-actsastaggableon-1mf8>

如果您需要向 ActiveRecord 模型添加标签，actsastaggaboleon 是一个瑞士军刀解决方案。
只需在你的 gem 文件中添加一个 gem，在模型中添加`acts_as_taggable`就可以得到你需要的一切:添加标签、按标签搜索模型、获取顶级标签等。但是，有时候这些都不需要。

在我们的项目中，我们使用`acts_as_taggable`来存储`Note`模型的标签。然后，我们在几页上显示了带有指定标签的注释列表，并在`Note`表单上自动完成了标签输入。一切都很好，但是由于我们使用 PostgreSQL，我决定在 Note model 中将标签存储为数组。

首先，我在`Note`中添加了`tags Array<String>`列，之后将 acts _ as _ taggable 标签迁移到`notes`表中。

```
class MigrateNoteTags < ActiveRecord::Migration[5.2]
  def change
    execute <<-SQL UPDATE notes 
    SET tags = grouped_taggings.tags_array 
    FROM
      (
      SELECT
        taggings.taggable_id,
        ARRAY_AGG ( tags.NAME ) tags_array 
      FROM
        taggings
        LEFT JOIN tags ON taggings.tag_id = tags.ID 
      WHERE
        taggable_type = 'Note' 
      GROUP BY
        taggings.taggable_id 
      ) AS grouped_taggings 
    WHERE
      notes.ID = grouped_taggings.taggable_id SQL
  end
end 
```

为了具有向后兼容性，我添加了`Note#tag_list`方法:

```
def tag_list
  tags.join(', ')
end 
```

最后是增加了搜索标签的功能。由于 Notes 表中有大约 50 万条记录，我决定创建一个 SQL 视图:

```
CREATE OR REPLACE VIEW note_tags AS

SELECT UNNEST
  ( tags ) AS name,
  COUNT ( * ) AS taggings_count 
FROM
  notes 
GROUP BY
  name 
```

就是这样！在这个视图中搜索标签需要 100 毫秒到 150 毫秒，对我来说没问题。

如果您有更多重要的数据集，那么最好是创建`tags`表并向`notes`表添加触发器，这些触发器将在插入/更新/删除时更新`tags`。