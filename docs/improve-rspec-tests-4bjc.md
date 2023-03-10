# 提高 Rspec 管道性能

> 原文：<https://dev.to/bambangsinaga/improve-rspec-tests-4bjc>

在 [Mekari](//mekari.com) ，我们高度鼓励开发人员在每个项目上编写测试。测试可以防止产品中的错误，强制更好的代码，并在对现有代码库进行更改时增强开发人员的信心。我们使用 rspec、selenium 和 capybara 作为我们的标准单元测试和集成测试工具。

### Rspec 最佳实践

如果您是 ruby on rails 的新手，并且以前从未使用过 rspec，这里有一些链接，对您的入门没有什么指导。

*   [RSpec (Rails)最佳实践](https://medium.com/@somethvictory/rspec-rails-best-practices-c457679e4c04)
*   thoughtbot 的最佳实践
*   [我们的最佳实践](https://dev.to/bambangsinaga/rspec-best-practice-26k5)

### 使用大型代码库

在大型代码库中，测试变得更加重要。虽然有些人认为测试会减慢开发速度，但我们认为随着时间的推移，测试会加快开发速度，尤其是在重构现有代码和消除技术债务的时候。

测试驱动开发(TDD) 鼓励经常运行你的测试。在运行完整的测试套件时，您可能会在大型代码库中发现，运行 2500 多个测试可能需要 25 分钟以上。这将使您在推动变更并等待测试套件完成时效率低下。

在这篇文章中，我将分享我们如何加速我们的管道测试。对于我的项目，我们使用专用的 [CI(持续集成)](https://en.wikipedia.org/wiki/Continuous_integration)服务器 bitbucket 来运行完整的测试套件。每隔几分钟，开发人员就会对分支进行更改，提交更改，并将更改推送到 bitbucket。然后，创建关于 ticket 和 bitbucket 的 PR (Pull Request ),将这些更改拉下来，并运行整个测试套件。

#### 增加硬件能力

当我们想在不做太多事情的情况下获得性能时，我们的第一选择是提高硬件规格。通过将您的 CI 服务器从旧的或低规格的机器升级到更快的机器，您可以很容易地在您的测试套件上看到双倍的非并行性能。

#### 平行测试

跨多个 CPU 内核运行规格可以进一步减少套件的测试时间。为此，我们使用了 [parallel_tests](https://github.com/grosser/parallel_tests) gem。它每个线程使用一个数据库，可以运行在 CI 服务器上，比如 bitbucket。并行运行测试套件还使得硬件升级更加有效，可以扩展到拥有更多内核的机器。单核性能不再是您的瓶颈。

当并行化您的套件时，要小心位于数据库之外的测试之间的共享状态，例如 [Elasticsearch](https://www.elastic.co/) 。这些相互依赖的问题也需要同时解决。

这个解决方案解决了你测试套件运行缓慢的问题，但是这是有代价的。

#### 剖析规格

当你已经实现了上面的解决方案，但是你仍然觉得你的测试套件很慢。然后，您应该尝试考虑的是您的数据库策略设置和测试套件。

##### 数据库清理器配置

如果您编写与数据库交互的 ruby 或 rails 代码并编写单元测试，那么您很可能听说过或使用过 [database_cleaner](https://github.com/bmabey/database_cleaner) gem。这是一个极好的例子，它抽象出了各种 ORM APIs，使数据库进入“空白”状态。一些设置需要在最佳模式下使用它。这里有一些文章可以帮助您建立自己网站:

*   [用 Rails、Rspec、Capybara 和 Selenium 配置 database _ cleaner】](http://www.virtuouscode.com/2012/08/31/configuring-database_cleaner-with-rails-rspec-capybara-and-selenium/)
*   [使用 Rspec、FactoryGirl、Faker 和 Database cleaner 进行测试](https://medium.com/brief-stops/testing-with-rspec-factorygirl-faker-and-database-cleaner-651c71ca0688)

##### 工厂级联

假设您有`User`模型，每个用户有一个`Contact`和一个`Location`记录。你的工厂应该看起来像这样:

```
factory :user do
  contact
  location
end 
```

现在，试着猜测一旦调用`create(:user)`，数据库中会创建多少条记录？

每次调用`create(:user)`，就会创建 2 个额外的记录。**是的，甚至还有同建的**。现在假设您在所有测试中使用这个基础工厂。有两种方法可以解决这个问题。

*   **显式关联**首先想到的是从我们的工厂中移除所有(或几乎所有)关联:

```
factory :user do
  # do not declare associations
  # contact
  # location
end 
```

使用这种方法，当使用工厂
时，您必须明确指定所有必需的关联

```
create(:user, contact: contact, location: location)

# But!!
create(:user) # => raises ActiveRecord::InvalidRecord 
```

*   你的基础工厂应该总是只有这些需要通过验证的属性。如果你有时需要联想，那么考虑使用[特质](https://thoughtbot.com/blog/remove-duplication-with-factorygirls-traits)

```
factory :user do
  name { "Zuned #{Faker::Name.unique.name}" }
  username { "#{ name.parameterize.underscore}_#{ SecureRandom.hex(3) }" }
  email { "#{ username + Random.rand(1000).to_s }@example.com"}

  trait :with_location do
    location
  end
end 
```

您可以使用
给工厂打电话

```
create :user, :with_location 
```

##### 随遇而安

我们的规格总是与所需的公司和 2 个或更多的员工有关系(与用户、职位、位置和工作级别相关联)。想象一下，如果我们为每个规格调用`create :company`等等。`1 spec`需要`1 company + 2 employee + 2 locations + 2 job_title + 2 job_level`，表示 9 条记录仅用于初始数据。我们可以为每个规格只生成一次这 9 个记录吗？

是的，任何设备都来了。内置`fixture`与`anyfixture`不同。两者代表不同方法。`Fixture`声明一个数据的静态状态，它被立即加载到一个测试数据库中，并且通常在两次测试运行之间保持不变。但是使用`anyfixture`，我们能够通过 refind 方法硬重载记录以获得记录的新副本。这里是我们用(any fixture)[[https://test-prof.evilmartians.io/#/any_fixture](https://test-prof.evilmartians.io/#/any_fixture)]实现的
。

```
require 'test_prof/any_fixture/dsl'
require 'test_prof/ext/active_record_refind'

using TestProf::AnyFixture::DSL
using TestProf::Ext::ActiveRecordRefind

# Fixtures are kept to be outside db transaction such that it can be leveraged
# for specs. https://github.com/palkan/test-prof/blob/master/docs/any_fixture.md
RSpec.shared_context 'company with staffs' do
  before(:all) do
    # register to fixture
    fixture(:company) { FactoryBot.create(:company) }
    fixture(:location) { FactoryBot.create(:location, company: fixture(:company)) }
    fixture(:job_title_ceo) {
      FactoryBot.create(:job_title, :ceo, company: fixture(:company))
    }

    fixture(:job_title_hr) {
      FactoryBot.create(
        :job_title,
        title: 'HR Manager', parent: fixture(:job_title_ceo), company: fixture(:company)
      )
    }

    fixture(:job_title_staff) {
      FactoryBot.create(
        :job_title,
        title: 'Staff', parent: fixture(:job_title_ceo), company: fixture(:company)
      )
    }

    fixture(:org_general) {
      FactoryBot.create(
        :organisation_node,
        name: 'General', company_id: fixture(:company).id
      )
    }

    fixture(:org_hr) {
      FactoryBot.create(
        :organisation_node,
        name: 'HR', company_id: fixture(:company).id
      )
    }

    fixture(:job_level_ceo) {
      FactoryBot.create(:job_level, :CEO, company: fixture(:company))
    }

    fixture(:job_level_hr) {
      FactoryBot.create(:job_level, name: 'HR', company: fixture(:company))
    }

    fixture(:job_level_staff) {
      FactoryBot.create(:job_level, name: 'Staff', company: fixture(:company))
    }

    fixture(:owner) {
      FactoryBot.create(
        :employee, :owner,
        company: fixture(:company),
        location: fixture(:location),
        job_level: fixture(:job_level_ceo),
        organisation_node: fixture(:org_general),
        job_title: fixture(:job_title_ceo)
      )
    }

    fixture(:hr_admin) {
      FactoryBot.create(
        :employee, :hr_admin,
        company: fixture(:company),
        location: fixture(:location),
        job_level: fixture(:job_level_hr),
        organisation_node: fixture(:org_hr),
        job_title: fixture(:job_title_hr)
      )
    }

    fixture(:staff) {
      FactoryBot.create(
        :employee, :staff,
        company: fixture(:company),
        location: fixture(:location),
        job_level: fixture(:job_level_staff),
        organisation_node: fixture(:org_general),
        job_title: fixture(:job_title_staff)
      )
    }
  end

  # Ensure that every example has a fresh copy of a record
  %i[
    company location job_title_ceo job_title_hr org_general org_hr
    job_level_ceo job_level_hr owner hr_admin staff
  ].each do |object|
    let(object) { fixture(object).refind }
  end
end 
```

想象一下这个例子。

```
describe User do
  let!(:user) { FactoryGirl.create :user }

  it 'does something' do
    # test with user
  end

  it 'does something' do
    # test with user
  end

  it 'does something' do
    # test with user
  end
end 
```

数据库中创建了多少用户记录？每个示例一条记录，所以答案是 3 条记录。为了避免这种情况，你可以使用(let _ it _ be)[[https://test-prof.evilmartians.io/#/let_it_be](https://test-prof.evilmartians.io/#/let_it_be)。这个助手使用 Rails 事务测试特性，这意味着记录只在测试开始时创建一次，并在测试结束时删除。

```
describe User do
  let_it_be(:user) { FactoryGirl.create :user }

  it 'does something' do
    # test with user
  end

  it 'does something' do
    # test with user
  end

  it 'does something' do
    # test with user
  end
end 
```

### 附加建议和资源

有些情况下，我们不需要先将数据保存到数据库中，例如检查模型上的自定义验证和视图或邮件规范上的单元测试。如果发生这种情况，我们可以利用`build`和`build_stubbed`创建策略。

```
let(:company) { FactoryBot.build_stubbed(:company) } 
```

一些有用的链接:

*   (测试分析)[[https://Test-prof . evil Marshall . io](https://test-prof.evilmartians.io)
*   (针对您的 ruby 测试的工厂疗法)[[https://evil Marshall . com/chronicles/test prof-2-Factory-therapy-for-your-ruby-tests-rspec-minitest # bonus-any fixture](https://evilmartians.com/chronicles/testprof-2-factory-therapy-for-your-ruby-tests-rspec-minitest#bonus-anyfixture)]
*   (build and build _ stubbed)[[https://thoughtbot . com/blog/use-factory-girls-build-stubbed-for-a-fast-test](https://thoughtbot.com/blog/use-factory-girls-build-stubbed-for-a-faster-test)