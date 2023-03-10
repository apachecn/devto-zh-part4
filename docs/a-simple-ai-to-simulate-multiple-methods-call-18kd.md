# 一个简单的人工智能来模拟多个方法调用

> 原文：<https://dev.to/nicolalc/a-simple-ai-to-simulate-multiple-methods-call-18kd>

# 用例介绍

## 为什么

我制作了一个简单的 js 脚本来处理不同参数的多个方法调用。我需要这个脚本的原因非常简单:

1.  用不同的参数模拟多个服务器端方法
2.  用大量的方法调用给服务器(或客户机)增加压力
3.  测试我的服务器端方法的强度
4.  自动化服务器行为，因为我不想在开发时手动触发很多方法。

## 如何

这个脚本以一种简单的方式工作，它接受一组定义的方法和一组不同的参数。所以脚本会用随机参数调用这些方法，模拟这些方法的执行。

## AI.js

以下是“AI”的实现:

```
/**
 _______  ___             ___  _______ 
|   _   ||   |           |   ||       |
|  |_|  ||   |           |   ||  _____|
|       ||   |           |   || |_____ 
|       ||   |  ___   ___|   ||_____  |
|   _   ||   | |   | |       | _____| |
|__| |__||___| |___| |_______||_______|

@desc   a script to automate js method run
@author nicolacastellanidev@gmail.com
*/
function AI(...bindings) {
  if (!bindings) {
    throw new Error('AI cannot run without bindings');
  }
  this.bindings = bindings;
}

AI.prototype = {
  running: false,
  /** @desc run a random binding with random parameters*/
  run() {
    this.running = true;
    /** get a random method to call with random params */
    const randomBindingIndex = Math.floor(Math.random() * this.bindings[0].length);
    const binding = this.bindings[0][randomBindingIndex];
    const randomParamIndex = Math.floor(Math.random() * binding.withParams.length);

    /** call random binding */
    try {
      this.log(`>>> SIMULATING *** ${binding.method.name} *** WITH PARAMS *** ${Object.values(binding.withParams[randomParamIndex])} ***`);
      binding.method.call(this, ...Object.values(binding.withParams[randomParamIndex]));
    } catch (ex) {
      this.log(`>>> SIMULATING *** ${binding.method.name} *** WITH PARAMS *** ${binding.withParams[randomParamIndex]} ***`);
      this.log(`>>> ERROR: ${ex.message}`);
    }
    /** then set a random timeout for the next call */
    this.runTimeout = setTimeout(() => {
      this.run();
    }, (Math.floor(Math.random() * 5) + 2) * 1000);
  },
  /** @desc stop the AI execution */
  stop() {
    this.running = false;
    this.log(`>>> SIMULATION STOPPED`);
    clearTimeout(this.runTimeout);
  },

  /** @desc log method - appen a string to the console html element */
  log(what) {
    console.log(`${what}</br>`)
  }
}; 
```

### 造假手段实现

```
/**
* FAKE METHODS IMPLEMENTATION
*/
const fakeMethod = (url) => {
  console.log(`A fakeMethod has been called with url: ${url}`);
}

const sum = (a, b) => {
  console.log(`The sum is: ${a + b}`);
}

const diff = (a, b) => {
  console.log(`The diff is: ${a - b}`);
} 
```

### AI 初始化用假方法

```
/**
* AI INITIALIZATION
*/
const r2d2 = new AI(
  [
    {
      method: fakeMethod,
      withParams: [
        {url: '/simulate?action=1'},
        {url: '/simulate?action=2'},
      ]
    },
    {
      method: sum,
      withParams: [
        {a: 1, b: 1},
        {a: 1, b: 3}
      ]
    },
    {
      method: diff,
      withParams: [
        {a: 1, b: 1},
        {a: 3, b: 5}
      ]
    }
  ]
); 
```

### 运行 AI

使用方法**运行**你可以开始模拟:

```
r2d2.run(); 
```

### 停止 AI

使用**停止**方法，您可以停止模拟:

```
r2d2.stop(); 
```

## 活生生的例子

[https://codepen.io/NicolaCastellaniDev/embed/RwbVgzb?height=600&default-tab=result&embed-version=2](https://codepen.io/NicolaCastellaniDev/embed/RwbVgzb?height=600&default-tab=result&embed-version=2)

# 结论

这只是一个例子，你可以根据自己的喜好扩展更复杂的模拟，或者人工智能行为。