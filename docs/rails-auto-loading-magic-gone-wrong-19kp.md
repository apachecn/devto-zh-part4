# Rails 自动加载魔法出错了！

> 原文：<https://dev.to/shushugah/rails-auto-loading-magic-gone-wrong-19kp>

TL；DR，在继承类时尽可能使用显式的模块命名空间或避免复杂的命名空间。

类和模块常量取决于[自动加载路径查找](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html#autoload-paths)(Ruby on Rails 的一个特性)的顺序，下面将详细介绍。因此，类的可发现性会因继承类的名称和位置而异。当多个类共享相同的名称时，就像我们的 Rails 应用程序中的情况一样，这种微妙之处会影响隐式类继承。

Ruby on Rails 在其`autoload_paths`中有[两个主要算法](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html#resolution-algorithm-for-relative-constants)用于常量查找(默认情况下，应用程序中的所有子目录`app/`和启动时出现的引擎，以及`app/*/concern`)。

可以使用以下命令检查`autoload_paths`的值:

```
$ bin/rails r 'puts ActiveSupport::Dependencies.autoload_paths'
.../app/assets
.../app/controllers
.../app/helpers
.../app/mailers
.../app/models
.../app/resources # This is loaded before models/ 
.../app/controllers/concerns
.../app/models/concerns
.../test/mailers/previews 
```

在我试图清理我们的一些代码时，我无意中创造了一个难以发现的 bug。下面是一些示例代码来看看这个问题的实际应用:

```
# app/models/campaign.rb 
# This class is completely irrelevant to our code but has the same class name as the other campaign class.
class Campaign; end 

# app/resources/homepage/apple.rb
module Resources
  module Homepage
    class Apple < Campaign
      campaign_method!  # This fails because apple.rb is loaded before resources/homepage/campaign.rb 
    end
  end
end

# app/resources/homepage/campaign.rb
module Resources
  module Homepage
    class Campaign
      def self.campaign_method!
        "I should be callable"
      end
    end
  end
end

# app/resources/homepage/yolo.rb
module Resources
  module Homepage
    class Yolo < Resources::Homepage::Campaign
       campaign_method!  # This works because the parent class is unambiguous
    end
  end
end

# app/resources/homepage/zolo.rb
module Resources
  module Homepage
    class Zolo < Campaign
       campaign_method!  # This works by luck because Zolo is loaded alphabetically after resources/homepage/campaign.rb
    end
  end
end 
```

我可以使用`rails console`
来调试这个问题

```
pry(main)> Resources::Homepage::Yolo # returns a constant
=> Resources::Homepage::Yolo
pry(main)> Resources::Homepage::Apple # returns an error message
NameError: undefined local variable or method `campaign_method!' for Resources::Homepage::Apple:Class` 
```

将`class Apple < Campaign`改为`class Apple < Resources::Homepage::Campaign`并重新加载控制台后，代码正确加载。

显式加载所需的文件也是可行的，这就是 Rails AutoLoader(通常)为您正确完成的工作。导轨不鼓励使用`require`或`require_relative`自动加载文件。相反，使用`require_dependency`

```
# resources/homepage/apple.rb
require_dependency 'resources/homepage/campaign' 
module Resources
  module Homepage
    class Apple < Campaign
      campaign_method!  # this works and loads campaign's class method
    end
  end
end 
```

希望 Ruby on Rails 自动加载现在更加清晰了。您可以在这里阅读更多并改进 Ruby on Rails [文档。如果您有任何问题或发现错误，请在此评论或在](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html)[推特](https://twitter.com/shushugah)上联系我！
在 Rails 5 中，生产中默认[禁用自动加载](https://blog.bigbinary.com/2016/08/29/rails-5-disables-autoloading-after-booting-the-app-in-production.html)。