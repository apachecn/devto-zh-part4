# Ruby 双斜线(**)运算符备忘单

> 原文：<https://dev.to/ldrner/ruby-double-splat-operator-cheatsheet-5c8n>

操作符`**`作为选项散列很有用。

```
def one_method(**options);end 
```

这种形式完全类似于下面:

```
def another_method(options = {});end 
```

此外，您可以严格定义该方法所需的键集。

```
def one_strict_method(first_name:, last_name: , **options)
  puts "options: #{options}"
  greeting = "Hello #{first_name}  #{last_name}"
  puts options[:upcase] ? greeting.upcase : greeting
end 
```

```
pry(main)>  one_strict_method(upcase: true)
ArgumentError: missing keywords: first_name, last_name   pry(main)>  one_strict_method(first_name: 'John', last_name: 'Doe', upcase: true)
options: {:upcase=>true}
HELLO JOHN DOE => nil 
```

double splat 文字的另一个优点是，它的工作方式类似于#merge for [Ruby Hash](https://ruby-doc.org/core-2.5.1/Hash.html#method-i-merge)

```
class Contact::ShowRepresenter #:nodoc:
  def call(contact)
    {
      contact: {
        **base_info(contact),
        **legal_info(contact),
        # You can add something more complex here.
        # **GeoLocaionRepresenter.new.(contact)
      }
    }
  end

  private

  def base_info(contact)
    {
      id: contact.id,
      first_name: contact.first_name,
      last_name: contact.last_name,
      email: contact.email,
      phone: contact.phone
    }
  end

  def legal_info(contact)
    {
      legal_name: contact.legal_name,
      legal_type: contact.legal_type,
      mailing_address: contact.mailing_address
    }
  end
end 
```

```
pry(main)>Contact::ShowRepresenter.new.(Contact.last)
=> {
  :contact=>{
    :id=>51986,
    :first_name=>"Ilia",
    :last_name=>"Kriachkov",
    :email=>"ilia.kriachkov@jetrockets.ru",
    :phone=>"+79000000000",
    :legal_name=>"JetRockets",
    :legal_type=>"LLC",
    :mailing_address=>"15 Let Oktyabrya Street, #10b, Tver, Russian Federation 170008"
  }
} 
```

最后，我想展示一些基准测试结果。

如您所见，`**`操作符比 Hash#merge 要快一点。

```
require 'benchmark'
n = 50_000
Benchmark.bm(2) do |x|
  x.report('merge:             ') { n.times { merge } }
  x.report('double_splat_merge:') { n.times { double_splat_merge } }
end

def merge
  hash = { a: 'a' }
  { b: 'b' }.merge(hash)
end

def double_splat_merge
  hash = { a: 'a' }
  { b: 'b', **hash }
end 
```

```
 user      system      total        real
merge:               0.109247   0.088652   0.197899 (  0.204470)
double_splat_merge:  0.079480   0.003590   0.083070 (  0.083642) 
```