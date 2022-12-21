# RSpec 最佳实践

> 原文：<https://dev.to/bambangsinaga/rspec-best-practice-26k5>

我在日常工作中使用 RSpec。如果你有好的规范覆盖面，这真的很难过分强调它是多么有帮助，你的生活会变得多么容易。但是它杰出的灵活性使得你的说明书变得糟糕:非常慢，过于臃肿，有时甚至不可读。我不想在这里教你 BDD 和 RSpec，而是给你一些如何提高你的规格质量和增加你的 BDD 工作流程效率的想法。

这里有一些关于我们如何进行 [Mekari](https://mekari.com/) 结构测试代码的提示。这些应该帮助你保持你的测试结构良好，易于阅读，理解和分析。

#### 1。形容

明确你所描述的方法。例如，当引用一个类方法的名称时使用 ruby 文档约定`.`,当引用一个实例方法的名称时使用`#`。

```
### Bad example
describe 'the authenticate method for User' do
describe 'if the user is an admin' do

### Good example
describe '.authenticate' do
describe '#admin?' do 
```

#### 2。语境

`context`以“with”或“when”开头，例如“当状态为待定时”

```
describe '#status_badge' do
  context 'returns css class based on status' do
    context 'when status is pending' do
      let(:request_status) { 'pending' }

      it 'returns css for grey badge' do
        expect(subject.status_badge).to eql 'c-badge--grey'
      end
    end

    context 'when status is approved' do
      let(:request_status) { 'approved' }

      it 'returns css for green badge' do
        expect(subject.status_badge).to eql 'c-badge--green'
      end
    end
  end
end 
```

#### 3。它

描述一个测试用例(一个期望)并且只指定一个行为。同一个例子中的多个期望表明你可能指定了多个行为。通过指定只有一个期望，帮助你找到可能的错误，直接进入失败的测试，并使你的代码可读。

无论如何，在没有被隔离的测试中(例如，集成了数据库、外部 web 服务或端到端测试的测试)，你会承受巨大的性能损失，一遍又一遍地做同样的设置，只是为了在每个测试中设置不同的期望。在这种速度较慢的测试中，我认为可以指定多个独立的行为。

```
it { is_expected.to belong_to(:job_title).class_name('JobTitle').optional }
it { is_expected.to belong_to(:template).class_name('Approval::Template') }

# or

it 'has relations' do
  is_expected.to belong_to(:job_title).class_name('JobTitle').optional
  is_expected.to belong_to(:template).class_name('Approval::Template')
end 
```

#### 4。科目

`subject`让你更清楚实际测试的内容，并帮助你在测试中保持干爽

```
describe Book do
  describe '#valid_isbn?' do
    subject { Book.new(isbn: isbn).valid_isbn? }

    context 'with a valid ISBN number' do
      let(:isbn) { 'valid' }

      # ...
    end

    context 'with an invalid ISBN number' do
      let(:isbn) { 'invalid' }

      # ...
    end
  end  
end 
```

你可以用 **RSpec** 做更多**的事情**，你可以在 [betterspecs](http://www.betterspecs.org) 上阅读它们

#### 5。嘲弄的

有趣的是，当我们想要测试的场景需要另一项服务时，我们通常会进行模仿。

你可以模仿任何东西，这样你的规范就不会影响到数据库或其他服务。但是，`this is something wrong`。当您的模型代码改变或者您调用的服务的初始化方法改变时，您的代码将在合并到产品之前中断而不会得到失败的规格。

通过提前嘲笑对象，你可以让自己专注于此刻正在做的事情。假设您正在处理系统的一个新部分，并且您意识到您当前描述和实现的代码将需要两个新的协作对象。使用 mocks，您可以在为当前工作的代码编写规范时定义它们的接口。

这样，在继续实现协作对象之前，通过让所有测试都通过，您可以保持一个干净的环境。如果没有模拟，在测试通过之前，您需要立即开始编写协作对象的实现。这可能会分散注意力，并可能导致糟糕的代码设计决策。嘲讽帮助我们减少了在特定时刻我们需要记在脑子里的东西的数量。

既然你可以模仿，记住不要模仿任何东西来绿化你的天赋或者让你的天赋永远不要出现在数据库里。这是不对的。当你的对象代码改变或者你调用的对象的初始化方法改变时，你的代码会在合并到产品之前中断而不会得到失败的规格。

```
class OvertimeRequest do
  #...
  def allow?
    current_company.active_subscribe?
  end
end

RSpec.describe OvertimeRequest do
  before do
    allow_any_instance_of(Company).to receive(:active_subscribe?).and_return true
  end

  it ... do
  #..
end 
```

#### 6。自定义匹配器

Rspec 有许多有用的匹配器，我们已经使用了`be true`、`be false`等等。有时，当期望给定值时，我们一遍又一遍地重复相同的代码。让我们考虑下面的例子

```
RSpec.feature 'some feature', type: :feature do
  it 'saves data' do
    #..

    expect(page).to have_css('.c-alert--success', text: 'Saved successfully', visible: :all)
  end

  it 'returns errors' do
    #..

    expect(page).to have_css('.c-alert--failed', text: 'Failed to save', visible: :all)
  end
end

# -- another 100 example to check flash message 
```

如果你能写这个就不会更容易了

```
expect(page).to have_flash_message("Saved successfully", type: :success) 
```

如果你在整个应用程序中有一致的逻辑，最好创建这样的自定义匹配器。要创建这样的匹配器，你必须在`spec/support`目录下创建`matchers.rb`文件(根据你的意愿命名)，并在那里放置你的匹配器定义:

```
RSpec::Matchers.define :have_flash_message do |message, opts = {}|
  match do |container|
    css_class = opts[:type].present? ? ".c-alert--#{opts[:type]}" : ".c-alert"

    expect(page).to have_css(
      css_class,
      text: message,
      visible: :all
    )
  end
end 
```

最后一步是通过在`spec_helper.rb`文件中包含`require 'support/matchers'`来要求您的匹配器。

### 资源

一些有帮助的链接

*   [用 rspec 嘲讽:加倍与期待](https://semaphoreci.com/community/tutorials/mocking-with-rspec-doubles-and-expectations)
*   阿比诺达的 rspec 最佳实践
*   [RSpec (Rails)最佳实践](https://medium.com/@somethvictory/rspec-rails-best-practices-c457679e4c04)
*   thoughtbot 的最佳实践