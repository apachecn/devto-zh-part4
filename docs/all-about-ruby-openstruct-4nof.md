# 关于 Ruby OpenStruct 的一切

> 原文：<https://dev.to/burdettelamar/all-about-ruby-openstruct-4nof>

## 什么是 OpenStruct？

来自 Ruby 的`OpenStruct`类的文档:

> OpenStruct 是一种数据结构，类似于哈希，允许定义任意属性及其附带值。

它很像一个`Struct`，但是有更多的附加功能:例如，你可以添加和删除属性。

为了演示`OpenStruct`，我将使用 Ruby 和 Ruby 交互 Shell`irb`，从它们的版本开始:

```
p `ruby --version`.chomp
"ruby 2.6.3p62 (2019-04-16 revision 67580) [x64-mingw32]"
p `irb --version`.chomp
"irb 1.0.0 (2018-12-18)" 
```

请继续阅读:

*   [基础知识](#the-basics)
*   [访问属性](#accessing-attributes)
*   [迭代](#iteration)
*   [创建 OpenStruct 对象的更多方法](#more-ways-to-create-an-openstruct-object)
*   [相等](#equality)
*   [更](#more)

## 基础知识

使`OpenStruct`可用(并显示其超类):

```
require 'ostruct'
p OpenStruct.superclass
Object 
```

创建一个`OpenStruct`对象，然后显示它(方法`:to_s`是方法`:inspect`的别名):

```
person = OpenStruct.new(:name => 'Burdette Lamar', :city => 'Houston', :state => 'TX')
puts person.to_s
#<OpenStruct name="Burdette Lamar", city="Houston", state="TX"> 
```

未定义的属性返回 nil，并且不引发异常:

```
p person.country
nil 
```

添加一个属性，通过分配给它:

```
person.country = 'USA'
p person.country
"USA" 
```

删除一个属性(方法`:delete_field`返回删除的值):

```
p person.delete_field(:country)
"USA"
p person
#<OpenStruct name="Burdette Lamar", city="Houston", state="TX"> 
```

将属性的值设置为`nil`不会删除该属性。

```
person.state = nil
p person
#<OpenStruct name="Burdette Lamar", city="Houston", state=nil> 
```

## 访问属性

使用存取方法创建属性，或者使用带参数(符号或字符串)的方法`:[]=`:

```
p person.hair_color = :gray
:gray
p person[:eye_color] = :brown
:brown
p person['hair_style'] = :ponytail
:ponytail 
```

使用其存取方法或带有参数
的方法`:[]`获取属性值

```
p person.hair_color
:gray
p person[:eye_color]
:brown
p person['hair_style']
:ponytail 
```

用一个或多个访问器方法`:[]=`和一个参数
改变属性值

```
p person.hair_color = :silver
:silver
p person[:eye_color] = :dark_brown
:dark_brown
p person['hair_style'] = :tie_back
:tie_back 
```

允许奇怪的名字:

```
p person[:'Weight (pounds)'] = 191
191
p person[:'Weight (pounds)']
191
p person['Weight (pounds)']
191
p person.send(:'Weight (pounds)')
191 
```

以散列形式获取所有属性。

```
p person.to_h
{:name=>"Burdette Lamar", :city=>"Houston", :state=>nil, :hair_color=>:silver, :eye_color=>:dark_brown, :hair_style=>:tie_back, :"Weight (pounds)"=>191} 
```

将所有属性处理成一个散列:

```
p person.to_h {|name, value| [name.to_s, value.to_s] }
{"name"=>"Burdette Lamar", "city"=>"Houston", "state"=>"", "hair_color"=>"silver", "eye_color"=>"dark_brown", "hair_style"=>"tie_back", "Weight (pounds)"=>"191"} 
```

新的`OpenStruct`实例没有方法:

```
person = OpenStruct.new(:name => 'Burdette Lamar', :city => 'Houston', :state => 'TX')
p person.methods(false)
[] 
```

用方法`:[]`访问属性不会创建访问器方法:

```
p person[:name]
"Burdette Lamar"
p person['name']
"Burdette Lamar"
p person.methods(false)
[] 
```

但是用方法`:[]=`访问它确实会创建访问器方法:

```
person[:name] = 'Lamar, Burdette'
p person.methods(false)
[:name, :name=] 
```

用属性的任一访问方法访问属性也是如此:

```
p person.state
"TX"
p person.methods(false)
[:state=, :name=, :name, :state]
person.city = 'Boston'
p person.methods(false)
[:city=, :state=, :name=, :name, :state, :city] 
```

在支持方法`:dig` :
的对象中挖掘内容

```
MyStruct = Struct.new(:foo)
ostruct = OpenStruct.new(
        :bar => MyStruct.new(
              Array.new([
                                Hash.new(
                                      :baz => 'Bag'
                                )
                          ])
        )
  )
p ostruct.dig(:bar, :foo, 0, :baz)
{:baz=>"Bag"} 
```

冻结一个`OpenStruct`对象:

```
ostruct = OpenStruct.new(:a => 0)
p ostruct.frozen?
false
ostruct.freeze
p ostruct.frozen?
true 
```

为元帅库:

```
ostruct = OpenStruct.new(:a => 0, :b => 1)
p ostruct
#<OpenStruct a=0, b=1>
x = ostruct.marshal_dump
p x
{:a=>0, :b=>1}
ostruct.marshal_load(x)
p ostruct
#<OpenStruct a=0, b=1> 
```

## 迭代

用方法`:each_pair` :
迭代

```
person.each_pair do |name, value|
  p [name, value]
end
[:name, "Lamar, Burdette"]
[:city, "Boston"]
[:state, "TX"] 
```

获取属性的枚举数。:

```
enum = person.each_pair
p enum
#<Enumerator: #<OpenStruct name="Lamar, Burdette", city="Boston", state="TX">:each_pair> 
```

## 创建 OpenStruct 对象的更多方法

从一个`Struct` :
创建一个`OpenStruct`

```
Person = Struct.new(:name, :city, :state)
struct = Person.new('Burdette Lamar', 'Houston', 'TX')
ostruct = OpenStruct.new(struct)
p ostruct
#<OpenStruct name="Burdette Lamar", city="Houston", state="TX"> 
```

从另一个`OpenStruct` :
创建一个`OpenStruct`

```
another_ostruct = OpenStruct.new(ostruct)
p another_ostruct
#<OpenStruct name="Burdette Lamar", city="Houston", state="TX"> 
```

创建一个空的`OpenStruct` :

```
ostruct = OpenStruct.new
p ostruct
#<OpenStruct> 
```

## 相等

要比较的几个`OpenStruct`实例:

```
a = OpenStruct.new(:a => 0, :b => 1)
b = OpenStruct.new(:b => 1, :a => 0)
p a
#<OpenStruct a=0, b=1>
p b
#<OpenStruct b=1, a=0> 
```

方法`:==`和`:eql?`测试相等性。

```
p a == b
true
p a.eql?(b)
true 
```

方法`:hash`返回一个整数值，而不是一个散列:

```
p a.hash
664573704
p b.hash
664573704 
```

## 更

*   [文档](https://ruby-doc.org/stdlib-2.6.3/libdoc/ostruct/rdoc/OpenStruct.html)。
*   [源代码](https://github.com/ruby/ruby/blob/master/lib/ostruct.rb)。
*   [相关宝石](https://rubygems.org/search?query=openstruct)。
*   [性能](https://www.google.com/search?q=ruby+openstruct+performance)。