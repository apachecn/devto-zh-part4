# 通过示例提取 Rails 引擎- Vikings 社交媒体

> 原文：<https://dev.to/szaszolak/extracting-rails-engine-by-example-vikings-social-media-4014>

这篇文章将一步一步地描述从一个整体应用程序中提取 Rails 引擎的过程。这是我上一篇文章的后续，在上一篇文章中，我解释了 Rails 引擎作为模块化大型整体应用程序的解决方案的优缺点。这篇文章使用的应用程序的完整源代码可以在[这里](https://gitlab.com/szaszolak/valhalla/merge_requests/2)找到

## 目录

*   [简介](#ch0)
*   [发动机准备](#ch1)
    *   [发动机初始化](#ch1-p1)
    *   [RSpec 初始化](#ch1-p2)
    *   [数据库连接配置](#ch1-p3)
*   [父 app 准备](#ch2)
    *   [请求测试创建](#ch2-p1)
    *   [发动机安装](#ch2-p2)
*   [代码传输](#ch3)
    *   [迁移](#ch3-p1)
    *   [型号](#ch3-p2)
    *   [工厂](#ch3-p3)
    *   [剩余的轨道部件](#ch3-p4)
    *   [非标准导轨组件](#ch3-p5)
    *   [外部依赖性](#ch3-p6)

## 简介

为了设置一个场景，让我们想象我们是维京人社区的伯爵(又名酋长)。作为一个有远见的领导者，我们已经为我们的员工创建了一个简单的社交媒体网站。它允许市民作为战士或农民入伍，并浏览每个职业的列表，为即将到来的袭击寻找同伴或供应商。它也有一个单独的部分，特别电子预言家给出其每日天气预报。这一切都一起工作，但现在我们已经决定是时候改善架构，并可能与其他维京社区共享我们系统的一些部分作为开源。事不宜迟，让我们开始工作，将 seer 模块提取到引擎中。

[![](img/2a6d5f3eea03be60c4dc1f967cfa4403.png)](https://i.giphy.com/media/ddCPqhszsbOxzWceGo/giphy-downsized.gif)

## 发动机准备

在我们可以将任何现有代码从父应用程序移动到引擎之前，我们需要初始化它，设置单元测试框架，并配置开发和测试数据库连接。

### 初始化引擎

要初始化引擎，我们可以使用 Rails generator 和这个命令。

```
 $ rails plugin new seer --mountable --dummy-path=spec/dummy --skip-test --databse=postgresql --skip-git 
```

它接受许多标志，这些标志根据我们的需要调整生成的引擎框架。最重要的是:

*   它指示生成器我们想要一个“可安装的”引擎，并且它应该与主机应用程序的名称空间隔离。这可以防止主机应用程序代码和引擎之间的常量名称冲突。
*   `--dummy-path`，设置 *Rails* 生成的‘虚拟’app 的位置，用于单元测试目的。
*   `--skip-test`，指示生成器不要搭建测试，如果你更喜欢使用另一个测试框架而不是 *Minitest* ，这很有用。对我来说，首选的是 *Rspec* (稍后将配置)。
*   `--database`，配置将要使用的数据库适配器。最好将其设置为与我们想要从中提取引擎的应用程序所使用的相同
*   `--skip-git`，阻止新的 *git* 储存库初始化。我更喜欢在整个过程的最后将引擎从父应用程序的子目录移动到一个单独的存储库时这样做。在此之前，我在主存储库的一个单独的分支上工作，并比较合并(即 pull)请求中的变更。

执行命令后，生成器将请求的引擎放在父应用程序的子目录中。当发电机工作时，我们可以在终端上看到典型的输出。在控制器、模型等标准部件中，我们还可以注意到生成了一些附加文件

```
create  lib/seer.rb
create  lib/seer/engine.rb
create  seer.gemspec
create  lib/seer/version.rb
vendor_app  spec/dummy 
```

这些文件非常有趣，有两个原因，一是它们清楚地表明 engine 是一个专门的 gem，二是它们每个都有一个特殊的角色:

*   `lib/seer.rb`是引擎的入口点，当引擎的主机应用程序加载其依赖项时，将需要该文件。它可用于使引擎外部依赖关系可配置和加载`lib/seer/engine.rb`
*   通知父应用程序这个给定的 gem 是一个引擎。它还可以用于配置引擎参数(类似的`config/application.rb`文件用于标准的 *Rails* 应用程序)。事实上，生成器已经为我们配置了一个参数:`isolate_namespace Seer`，它声明整个引擎被隔离在以引擎命名的名称空间中。
*   包含关于引擎和它所依赖的 gems 的元数据。
*   这个文件声明了引擎的当前版本
*   `spec/dummy`是前面提到的“虚拟”轨道应用程序，将用于发动机的单元测试

### Rspec 初始化

在引擎初始化之后，我们应该配置一个单元测试框架。虽然我们可以使用哪一个没有限制，但是选择同一个是最方便的，因为它在父应用程序中使用。这样，我们可以简单地重用大部分已经存在的单元测试代码。首先，我们需要将开发依赖项添加到`gemspec`文件中。

```
Gem::Specification.new do |spec|  
  ....
  spec.test_files = Dir["spec/**/*"]            

  spec.add_development_dependency 'rspec-rails'
  spec.add_development_dependency 'factory_bot_rails'
  spec.add_development_dependency 'dotenv-rails'
end 
```

然后我们可以继续安装发电机。

```
module Seer
  class Engine < ::Rails::Engine
    isolate_namespace Seer

    config.generators do |generators|
      generators.test_framework :rspec
      generators.fixture_replacement :factory_bot
      generators.factory_bot dir: 'spec/factories'
    end
  end
end 
```

而且至少我们应该安装新的依赖项和 *RSpec*

```
developer@macbook engines-directory $ bundle install developer@macbook engines-directory $ rails g rspec:install
  create  .rspec
  exist  spec
  create  spec/spec_helper.rb
  create  spec/rails_helper.rb 
```

在`rails_helper.rb`文件被创建后，我们必须调整其中的环境路径，指向“虚拟”应用程序。

```
require File.expand_path('../dummy/config/environment.rb', __FILE__) 
```

现在我们已经为下一步做好了准备。

### 数据库连接配置

配置“虚拟”应用程序数据库连接的第一步是调整“虚拟”`db`目录中的`database.yml`文件，因为来自父应用程序的大部分`database.yml`文件都可以重用，除了数据库名称。

```
default: &default
adapter: postgresql
host: <%= ENV.fetch('POSTGRES_HOST') %>
username: <%= ENV.fetch('POSTGRES_USER') %>
password: <%= ENV.fetch('POSTGRES_PASSWORD') %>
port: <%= ENV.fetch('POSTGRES_PORT') %>

development:
  <<: *default
  database: <%= ENV.fetch('POSTGRES_DB') %>_development

test:
  <<: *default
  database: <%= ENV.fetch('POSTGRES_DB') %>_test 
```

我决定将敏感数据存储在 *git* 存储库之外的环境变量中。在这种情况下，我们应该准备一个工具来为我们加载本地环境变量。我选择了 [Dotenv](https://github.com/bkeepers/dotenv) ，所以我在‘虚拟’应用程序目录下创建了一个`.env`文件，并且在虚拟应用程序启动之前需要`dotenv`。

```
#seer/spec/dummy/config/application.rb
Bundler.require(*Rails.groups)
require "seer"

require 'dotenv-rails'

module Dummy
  class Application < Rails::Application
... 
```

至少我们应该执行两个与 Rails 数据库相关的任务。

```
developer@macbook engines-directory $ rails db:setup
developer@macbook engines-directory $ rails db:migrate 
```

如果在“虚拟”应用程序`db`目录中正确执行了所有步骤，应该会创建`schema`文件。

在此之后，我们准备采取下一步行动。
[![](img/8f15787546844a953c4d61304a466c35.png)T3】](https://i.giphy.com/media/3o7TKD9Irma8yw5fSo/giphy.gif)

## 母 app 准备

接下来初始化引擎，我们需要把它插入到父应用程序中，这样我们要做的更改可以在我们进行代码转移时被迭代验证。

### 请求测试创建

在我们提取任何一段代码之前，从最终用户的角度来看，确保整个系统在过程完成后没有改变是非常方便的。为了确保这一点，我更喜欢为提取的模块添加请求测试。

```
require 'rails_helper'

RSpec.describe 'WeatherProphecies', type: :request do
  let(:villager) { create(:villager) }

  before { sign_in villager }

  describe 'GET /weather_prophecies' do
    it 'returns current prophecy' do
      get weather_prophecies_path

      expect(response.body).to include('rain')
    end
  end
end 
```

### 发动机安装

要使用新创建的引擎，我们需要将其包含在父应用程序
的`Gemfile`中

```
source 'https://rubygems.org'
...

# engines
gem 'seer', path: 'seer' 
```

把它安装在路线上。

```
Rails.application.routes.draw do
  ...
  mount Seer::Engine, at: '/seer'  
  ...
end 
```

## 代码转移

有了父应用和引擎，我们可以开始将代码从一个移动到另一个。我更喜欢从数据库开始，继续向上直到控制器。这样，我们就可以减少对外部的依赖。

### 迁徙

最终，引擎和父应用程序将使用相同的数据库。然而，出于测试和开发目的，也为了使引擎真正可安装，甚至可安装到其他应用程序，我们必须将所有与传输的数据库表相关的迁移复制到引擎中。在处理来自父代的迁移时，**我们应该保持迁移名称不变**，这样在安装引擎时，这些迁移就不会重复。

默认情况下，引擎的所有表都以引擎的名称为前缀(在本例中为`seer`),因为在提取之前，当所有现有的迁移都被复制到引擎时，情况并非如此，我们必须添加新的表，这将使用正确的前缀重命名传输的表。

```
class RenameWeatherPropheciesToSeerWeatherProphecies < ActiveRecord::Migration[6.0]
  def change
    rename_table :weather_prophecies, :seer_weather_prophecies
  end
end 
```

要将引擎的迁移复制并应用到父应用程序，我们可以使用以下命令。

```
developer@macbook host-app-directory $ rails seer:install:migrations
NOTE: Migration 20170809130629_create_weather_prophecies.rb from seer has been skipped. Migration with the same name already exists.
developer@macbook host-app-directory $ rails db:migrate 
```

### 车型

为了将模型从父模型转移到引擎中，我们可以简单地运行 bash 命令，或者使用剪切和粘贴方法。

```
developer@macbook host-app-directory $ mv app/models/weather_prophecy.rb seer/app/models/seer/weather_prophecy.rb 
```

我们应该小心地将每个文件放在正确的目录中，这代表了引擎的名称空间(例如: **seer/app/models/seer** )否则，自动加载程序会忽略它们，我们会得到一个错误`NameError: uninitialized constant`。

在具有相应规范的每个模型被转移之后，我们需要将它们的代码包装在引擎的模块中。

```
module Seer
  class WeatherProphecy < ApplicationRecord
  end
end 
```

### 工厂

当模型和它们的规格被移动时，我们缺少的一件事就是工厂。

要使用 [FactoryBot](https://github.com/thoughtbot/factory_bot_rails) ，首先我们需要调整`rails_helper.rb`文件，并指向包含工厂定义的目录

```
require 'factory_bot'
FactoryBot.definition_file_paths = [File.join(File.dirname(__FILE__), 'factories')]
FactoryBot.find_definitions

RSpec.configure do |config|
  config.include FactoryBot::Syntax::Methods
  ...
end 
```

然后我们可以移动工厂定义，就像我们对模型文件所做的那样。

### 其余轨道部件

其余的*轨道*部分，如控制器、作业、通道，可以像我们转移模型一样移动。

至于路由，应该是从父 app 的`routes.rb`文件中剪下来，放在属于引擎的那个里。

### 非标准钢轨组件

在这个场景中，我创建了一个额外的`services`层，这种方法有一个缺点。为了确保 autoloader 也加载代码的这些部分，我们可以做两件事情中的一件，要么我们扩展 autoloader 路径，要么我们将这些附加层放在引擎的 app 目录中。对于这个场景，我选择了后者。

除此之外，我们可以像其他人一样移动它们。

### 外部依赖

最后的收尾工作是使外部依赖关系可配置。
虽然在开发引擎时，我们可以通过使用它们的限定名(前缀为`::`)来引用父应用模块和类。

```
module Seer
  class ApplicationController < ::ApplicationController
  end
end 
```

不幸的是，这在引擎和父应用程序之间引入了大腿耦合。为了解决这个问题，并使我们的引擎更加灵活，我们应该允许其他人在引擎初始化时配置外部依赖关系。

因此，我们应该添加一个模块访问器来存储这样的依赖关系。

```
# lib/seer.rb
module Seer
  mattr_accessor :application_controller_class

  def self.application_controller_class
    @@application_controller_class.constantize
  end
end 
```

为了配置它们，我们需要在父应用程序中创建一个初始化器。

```
# parent_app/config/initializers/seer.rb

Seer.application_controller_class = 'ApplicationController' 
```

在引擎内部，我们可以按以下方式使用它们。

```
 class ApplicationController < Seer.application_controller_class 
```

完成这最后一步后，我们的引擎就可以使用了。我们可以把它作为父母应用程序的子目录，或者把它移到独立的存储库中，在许多应用程序中自由使用。

由于我们刚刚完成了这项具有挑战性的任务，我们的系统更加结构化，更容易导航，测试套件运行更快，更容易在系统的不同部分并行工作，而不用担心合并冲突。我们现在准备在维京盛宴上庆祝我们的荣耀。

[![](img/3d0a6e301d130d66fe49d3d7d2d4a4a2.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--jTDwkOLg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://images.gr-assets.com/hostedimages/1423297125ra/13604922.gif)