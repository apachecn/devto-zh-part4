# 高级捆扎机

> 原文：<https://dev.to/pnomolos/advanced-bundler-4fbe>

下面将是一篇(希望经常)更新的文章，提供帮助充分利用 Bundler 的技巧。

### 干式多平台 gemspecs (2019-06-14)

*   注意加载通用规范后的`.dup`调用。这很重要，因为 Bundler 实际上加载了所有(`*.gemspec`)文件，Rubygems 缓存了`eval`调用`Gem::Specification.load`的结果。没有了`.dup`,这意味着您将不得不为您想要的每个定制 gemspec 修改相同的 spec 实例。

##### gemname.gemspec.common

```
Gem::Specification.new do |gem|
  gem.authors     = ["Phil Schalm"]
  gem.email       = ["notmyrealemail@nowhere.com"]
  gem.description = "Sample"
  gem.summary     = "Longer sample"
  gem.files       = %w[gemname.gemspec gemname-java.gemspec gemname.gemspec.common Gemfile]

  # Common dependencies here
end 
```

##### gemname.gemspec

```
Gem::Specification.load(File.expand_path("gemname.gemspec.common", __dir__)).dup.tap do |gem|
  gem.instance_exec do
    self.name = "gemname"
    # 'Everything else' specific dependencies here
  end
end 
```

##### gemname-java.gemspec

```
Gem::Specification.load(File.expand_path("gemname.gemspec.common", __dir__)).dup.tap do |gem|
  gem.instance_exec do
    self.name = "gemname-java"
    # Java specific dependencies here
  end
end 
```

##### Gemfile

```
source 'https://rubygems.org'

gemspec name: RUBY_PLATFORM == "java" ? "gemname-java" : "gemname" 
```