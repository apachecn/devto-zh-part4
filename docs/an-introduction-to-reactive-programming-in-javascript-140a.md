# JavaScript 中的反应式编程简介

> 原文：<https://dev.to/daslaf/an-introduction-to-reactive-programming-in-javascript-140a>

> 本文原载西班牙文[此处](https://medium.com/@osmancea/programaci%C3%B3n-reactiva-en-javascript-997478d45bfb)。

反应式编程是一种声明式编程范例，主要关注(1)数据流的使用和(2)变化传播。反应式编程的原则是一切都是一个流。

但是奥斯曼，什么是溪流？

[![Yeah, Osman. What is a stream?](img/2d34a2880c368db3ab9eee6457296539.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C-nOqlNS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2AicV5kXGwDQnTKgVBY7Ourg.jpeg)

<figcaption>Yeah, Osman. What is a stream?</figcaption>

### 关于溪流

#### 据维基百科:

> 在计算机科学中，流是一段时间内可用的数据元素序列。一个流可以被认为是传送带上的项目，一次处理一个，而不是成批处理。

应用反应式编程的*禅*:应用程序生命周期中触发的所有数据和事件都可以表示为随时间变化的数据流；当我们对这些变化做出反应时，我们的程序是被动的。

我能想到的最常见的反应性例子如下:

```
const $increment = document.querySelector('#increment');
let counter = 0;

$increment.addEventListener('click', onIncrement);

function onIncrement(event) {
  counter += 1;
} 
```

是的，当注册`onIncrement`为点击事件的事件监听器时，我们使用的是反应式编程。我们可以把每一次点击`$increment`按钮看作一段时间内的事件集合，对于每个事件，我们运行`onIncrement`来作出反应。

但是不仅点击可以表示为流。每次执行`onIncrement`时，我们都会改变`counter`的值，因此`counter`的值也可以表示为一个流。

让我们在示例中再添加几个函数:

```
const $increment = document.querySelector('#increment');
const $decrement = document.querySelector('#decrement');
const $double = document.querySelector('#double');
const $halve = document.querySelector('#halve');

let counter = 0;

$increment.addEventListener('click', onIncrement);
$decrement.addEventListener('click', onDecrement);
$double.addEventListener('click', onDouble);
$halve.addEventListener('click', onHalve);

function onIncrement(e) {
  counter += 1;
}
function onDecrement(e) {
  counter -= 1;
}
function onDouble(e) {
  counter *= 2;
}
function onHalve(e) {
  counter /= 2;
} 
```

每次我们点击任何按钮时,`counter`的值都会改变，并被记录到控制台中。不幸的是，在我们的事件监听器回调中改变`counter`的值并不是最好的做法:即使我们表达了以某种方式改变`counter`的意图，也不清楚`counter`的值是一组特定操作的结果。如果我们可以像这样表达前面的代码，那会好得多(或者更具声明性):

```
let counter = 0;

combine(
  onIncrementClick,
  onDecrementClick,
  onDoubleClick,
  onHalveClick,
).observe(result => {
  counter = result;
  console.log(result);
}); 
```

我敢说，即使不知道`combine`和`observe`函数的实现细节，也能更清楚、更容易地理解程序的意图:从一个初始值并结合一组函数，我们得到一个新的 counter 值。

让我们回到一切都是流的观点。

[![A stream of clicks](img/7a69cb20ab17d5d1093f6319684a2390.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YkXdu4bh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AB57bZMoQuDVdREkpjVcwKg.jpeg)

<figcaption>Representation of clicks on $increment over time</figcaption>

上面的大理石图是一段时间内按钮`$increment`上的点击事件的表示，或者用我们到目前为止所学的概念来表述，一系列的点击。

我们也可以为每个按钮设定一个时间线:

[![More streams](img/63bf8093bd55bc25f5ce5772425c5f9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LvJqeZDo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AKBK96fR2xSAmvYw1Y1TRtQ.jpeg)

当合并我们的流时，我们产生了另一个流，称为`clicks`。该流表示在一段时间内点击了哪些按钮。如果我们将每个点击流与一个更新`counter`值的函数相关联，我们的`clicks`流也可以代表`counter`随时间的值。

稍后我们将看到如何将我们的事件组合成一个单独的流。

澄清了流的本质之后，让我们来谈谈反应式编程中的第二个重要概念，即变更传播。

### 什么是变化传播？

变化传播基本上是我们如何通知程序的一个模块它的一些依赖关系已经改变(或者一些事件已经发生)并且它必须相应地作出反应。

在反应式系统中，变革发起者和对这些变革做出反应的人之间的工效学与主动式系统有着不同的构成。

[André Staltz](https://twitter.com/andrestaltz) 在[这个演讲](https://www.youtube.com/watch?v=49dMGC1hM1o&feature=youtu.be&t=2m46s)中精彩地解释了这种关系。

总之，在前摄系统中，如果一个模块触发了另一个模块上的变化，则前者依赖后者。在反应系统中，关系是相反的。让我们通过一个代码示例来看看这是如何工作的:

```
class Cart {
  constructor(invoice) {
    this.items = [];
    this.invoice = invoice;
  }

  addItem(item) {
    this.items.push(item);
  }

  checkout() {
    const total = this.items.reduce(
      (acc, item) => acc + item.price,
      0
    );

    this.invoice.update(total);
  }
}

class Invoice {
  constructor() {
    this.total = 0;  
  }

  update(total) {
    this.total = total;
  }
}

const invoice = new Invoice();
const cart = new Cart(invoice);

cart.addItem({ name: 'Miller Lite', price: 1.99 });
cart.addItem({ name: 'Chips', price: 1.50 });
cart.checkout();

console.log(invoice.total); // 3.49 
```

`Cart`类依赖`Invoice`(我们将`Invoice`的一个实例作为构造函数参数传递给`Cart`)。当我们调用`checkout`方法时，`Invoice`上的`update`方法被直接调用。在这种情况下，我们触发了从`Cart`类到`Invoice`类的变化，因此我们的系统是主动的。

我们来看看反应型的:

```
const EventEmitter = require('events');

class Cart {
  constructor() {
    this.items = [];
    this.emitter = new EventEmitter();
  }

  addItem(item) {
    this.items.push(item);
  }

  checkout() {
    const total = this.items.reduce(
      (_total, item) => _total + item.price,
      0
    );

    this.emitter.emit('checkout', total);
  }
}

class Invoice {
  constructor(cart) {
    this.total = 0;
    this.cart = cart;

    this.cart.emitter.on('checkout', this.update.bind(this));
  }

  update(total) {
    this.total = total;
  }
}

const cart = new Cart();
const invoice = new Invoice(cart);

cart.addItem({ name: 'Miller Lite', price: 1.99 });
cart.addItem({ name: 'Chips', price: 1.50 });
cart.checkout();

console.log(invoice.total); // 3.49 
```

这里的主要区别在于，现在`Invoice`将`Cart`作为依赖项，与前面的例子正好相反。现在，当我们调用`checkout`方法时，会发出一个名为`checkout`的事件，总费用作为有效载荷。注意，我们没有更新`Invoice`实例中的总值，我们只是发出了一条消息，表明`checkout`事件已经发生。`Invoice`实例正在监听类型为`checkout`的事件，每当该事件被发出时，它就调用`update`方法来处理它。

女士们先生们，这就是反应性。

### 观察者模式👀

我们已经知道了反应式编程的原理，但是我们来看看一个设计模式，它可以帮助我们对程序的不同部分之间的交互进行建模:观察者模式，这将是一个不错的想法。

#### 据维基百科:

> 观察者模式是一种软件设计模式，在这种模式中，一个名为 subject 的对象维护一个名为 observer 的依赖者列表，并自动通知它们任何状态变化，通常是通过调用它们的方法之一。

让我们回顾一下我们的第一个例子:

```
const $increment = document.querySelector('#increment');
let counter = 0;

$increment.addEventListener('click', onIncrement);

function onIncrement(event) {
  counter += 1;
} 
```

假设我们希望有多个计数器，而不是只有一个。我们应该如何重构我们的程序？我们可以(a)修改`onIncrement`函数来更新它体内的计数器，或者(b)注册更多的函数来监听同一个事件。

解决方案(a)没有那么灵活。如果我们删除事件侦听器，那么我们将失去更新任何计数器的能力。我们不能同时更新一个计数器:

```
const $increment = document.querySelector('#increment');
let counterA = 0;
let counterB = 0;

$increment.addEventListener('click', onIncrement);

function onIncrement(event) {
  counterA += 1;
  counterB += 1;
}

// After 3 seconds no counter will receive updates
setTimeout(() => {
  $increment.removeEventListener('click', onIncrement);
}, 3000); 
```

为了实现解决方案(b ),让我们应用观察者模式。我们将创建一个名为`FromEvent`的类作为主题:

```
class FromEvent {
  constructor(target, name) {
    this.observers = [];

    target.addEventListener(name, this.next.bind(this));
  }

  observe(observer) {
    this.observers.push(observer);
  }

  remove(observer) {
    this.observers = this.observers.filter(
      obs => obs !== observer
    );
  }

  next(e) {
    this.observers.forEach(obs => obs.next(e));
  } 
} 
```

我们使用我们的类`FromEvent`来注册点击事件的观察者:

```
const $increment = document.querySelector('#increment');
let counterA = 0;
let counterB = 0;

const fromEvent = new FromEvent($increment, "click");

const updateCounter = (counter, callback) => (event) => {
  counter = callback(counter);
  console.log(counter);
};

const observerA = {
  next: updateCounter(counterA, value => value + 1)
};
const observerB = {
  next: updateCounter(counterB, value => value + 2)
};

fromEvent.observe(observerA);
fromEvent.observe(observerB);

// After 3 seconds only counterA will receive updates
setTimeout(() => {
  fromEvent.remove(observerB);
}, 3000); 
```

在这种情况下，我们将一个高阶函数(`updateCounter`)分配给每个观察器的`next`方法，它负责更新每个计数器的值。然后我们使用`observe`函数将每个观察者注册到`fromEvent`主题。通过这种方式，我们有效地将我们的程序建模为具有变更源(主体)和多个消费者(观察者)。

### 回到开始

利用我们已经获得的知识，让我们实现本文开头提出的理想 API:

```
let counter = 0;

combine(
  onIncrementClick,
  onDecrementClick,
  onDoubleClick,
  onHalveClick,
).observe(result => {
  counter = result;
  console.log(result);
}); 
```

我们将开始实现`combine`，它是一个以两个或多个主题为参数，返回一个新主题的函数。其思想是，无论何时任何主题发出一个事件，我们都必须通知观察者由`combine`返回的主题。当我们在`combine`上注册一个观察者时，我们也将在我们作为参数传递给`combine`的每个主题上注册一些观察者。

```
const combine = (...subjects) => ({
  observe: observer => {
    subjects.forEach(subject => {
      subject.observe(observer)
    })
  }, 
}); 
```

我们可以传递多个流作为参数:

```
const onIncrementClick = new FromEvent($increment, "click");
const onDecrementClick = new FromEvent($decrement, "click");

const clicks = combine(
  onIncrementClick,
  onDecrementClick
);

const clicksObserver = {
  next: e => console.log('click', e.target.id)
}

clicks.observe(clicksObserver); 
```

太好了！现在我们有一种方法来组合多个数据流。

### 作业

因此，我们涵盖了关于如何使用观察者模式进行反应式编程的所有基础。现在唯一要做的就是将每个事件流映射到可以相应修改计数器值的函数。如果您不知道从哪里开始，您可以向`FromEvent`类添加一个`map`方法，以便将每个事件映射到一个有用的值。

你可以在这里找到我的解决方案[的实现。](https://codesandbox.io/s/qqj3zp4zj6)

如果你喜欢这篇文章，别忘了分享它，并在 Twitter 上关注我，以获得更多关于 JavaScript、反应式编程、函数式编程、前端开发等的见解。

干杯！