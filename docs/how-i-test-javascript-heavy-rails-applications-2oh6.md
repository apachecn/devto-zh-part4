# 我如何测试大量使用 JavaScript 的 Rails 应用程序

> 原文：<https://dev.to/jasonswett/how-i-test-javascript-heavy-rails-applications-2oh6>

我得到的一个常见问题是如何在 Rails 应用程序中测试 JavaScript。我的方法几乎完全简单而不复杂。

## 我的 Rails + JavaScript 测试方法

我认为应用程序使用 JavaScript 就像一个无关紧要的实现细节。我只使用 [RSpec + Capybara 集成测试](https://www.codewithjason.com/rails-integration-tests-rspec-capybara/)来测试大量使用 JavaScript 的应用程序，就像我测试一个只有很少 JavaScript 或者根本没有 JavaScript 的应用程序一样。我真的没有什么要说的，因为我实际上没有做任何不同于我的常规 RSpec +水豚测试的事情。

## 单页应用

单页应用呢？我仍然使用同样的方法。当我构建 Angular+Rails spa 时，我会添加一个`before(:all)` RSpec 钩子，在测试套件运行之前启动我的 Angular 应用程序的构建。此后，我的 RSpec + Capybara 测试可以与我的 SPA 轻松交互，就像应用程序是一个“传统的”Rails 应用程序一样。

*附注:传统的 Rails 应用程序很好。使用带有反应/Vue/角度/等的轨道。并不“现代”,使用没有这些的 Rails 并不“过时”。对于大多数常规的老式商业应用程序来说，没有前端框架的 Rails 本身不仅是一种足够的方法，而且是 SPA 的一种更好的方法，因为使用普通 Rails 和“JavaScript sprinkles”进行开发的复杂性往往远远低于使用 JavaScript 框架的 Rails。*

## 直接测试 JavaScript

尽管我通常将 JavaScript 视为一个细节，但有时我希望有更严格的控制，并直接测试我的 JavaScript。在这些情况下，我使用 Jasmine 来测试我的 JavaScript。但是我的目标是使用如此之少的 JavaScript，以至于我永远不会超过我认为需要直接用 Jasmine 测试我的 JavaScript 的复杂度。我发现，如果我真的尝试，我可以在大多数应用程序中使用很少的 JavaScript，而不会牺牲任何 UI 的丰富性。