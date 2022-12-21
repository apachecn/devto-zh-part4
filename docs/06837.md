# 测试 setTimeout/setInterval

> 原文：<https://dev.to/rfornal/testing-settimeout-setinterval-22ff>

最近在一个客户那里，出现了一个关于使用 **setTimeout** 和 **setInterval** 的单元测试功能的问题。

这个特殊案例中的问题是，还有几个额外的位置实现了 setTimeout 和 setInterval...测试前需要运行完整的代码库。因为所有的代码都会运行，所以会出现 ***和其他*** 代码干扰测试的“溢出”情况。

## 图样

所讨论的模式将允许这两组功能以这样一种方式包装，即它们可以根据需要被移除。这个功能允许 id 以一种可以在测试迭代时删除的方式存储。

这种模式是 ***一个补丁*** 作为一个测试人员，我对使用这种模式感到有些不舒服，但是考虑到已经存在的大量代码，这似乎是一个合理的选择。

```
var timeoutIds = [];
var intervalIds = [];

var windowSetTimeout = window.setTimeout;
var windowSetInterval = window.setInterval;

window.setTimeout = function testSetTimeout() {
  var id = windowSetTimeout.apply(this, arguments)
  timeoutIds.push(id);
  return id;
};

window.setInterval = function testSetInterval() {
  var id = windowSetInterval.apply(this, arguments)
  intervalIds.push(id);
  return id;
};

afterEach(function() {
  timeoutIds.forEach(window.clearTimeout);
  intervalIds.forEach(window.clearInterval);
  timeoutIds = [];
  intervalIds = [];
}); 
```

Enter fullscreen mode Exit fullscreen mode

## setTimeout

现在，在展示了这个模式之后，我发现了一些其他的选项，虽然它们看起来更合理，但是不太适合这个已建立的代码库。

下面的例子主要来源于[如何用 jasmine 测试一个有 setTimeout 的函数？](https://stackoverflow.com/questions/10955201/how-to-test-a-function-which-has-a-settimeout-with-jasmine)

我从这些例子中看到的部分问题是 ***setInterval*** 没有被覆盖。

给定一个内部超时的函数...

```
var testableVariable = false;
function testableCode() {
  setTimeout(function() {
    testableVariable = true;
  }, 10);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 **done** 作为一种手段，告诉测试人员`expect`将被异步检查，为上面代码中的 setTimeout 运行留出足够的时间...

```
it('expects testableVariable to become true', function(done) {
  testableCode();

  setTimeout(function() {
    expect(testableVariable).toEqual(true);
    done();
  }, 20);
}); 
```

Enter fullscreen mode Exit fullscreen mode

此外，计时器行为可能会被嘲笑...这种方法允许 ***茉莉*** 将时间向前推进。

```
it('expects testableVariable to become true', function() {
  jasmine.clock().install();

  testableCode();
  jasmine.clock().tick(10);

  expect(testableVariable).toEqual(true);
  jasmine.clock().uninstall();
}); 
```

Enter fullscreen mode Exit fullscreen mode

和...我们现在可以使用 ***异步/等待*** ...

...从[异步工作](https://jasmine.github.io/tutorials/async)

这种模式实际上意味着`setTimeout`需要调整以允许它`resolve`...

```
var testableVariable = false;

const sleep = (time) => {
 return new Promise(resolve => setTimeout(resolve, time));
};

async function testableCode() {
  await sleep(10);
  testableVariable = true;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，测试变成了...

```
it('expects testableVariable to become true', async function() {
  await testableCode();
  expect(testableVariable).toEqual(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

此外，可以重构原始代码，将 ***setTimeout*** 中的函数取出，使其可测试。

```
var testableVariable = false;
function testableAfterTimeout() {
  testableVariable = true;
}
function testableCode() {
  setTimeout(testableAfterTimeout, 10);
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这段代码，我们可以简单地直接测试 **testableAfterTimeout** 函数...

```
it('expects testableVariable to become true', function() {
  testableAfterTimeout();
  expect(testableVariable).toEqual(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 设置间隔

看另一个例子...

```
var testableVariable2 = false;
function testableCode2(){
  var counter = 1;
  var interval = setInterval(function (){
    if (counter === 5){
      testableVariable2 = true;
      clearInterval(interval);
    }

    counter++;
  }, 500);

  return interval;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们应该能够看到前面的测试模式应该对我们有利。

使用 **done** 作为一种手段，告诉测试人员`expect`将被异步检查，为上面代码中的 setTimeout 运行留出足够的时间...

```
it('expects testableVariable2 to become true', function(done) {
  testableCode2();

  setTimeout(function() {
    expect(testableVariable2).toEqual(true);
    done();
  }, 4000);
}); 
```

Enter fullscreen mode Exit fullscreen mode

此外，计时器行为可能会被嘲笑...这种方法允许 ***茉莉*** 将时间向前推进。

```
it('expects testableVariable2 to become true', function() {
  jasmine.clock().install();

  testableCode2();
  jasmine.clock().tick(4000);

  expect(testableVariable2).toEqual(true);
  jasmine.clock().uninstall();
}); 
```

Enter fullscreen mode Exit fullscreen mode

同样，可以重构原始代码，将 ***setInterval*** 中的函数取出来，使其可测试。

```
var testableVariable2 = false;
var counter = 1;
var interval;
function testableAfterInterval() {
  if (counter === 5){
    testableVariable2 = true;
    clearInterval(interval);
  }
  counter++;
}
function testableCode2() {
  counter = 1
  interval = setInterval(testableAfterInterval, 500);
  return interval;
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这段代码，我们可以简单地直接测试 **testableAfterInterval** 函数...

```
it('expects testableVariable2 to become true', function() {
  counter = 5;
  testableAfterInterval();
  expect(testableVariable2).toEqual(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

有许多处理异步行为的方法。我在这里只提到了几个(并且是在一个特定的测试框架中)。这些只是出现这些情况时可以使用的一些模式。