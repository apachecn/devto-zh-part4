# ruby 爱好者的生活因为复合操作符而变得更加容易。

> 原文：<https://dev.to/enoch0x5a/rubyists-life-made-easier-with-composition-operators-5513>

如果你写了 Ruby 代码，并且进入了 FP 世界，你可能只是开始在你的类/模块中写那些小方法。编写这样的代码真是太棒了:

```
class Import
  # Some code goes here...

  def find_record(row)
    [ Msa.find_or_initialize_by(region_name: row[:region_name], city: row[:city], state: row[:state], metro: row[:metro] ), row ]
  end

  # record is one of:
  # Object - when record was found
  # false - when record was not found
  def update_record(record, attributes)
    record.attributes = attributes
    record
  end

  # record is one of:
  # false
  # ZipRegion
  def validate_record(record)
    case record
    when false
      [:error, nil]
    else
      validate_record!(record)
    end
  end

  # record is ZipRegion object
  def validate_record!(record)
    if record.valid?
      [:ok, record]
    else
      error(record.id, record.errors.messages)
      [:error, record]
    end
  end

  def persist_record!(validation, record)
    case validation
    when :ok
      record.save
    when :error
      false
    end
  end
end 
```

是的，我知道有 YARD，而且参数类型有些奇怪，但在编写代码时，我被 Gregor Kiczales 的 HTDP 课程迷住了(这非常有趣，真诚地推荐给每一个喜欢冒险的人)。

接下来是可怕的作文:

```
def process(row, index)
    return if header_row?(row)

    success(row[:region_name], persist_record!(*validate_record(update_record(*find_record(parse_row(row))))))
  end 
```

管道很短，但已经很难阅读。幸运的是，在 Ruby 2.6 中，我们现在有两个复合操作符:Proc# > >和它的反向兄弟 Proc#

并且，用一点重构的构图方法就变成了:

```
def process(row, index)
    return if header_row?(row)

    composition = method(:parse_row)        >>
                  method(:find_record)      >>
                  method(:update_record)    >>
                  method(:validate_record)  >>
                  method(:persist_record!)  >>
                  method(:success).curry[row[:region_name]]

    composition.call(row) 
```

很可爱，你不觉得吗？Ruby 离 FP 友好语言家族又近了一步，让我们期待会有更多！