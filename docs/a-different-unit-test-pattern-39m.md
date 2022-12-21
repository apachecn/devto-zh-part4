# 不同的单元测试模式

> 原文：<https://dev.to/rfornal/a-different-unit-test-pattern-39m>

我将把这个模式添加到[坏测试，坏测试](https://dev.to/rfornal/bad-test-bad-1og7)

## 一个单元测试反模式

这个测试是我以前从未见过的模式...其中，测试的设置和发起者位于嵌套的 ***中，每个*** 之前，而单个的 ***期望*** 在它们自己的测试中。

当我第一次看到这段代码时，我想，“这太酷了。”

然后，我很快有了一种奇怪的、令人反胃的感觉...

*   如果这个图案很酷，为什么我以前没在哪里见过这个图案？
*   为什么开发人员会创建这种模式？

```
describe("removeSomething", function () {
  describe("where when called", function () {
    beforeEach(function () {
      this.module.remove.and.returnValue(jasmine.helpers.deferredDone());
      this.module.removeSomething();
    });

    it("should call remove action to remove something", function () {
      expect(this.module.remove).toHaveBeenCalledWith({
        "subPathId": "removeSomething"
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我想到的第一件事是，有人被教导每个测试只创建**一个期望，这种模式允许单独执行被测代码。这种模式允许开发人员忽略被测代码的单一责任原则(SRP ),编写看起来像好的测试。**

## 结论

对于这组特定的代码，我发现可以通过简单地将上面每个代码之前的**移动到测试中来测试原始代码。但是在这里，重点是不应该使用该模式。虽然它“在某种程度上”是优雅的，但它也使得测试的设置和启动变得复杂，这可能会导致更复杂的被测代码模式，从而打破单一责任原则。**