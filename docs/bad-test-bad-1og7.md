# 糟糕的测试，糟糕的

> 原文：<https://dev.to/rfornal/bad-test-bad-1og7>

> 这些测试本来就不应该被编写。它们不提供价值或提供很少的价值。

测试代码被描述为一种“艺术形式”是，但不应该是。在编写测试时，应该遵循许多好的模式。甚至有一个测试的生命周期应该引起注意。还有许多开发人员应该知道的坏模式，以便可以避免(或清除)。

这些年来，我听说并遇到了许多测试不正确、违反测试基本原则、做得太多或太少的例子。我所面临的挑战是找到支持我所遵循的测试原则的实际例子。

这篇文章的原因是提供具体的不好的代码模式，以及在可能的情况下，纠正这些不好的测试的方法。

这里使用的代码位于 https://github.com/bob-fornal/bad-test-bad 的[github 上。](https://github.com/bob-fornal/bad-test-bad)

## 使用不良测试模式

假设我们展示的是糟糕的代码:总是有理由违反这些规则，但是违反规则背后的逻辑应该经过深思熟虑，并在测试代码中进行描述。

## 测试不应被“注释掉”

永远不要通过管道推入测试来获取代码。只有当代码被删除时，相应的测试才应该被删除。

## 测试不应该是“分解良好的”

```
/* BAD PATTERN: Well-Factored */
var testableCode = {
  user: {},
  getUserRating: function() {
    return testableCode.user.rating;
  },
  getUserScore: function() {
    return testableCode.user.score;
  }
};

beforeEach(function() {
  testableCode.user = {
    username: 'Bob',
    rating: 100,
    score: 1.1
  };
});

// more code and tests here...

it('expects user rating to be returned', function() {
  var result = testableCode.getUserRating('Bob');
  expect(result).toEqual(100);
});

it('expects user score to be returned', function() {
  var result = testableCode.getUserScore('Bob');
  expect(result).toEqual(1.1);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码中的测试是做什么的？它检索一个评级，并验证该值是否为 100。只看这个测试，我会想到几个问题...

1.  “鲍勃”是从哪里来的？
2.  为什么评分是 100？

这个测试是分解良好的，这意味着在这种情况下，显而易见的信息被移出了测试。使用稍微不同的设计对此进行检查...

```
/* GOOD PATTERN: Keep the reader in the test */
/* GOOD PATTERN: Violate the DRY Principle */
var testableCode = {
  user: {},
  getUserRating: function() {
    return testableCode.user.rating;
  },
  getUserScore: function() {
    return testableCode.user.score;
  }
};

afterEach(function() {
  testableCode.user = {};
});

// more code and tests here...

it('expects user rating to be returned', function() {
  testableCode.user = {
    username: 'Bob',
    rating: 100,
    score: 1.1
  };

  var result = testableCode.getUserRating('Bob');
  expect(result).toEqual(100);
});

it('expects user score to be returned', function() {
  testableCode.user = {
    username: 'Bob',
    rating: 100,
    score: 1.1
  };

  var result = testableCode.getUserScore('Bob');
  expect(result).toEqual(1.1);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 保持读者在测试中

在编写测试时，考虑下一个会看到测试的开发人员。他们不想阅读整个测试套件，他们当然也不想通读一套测试工具。

如果一个测试中断，读者应该能够通过从上到下的直线阅读测试来诊断问题。如果他们不得不跳出测试去读取额外的代码，那么测试就没有完成它的工作。

### 违背干原则

这种包含有意义信息的模式意味着代码将被重复，很可能使用复制/粘贴。这很好；测试包括所有必要的信息。

这里的目标很明确，简单的测试。

在盲目地将 DRY 原则应用于测试之前，想想当一个测试失败时，什么会使问题变得明显。重构可能会减少重复，但也会增加复杂性，并且当事情发生时会模糊信息。

## 测试过多

给定一些代码...

```
var testableCode = {
  strings: [],
  getString: function() {
    if (testableCode.strings.length === 0) {
      return "";
    }
    return testableCode.strings.pop();
  },
  setString: function(value) {
    testableCode.string.push(value);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，检查一个可能的测试...

```
/* BAD PATTERN: Testing Too Much */
it('expects getString to return an empty string', function() {
  testableCode.strings = [];
  expect(testableCode.getString()).toEqual("");

  testableCode.strings = ["Bob"];
  expect(testableCode.getString()).toEqual("Bob");
  expect(testableCode.getString()).toEqual("");
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的测试做的太多了。至少有两种不同的情况。清理这些测试应该会产生如下结果...

```
/* GOOD PATTERN: Each test expects once */
describe('it expects getString to return', function() {
  it('an empty string', function() {
    testableCode.strings = [];
    expect(testableCode.getString()).toEqual("");
  });

  it('the "last" value', function() {
    testableCode.strings = ["Dan", "Bob"];
    expect(testableCode.getString()).toEqual("Bob");
  });

  it('an empty string after all other values are removed', function() {
    testableCode.strings = ["Bob"];
    testableCode.getString(); // removes "Bob"
    expect(testableCode.getString()).toEqual("");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 每次测试预计一次

拥有一个以上的断言并不会自动成为问题(即使拥有太多断言是一种代码味道)。但是比资产数量更重要的是它们的质量；语义。测试名称承诺它只测试一个场景。但是这个测试远不止于此，它还测试其他行为，每一个行为都应该有自己的焦点和名字。

### 打破单一责任原则

(在测试代码中)

这种测试...

```
/* BAD PATTERN: Code-under-test can break SRP */
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

...将允许开发人员遵循上面的“每个测试期望一次”规则，测试下的代码做得太多，违反了单一责任原则。

参见[不同的单元测试模式](https://dev.to/rfornal/a-different-unit-test-pattern-39m)

## 代码逻辑重复

给定一些代码...

```
var testableCode = {
  getString: function(a, b, len) {
    return (a + b).substr(0, len);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，检查一个可能的测试...

```
/* BAD PATTERN: Duplication of code logic */
it('expects getString to return a + b at len', function() {
  var a = "abc";
  var b = "def";
  var len = 4;
  var result = (a + b).substr(len);

  expect(testableCode.getString(a, b, len)).toEqual(result);
}); 
```

Enter fullscreen mode Exit fullscreen mode

该测试包含被测代码使用的相同逻辑。如果这个逻辑包含一个缺陷，测试仍然可能成功。清理这些测试应该会产生如下结果...

```
/* GOOD PATTERN: Use Magic Numbers */
it('expects getString to return a + b at len', function() {
  var a = "abc";
  var b = "def";
  var len = 4;

  expect(testableCode.getString(a, b, len)).toEqual("abcd");
}); 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果代码中的逻辑是错误的(或者被更改为不正确的)，测试将会失败。

## 坏测双

这是一个用“忠实的”双倍测试的例子。

给出一些代码(这里有更多的设置)...

```
var apiService = {
  base: 'http://www.example.com/',
  testApi: 'testApi/',
  getSimple: function() {
    return fetch(apiService.base + apiService.testApi{
        method: 'GET'
      }).then(function(response) {
        return response.json();
      });
  },
  getMore: function() {
    return fetch(apiService.base + apiService.testApi{
        method: 'GET'
      }).then(function(response) {
        var result = response.json();
        result.checked = true;
        return result;
      });
  }
};

var testableCode = {
  getData: function(service) {
    return service.getSimple();
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码让我们检查一些测试路径，在这些路径中，双重测试可能会导致问题。

```
/* BAD PATTERN: Testing with a mock */
var mockService, mockResult;
beforeEach(function() {
  mockResult = {
    data: "Bob"
  };
  mockService = {
    getSimple: function() {
      return Promise().resolve(mockResult);
    }
  };
});

describe('expect getData to', function() {
  it('return the correct object', function(done) {
    testableCode.getData(mockService)
      .then(function(data) {
        expect(data).toEqual(mockResult);
        done();
      }).catch(function() {
        expect(true).toEqual(false);
        done();
      });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在前面的测试中，如果服务发生变化或者返回的数据发生变化，这些测试的设置也必须改变。这些测试将继续通过。

看下一个例子...

```
/* OK PATTERN: Hooking into the service */
var mockService, mockResult;
beforeEach(function(_apiService_) {
  mockResult = {
    data: "Bob"
  };
  mockService = _apiService_;
  mockService.getSimple = function() {
    return Promise().resolve(mockResult);
  };
});

describe('expect getData to', function() {
  it('return the correct object', function(done) {
    testableCode.getData(mockService)
      .then(function(data) {
        expect(data).toEqual(mockResult);
        done();
      }).catch(function() {
        expect(true).toEqual(false);
        done();
      });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，如果服务发生变化或者返回的数据发生变化，前面的示例将继续有效。这是对之前测试的一个改进，因为如果需要的话， ***蜂业*** 的内部剩余部分可以暴露出来使用。这种公开将允许 ***testableCode*** 的变化以及服务的其他部分保持不变。

```
/* GOOD PATTERN: Hooking into HTTP Request */
var mockService, mockResult;
beforeEach(function(_apiService_) {
  mockResult = {
    data: "Bob"
  };
  mockService = _apiService_;

  // Hook into HTTP Requests here ...
  // Specifically: GET on http://www.example.com/testApi/
  // Leaving this code out; will depend on packages
  //   or frameworks used
});

describe('expect getData to', function() {
  it('return the correct object', function(done) {
    testableCode.getData(mockService)
      .then(function(data) {
        expect(data).toEqual(mockResult);
        done();
      }).catch(function() {
        expect(true).toEqual(false);
        done();
      });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码应该更能适应变化。例如，可测试代码中的 ***getData*** 可以改为使用服务中的 ***getMore*** 而不是 ***getSimple*** 而不会失败。

在这里，测试只容易受到返回的 API 数据变化的影响。

## 针对不良数据进行测试

给定一些代码...

```
var testableCode = {
  isDateAfterToday: (givenDate) => {
    var currentDate = new Date();
    return givenDate > currentDate;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，检查一个可能的测试...

```
/* BAD PATTERN: Testing against bad data */
it('expects isDateAfterToday to return true', function() {
  var futureDate = new Date('2019-10-22');
  expect(testableCode.isDateAfterToday(futureDate))
    .toEqual(true);
});

/* BETTER PATTERN: Testing against bad data */
it('expects isDateAfterToday to return true', function() {
  var futureDate = new Date('3019-10-22');
  expect(testableCode.isDateAfterToday(futureDate))
    .toEqual(true);
});

/* BEST PATTERN: Testing against bad data */
it('expects isDateAfterToday to return true', function() {
  var tomorrow = new Date();
  tomorrow.setDate(tomorrow.getDate() + 1);
  futureDate = new Date(tomorrow);
  expect(testableCode.isDateAfterToday(tomorrow))
    .toEqual(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

在坏模式中，日期是硬编码的；我们将达到这个日期(是的，这里使用的日期是在最近一次失败的测试中发现的)。

在更好的模式中，使用了一年的时间，我们很可能达不到这个时间。

在最佳模式中，我们正在计算一个明天不会达到的值。

## 试探嘲弄

给定一些代码...

```
var testableCode = {
  getString: function(a, b, len) {
    return (a + b).substr(0, len);
  },
  getShortName: function(first, last) {
    return testableCode.getString(last + ", ", first, 10);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，检查一个可能的测试...

```
/* BAD PATTERN: Testing the mock */
beforeEach(function() {
  jasmine.spyOn(testableCode, "getShortName")
    .and.returnValue("Bob45678901234567890");
});

describe('expects getShortName to return', function() {
  it('a name truncated to 10 characters', function() {
    expect(testableCode.getShortName("Bob", "Last"))
      .toEqual("Bob45678901234567890");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

本例中唯一需要测试的是在每个之前的**中创建的模拟。真正的 **getString** 功能在这里没有得到实现。这里唯一确定的是 **getString** 函数是在 **getShortName** 功能中实际调用的(这是一种灰箱测试的形式；内部的一些知识)。**

清理这些测试应该会产生如下结果...

```
/* GOOD PATTERN: Testing appropriate code */
describe('expects getString to return', function() {
  it('a + b at len', function() {
    var a = "abc";
    var b = "def";
    var len = 4;

    expect(testableCode.getString(a, b, len)).toEqual("abcd");
  });
});

describe('expects getShortName to return', function() {
  it('a name truncated to 10 characters', function() {
    expect(testableCode.getShortName("Bob4567890", "Last"))
      .toEqual("Last, Bob4");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，很明显我们是在测试代码，而不是测试框架本身。

## 误报

检查可能的测试...

```
/* BAD PATTERN: False positive */
it('expect the code inside setTimeout to be ignored', function() {
  setTimeout(function() {
    expect(true).toEqual(false);
  }, 1000);
}); 
```

Enter fullscreen mode Exit fullscreen mode

在测试完成之前， **setTimeout** 中的代码不会运行。

在大多数 javascript 测试套件中，没有预期的* *规范简单地通过了。

```
/* BAD PATTERN: False positive */
it('test with no expect will always pass', function() {
  const hello = 'World';
  const life = 42;
}); 
```

Enter fullscreen mode Exit fullscreen mode

处理这个问题很简单:在 mocha 和 jasmine 中，都可以向规范中传递一个额外的参数(通常称为 ***done*** )。
这将测试标记为异步，在将测试标记为通过之前，测试引擎将等待参数(函数)被调用。

从这个角度来看上面的例子...

```
/* GOOD PATTERN: Handling Asynchronous Behavior */
it('expect the code inside setTimeout to run', function(done) {
  setTimeout(function() {
    expect(true).toEqual(false);
    done();
  }, 1000);
});

/* GOOD PATTERN: Include expect */
it('test with an expect can pass or fail', function() {
  const hello = 'World';
  const life = 42;
  expect(true).toEqual(false);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这些测试现在将会失败。

### 承诺

查看一些代码...

```
var testableCode = {
  getRejectedPromise: function() {
    return new Promise((resolve, reject) => { 
      setTimeout(() => {
          reject('fail');
      }, 1000);
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，看看这个测试...

```
/* BAD PATTERN: False positive */
it('expects rejection to occur (should pass)', function() {
  testableCode.getRejectedPromise().then(function(result) {
    expect(result).toEqual('fail');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

如上所示，该测试将给出假阳性。使用看到的分辨率...

```
/* GOOD PATTERN: Handling Asynchronous Behavior */
it('expects rejection to occur (should follow catch)', function(done) {
  testableCode.getRejectedPromise()
    .then(function(result) {
      expect(result).toEqual('pass');
      done();
    })
    .catch(function(result) {
      expect(result).toEqual('fail');
      done();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

因此，在这一点上，如果代码通过了**解析**，它将会失败，但是由于它得到了**拒绝**，它将会正确地通过。

## 直接测试私有功能

查看一些代码...

```
function testableCode() {
  function privateFnA() { ... };
  function privateFnB() { ... };

  function publicFnC() { ... };
  function publicFnD() { ... };

  return {
    publicFnC,
    publicFnD,

    testable: {
      privateFnA,
      privateFnB,
      publicFnC,
      publicFnD
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，测试现在可以直接测试代码库中的所有功能。这里的问题是私有功能不应该被公开，如果代码中有一些功能上的变化，这将导致大量的测试重构和代码重构。

私有功能应该通过公开的功能进行间接测试。

## 过度设置

过多的设置更多的是一种代码味道，而不是显示不正确和正确的代码。请注意，在这种情况下，应该仔细检查设置为何如此冗长，并记录其存在的原因。

## 结论

这篇文章的原因是提供具体的不好的代码模式，以及在可能的情况下，纠正这些不好的测试的方法。

> 这些测试本来就不应该被编写。它们不提供价值或提供很少的价值。

测试代码被描述为一种“艺术形式”是，但不应该是。在编写测试时，应该遵循许多好的模式。甚至有一个测试的生命周期应该引起注意。还有许多开发人员应该知道的坏模式，以便可以避免(或清除)。