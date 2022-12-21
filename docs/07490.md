# 使用不带 Ruby on Rails 的 factory_bot

> 原文：<https://dev.to/taylorthurlow/using-factorybot-without-ruby-on-rails-nl6>

# 前言

在我从事 Ruby on Rails 项目的过程中，我喜欢使用 Thoughtbot 的 [factory_bot](https://github.com/thoughtbot/factory_bot) 来替代 fixtures 的内置概念。如果你不熟悉，前提很简单。Rails 的 fixtures 很好，但是非常有限，并且倾向于束缚开发人员编写不可维护的测试。一个夹具看起来像这样:

```
class Song < ApplicationRecord
  # has ActiveRecord attributes title, artist, year
  validates :title, :artist, :year, presence: true
end 
```

```
a_cool_song:
  title: All My Friends
  artist: LCD Soundsystem
  year: 2007 
```

很容易看出，在一个复杂的系统中，要么创建一堆不同的装置，要么在每个测试场景中修改它们。对于较小的项目是可行的，但是很快就变得不可行了。来自 factory_bot 的工厂就像增压夹具。我们没有定义一堆设备，而是为我们的`Song`模型定义了一个工厂:

```
FactoryBot.define do
  factory :song do
    title { 'All My Friends' }
    artist { 'LCD Soundsystem' }
    year { 2007 }
  end
end 
```

在我们的测试场景中，创建一个`Song`的实例非常简单，即使属性可能不同:

```
pry(main)> FactoryBot.create(:song)
=> #<Song:0x00007ff42b84b008 @artist="LCD Soundsystem", @title="All My Friends", @year=2007>

pry(main)> FactoryBot.create(:song, title: 'call the police', year: 2017)
=> #<Song:0x00007ff42a321510 @artist="LCD Soundsystem", @title="call the police", @year=2017>
pry(main)> 
```

这只是对 factory_bot 功能的皮毛。更多信息，请查看其[入门文档](https://www.rubydoc.info/gems/factory_bot/file/GETTING_STARTED.md)。

# 修改 factory_bot 以使用普通 Ruby

factory_bot 的编写和维护都非常关注对 Ruby on Rails 的支持——这很好。在那里效果非常好。只需几个小的调整，我们就可以让它很好地与 POROs(普通的老 Ruby 对象)一起工作。在某种意义上，它已经可以工作了，但是还需要做一些事情来使它更容易工作。

## 修复对 ActiveRecord 的中断调用

尝试使用开箱即用的工厂可能如下所示:

```
class Song
  attr_accessor :title, :artist, :year
end

pry(main)> FactoryBot.create(:song)
NoMethodError: undefined method `save!' for #<Song:0x00007f915fae6aa8> 
```

factory_bot 正在尝试调用`ActiveRecord::Persistence#save!`。我们没有使用 ActiveRecord，所以我们需要跳过这一步。为此，我们可以将`skip_create`添加到工厂定义中。

```
FactoryBot.define do
  factory :song do
    skip_create

    title { 'All My Friends' }
    artist { 'LCD Soundsystem' }
    year { 2007 }
  end
end

pry(main)> FactoryBot.create(:song)
=> #<Song:0x00007ff42b84b008 @artist="LCD Soundsystem", @title="All My Friends", @year=2007> 
```

## 固定对象初始化

factory_bot 的动作与它们在 Rails 上下文中的动作是一样的，但是准确理解它创建对象的过程是很重要的。factory_bot 做的第一件事实际上是实例化对象，并运行它的`initialize`方法，如果它存在的话。这有时是一个问题——factory _ bot 不会将任何参数传递到对`Song.new`的调用中，即使它是必需的。考虑这样一个`Song`类:

```
class Song
  attr_accessor :title, :artist, :year

  def initialize(something)
    @title = "A Placeholder Title: #{something}"
  end
end 
```

如果我们照原样使用我们的工厂会发生什么？

```
pry(main)> FactoryBot.create(:song)
ArgumentError: wrong number of arguments (given 0, expected 1)
from test.rb:7:in `initialize' 
```

很明显，factory_bot 只是在调用`Song.new`并收工。不过，我们可以用`initialize_with`来解决这个问题:

```
FactoryBot.define do
  factory :song do
    skip_create

    title { 'All My Friends' }
    artist { 'LCD Soundsystem' }
    year { 2007 }

    initialize_with { new('foo') }
  end
end

pry(main)> FactoryBot.create(:song)
=> #<Song:0x00007ff42b84b008 @artist="LCD Soundsystem", @title="All My Friends", @year=2007> 
```

factory_bot 现在正在以我们想要的方式构造对象，一切正常。您可能会注意到，尽管`initialize`方法将`@title`实例变量设置为某个值，但是`'All My Friends'`的值具有预趋势。这是因为首先对`initialize`方法求值，然后 factory_bot 对实例变量(`@title = 'All My Friends'`等等)进行简单赋值...).这就是为什么我们需要为我们在工厂中定义的所有实例变量包含`attr_accessor`或`attr_writer`。

## 与其他物体的关联

你仍然可以使用 factory_bot 的`association`方法来调用其他工厂，和在 Rails 中一样。只要有一个同名的实例变量，它就能正常工作。

```
class Album
  attr_accessor :title, :artist, :year
end

class Song
  attr_accessor :title, :album
end

FactoryBot.define do
  factory :album do
    skip_create

    title { 'Sound of Silver' }
    artist { 'LCD Soundsystem' }
    year { 2007 }
  end

  factory :song do
    skip_create

    title { 'All My Friends' }
    album { association(:album) }
  end
end 
```

自动创建关联，因为我们没有将它传递给`create`调用:

```
pry(main)> s = FactoryBot.create(:song)
=> #<Song:0x00007f9f612eec28
 @album=#<Album:0x00007f9f639f38f0 @artist="LCD Soundsystem", @title="Sound of Silver", @year=2007>,
 @title="All My Friends">
pry(main)> s.album
=> #<Album:0x00007f9f639f38f0 @artist="LCD Soundsystem", @title="Sound of Silver", @year=2007> 
```

通过将关联作为一个命名参数:
传递到`create`调用中来设置与现有对象的关联

```
pry(main)> a = FactoryBot.create(:album)
=> #<Album:0x00007f9f639a0df8 @artist="LCD Soundsystem", @title="Sound of Silver", @year=2007>
pry(main)> s = FactoryBot.create(:song, album: a)
=> #<Song:0x00007f9f61366f20
 @album=#<Album:0x00007f9f639a0df8 @artist="LCD Soundsystem", @title="Sound of Silver", @year=2007>,
 @title="All My Friends"> 
```

注意，`Album`对象标识符中的内存地址在两种情况下都是`0x00007f9f639a0df8`。这意味着我们知道这样一个事实，我们首先创建的`Album`的同一个实例被分配给了`Song`实例上的`@album`实例变量，正如我们所希望的那样。

就这些了，黑客快乐！