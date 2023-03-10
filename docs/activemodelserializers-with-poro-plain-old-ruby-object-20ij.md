# 带 PORO 的 active _ model _ serializers(普通的 Ruby 对象)

> 原文：<https://dev.to/danimal141/activemodelserializers-with-poro-plain-old-ruby-object-20ij>

最近，我参与了一个 Rails API 项目。

在这个项目中，我获取了外部数据，并用这些数据制作了符合[active _ model _ serializer](https://github.com/rails-api/active_model_serializers)的 POROs。
然后我们的 API 返回了序列化的 POROs。

今天我稍微介绍一下开发流程。

## 环境

*   rails 6 . 0 . 0 beta 3
*   活动模型序列化程序 0.10.9
*   工厂机器人 5.0.2

## 目录结构

*   应用程序/型号
    *   劣等石灰石
*   应用程序/序列化程序
    *   序列化程序
*   应用程序/控制器
    *   端点

## 型号

作为一个例子，让我们考虑有一个 URL 和大小信息的`Image`模型。

`active_model_serializers`为像[这种](https://github.com/rails-api/active_model_serializers/tree/0-10-stable#what-does-a-serializable-resource-look-like)的 POROs 提供`ActiveModelSerializers::Model`，太好用了。

如果你需要处理一个更复杂的情况，你将能够实现并使用一个符合这个规范的模型。

```
# app/models/image.rb
class Image < ActiveModelSerializers::Model
  attributes :url, :size
end

# app/models/image/size.rb
class Image::Size < ActiveModelSerializers::Model
  attributes :width, :height
end 
```

Enter fullscreen mode Exit fullscreen mode

## 串行器

```
# app/serializers/image_serializer.rb
class ImageSerializer < ActiveModel::Serializer
  attributes :url, :type
  has_one :size
end

# app/serializers/image/size_serializer.rb
class Image::SizeSerializer < ActiveModel::Serializer
  attributes :width, :height
end 
```

Enter fullscreen mode Exit fullscreen mode

定义类似于`has_one`的关系，我们可以在端点上方便地使用[包含选项](https://github.com/rails-api/active_model_serializers/blob/0-10-stable/docs/general/adapters.md#include-option)。

例如，`render json: image, include: '*'`返回包含`size`的 JSON，`render json: image, include: ''`返回不包含`size`的 JSON。

## 控制器

我们可以在控制器中轻松使用序列化器。我们所要做的就是创建一个模型实例，并将其传递给`render`方法。

然后`active_model_serializers`为给定的实例找到合适的序列化器并序列化，响应返回。

```
# app/controllers/v1/images_controller.rb
module V1
  class ImagesController < ApplicationController
    def show
      render json: image, include: params[:include]
    end

    private

    def image
      @image ||= Image.new(image_attrs)
    end

    def image_attrs
      @image_attrs ||= fetch_data_somehow # Fetch external data
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

当测试`ActiveRecord`模型时，我们可以使用`factory_bot`并使工厂喜欢:

```
FactoryBot.define do
  factory :user do
    name { Faker::Name.name }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
> user = FactoryBot.create(:user) 
```

Enter fullscreen mode Exit fullscreen mode

但是在这种情况下，`FactoryBot#create`不能很好地工作，因为没有存储(此外，也不需要存储它们)。

另外，`ActiveModelSerializers::Model`是基于`ActiveModel`的，所以初始化器需要一个名为`attributes`的散列。

如果我们省略参数`attributes`，`attributes = {}`将作为缺省值给出，那么序列化就不会像预期的那样工作。

```
# spec/factories/images.rb
FactoryBot.define do
  factory :image do
    url { Faker::Internet.url }
    size { build(:image_size) }
  end
end

# spec/factories/image/sizes.rb
FactoryBot.define do
  factory :image_size, class: 'Image::Size' do
    width { rand(100..500) }
    height { rand(100..500) }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
> image = FactoryBot.create(:image)
=> NoMethodError: undefined method `save!`...

> image = FactoryBot.build(:image)
> image.attributes
=> {}

> image.to_json
=> "{}" 
```

Enter fullscreen mode Exit fullscreen mode

`factory_bot`提供 [initialize_with](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#custom-construction) 来覆盖初始化器。

此外，它还提供了 [skip_create](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#custom-methods-to-persist-objects) 来跳过创建。

```
# spec/factories/images.rb
FactoryBot.define do
  factory :image do
    skip_create
    initalize_with { new(attributes) }

    url { Faker::Internet.url }
    size { build(:image_size) }
  end
end

# spec/factories/image/sizes.rb
FactoryBot.define do
  factory :image_size, class: 'Image::Size' do
    skip_create
    initalize_with { new(attributes) }

    width { rand(100..500) }
    height { rand(100..500) }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

用法:

```
> image = FactoryBot.create(:image)
=> #<Image:...> The result is the same as one from `build`🙌

> image = FactoryBot.build(:image)
> image.attributes
=> { "url" => ..., "size" => ... }

> image.to_json
=> "{\"url\":...,\"size\":...}" 
```

Enter fullscreen mode Exit fullscreen mode

为了避免多次编写`initialize_with`和`skip_create`，我最终准备了一个特定的 DSL，比如:

```
if defined?(FactoryBot)
  module FactoryBot
    module Syntax
      module Default
        class DSL
          # Custom DSL for ActiveModelSerializers::Model
          # Original: https://github.com/thoughtbot/factory_bot/blob/v5.0.2/lib/factory_bot/syntax/default.rb#L15-L26
          def serializers_model_factory(name, options = {}, &block)
            factory = Factory.new(name, options)
            proxy = FactoryBot::DefinitionProxy.new(factory.definition)
            if block_given?
              proxy.instance_eval do
                skip_create
                initialize_with { new(attributes) }
                instance_eval(&block)
              end
            end
            FactoryBot.register_factory(factory)

            proxy.child_factories.each do |(child_name, child_options, child_block)|
              parent_factory = child_options.delete(:parent) || name
              serializers_model_factory(child_name, child_options.merge(parent: parent_factory), &child_block)
            end
          end
        end
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

工厂实现结果如下:

```
# spec/factories/images.rb
FactoryBot.define do
  serializers_model_factory :image do
    url { Faker::Internet.url }
    size { build(:image_size) }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以很容易地在规范中使用它，比如:

```
# spec/serializers/image_serializer_spec.rb
require 'rails_helper'

RSpec.describe ImageSerializer, type: :serializer do
  let(:resource) { ActiveModelSerializers::SerializableResource.new(model, options) }
  let(:model) { build(:image) }
  let(:options) { { include: '*' } }

  describe '#url' do
    subject { resource.serializable_hash[:url] }

    it { is_expected.to eq model.url }
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

没有`ActiveRecord`我们也可以轻松使用`active_model_serializers`。

## 参考文献

*   [https://github . com/rails-API/active _ model _ serializer/tree/0-10-stable # what-do-a-serializable-resource-look-like](https://github.com/rails-api/active_model_serializers/tree/0-10-stable#what-does-a-serializable-resource-look-like)
*   [https://github . com/rails-API/active _ model _ serializer/blob/0-10-stable/docs/general/adapters . MD # include-option](https://github.com/rails-api/active_model_serializers/blob/0-10-stable/docs/general/adapters.md#include-option)
*   [https://github . com/thought bot/factory _ bot/blob/master/GETTING _ started . MD](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md)