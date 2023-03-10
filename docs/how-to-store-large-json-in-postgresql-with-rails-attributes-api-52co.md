# 如何用 Rails 属性 API 在 PostgreSQL 中存储大型 JSON

> 原文：<https://dev.to/jetrockets/how-to-store-large-json-in-postgresql-with-rails-attributes-api-52co>

如果在数据库中存储大型对象(比如 JSON)，例如大型报表的数据，那么这会占用大量空间。为了减小数据的大小，可以压缩并以二进制形式存储。

PostgreSQL 有一个 [bytea 字段类型](https://www.postgresql.org/docs/9.0/datatype-binary.html)用于存储这样的数据。您可以使用迁移
在 Rails 中添加 bytea 列

```
add_column :reports, :data, :binary 
```

Enter fullscreen mode Exit fullscreen mode

对于二进制字段操作，您可以使用 [Rails 属性 API](https://api.rubyonrails.org/classes/ActiveRecord/Attributes/ClassMethods.html) 并添加一个新的`BinaryHash`数据类型

```
# app/types/binary_hash.rb

class BinaryHash < ActiveRecord::Type::Binary
  def serialize(value)
    super value_to_binary(value.to_json)
  end

  def deserialize(value)
    super case value
          when NilClass
            {}
          when ActiveModel::Type::Binary::Data
            value_to_hash(value.to_s)
          else
            value_to_hash(PG::Connection.unescape_bytea(value))
          end
  end

  private

  def value_to_hash(value)
    JSON.parse(
      ActiveSupport::Gzip.decompress(value),
      symbolize_names: true
    ) || {}
  end

  def value_to_binary(value)
    ActiveSupport::Gzip.compress(value)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在初始化器中注册新类型

```
# config/initializers/types.rb

ActiveRecord::Type.register(:binary_hash, BinaryHash) 
```

Enter fullscreen mode Exit fullscreen mode

并添加到模型
中的二进制类型属性

```
# app/models/snapshot.rb

class Reports < ApplicationRecord
  attribute :data, :binary_hash
end 
```

Enter fullscreen mode Exit fullscreen mode

测试表明，数据大小减少了近 3 倍

```
Run time with 100000 width JSON
                           user     system      total        real
Compress JSON          0.008671   0.001535   0.010206 (  0.010885)
Decompress JSON        0.001357   0.000095   0.001452 (  0.001509)

json size       95450 bytes
binary size   33868 bytes
~ 2.82 times compression 
```

Enter fullscreen mode Exit fullscreen mode