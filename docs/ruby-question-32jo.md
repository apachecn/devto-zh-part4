# Ruby 问题

> 原文：<https://dev.to/burdettelamar/ruby-question-32jo>

Ruby 类`OpenStruct`中方法`:to_h`的[文档](http://ruby-doc.org/stdlib-2.6.3/libdoc/ostruct/rdoc/OpenStruct.html#method-i-to_h)这样写道:

> 将 OpenStruct 转换为具有表示每个属性(作为符号)及其相应值的键的哈希。
> 
> 如果给定一个块，每对接收器上的块的结果将作为对使用。

并且它提供了这个例子:

```
require "ostruct"
data = OpenStruct.new("country" => "Australia", :capital => "Canberra")
data.to_h   # => {:country => "Australia", :capital => "Canberra" }
data.to_h {|name, value| [name.to_s, value.upcase] }
            # => {"country" => "AUSTRALIA", "capital" => "CANBERRA" } 
```

当我在`irb`(版本:irb 0.9.6(09/06/30))中做同样的事情时，我得到这个:

```
require "ostruct"
# => true
data = OpenStruct.new("country" => "Australia", :capital => "Canberra")
# => #<OpenStruct country="Australia", capital="Canberra">
data.to_h
# => {:country=>"Australia", :capital=>"Canberra"}
data.to_h {|name, value| [name.to_s, value.upcase] }
# => {:country=>"Australia", :capital=>"Canberra"} 
```

区别在于:

*   在文档中，最后两个值是不同的。
*   在`irb`会话中，它们是相同的。

所以，我的问题是:这是一个文档错误吗？