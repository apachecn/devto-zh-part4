# Getters y setters VS 数据属性

> 原文：<https://dev.to/kuscamara/getters-y-setters-vs-data-properties-34ab>

*和**行业*与我们相处得很愉快(这是 E5 的特点)，是聚合物或[vista](https://youtu.be/VyzlZl6LlqM?t=2189)等一些书店反应性的基础。但是，当我们使用它们时，往往会出现疑问。∞以及为什么使用*和*(*【接入属性】*)代替*【数据属性】*？本文中我们看到了一些使用案例和示例。****

 *## 调用函数

使用存取器属性和*数据属性*是相同的，但前者在过程中调用函数:查询时调用 *getter* 和 *setter* 这使我们能够执行额外任务，如**验证**，计算**计算特性**或**更新**。

考虑到这种**的动态性，而且我们只能使用其中一种方法(通常只有`get`，我们可以列举一些**的明确使用案例**。**

 **## 属性*只读*

为某个*财产提供一次性的可防止其价值被设定，从而使其成为只读的。如果在严格模式下指定值，则不会生效或出现错误。* 

```
const player = {
  lives: 5,
  get canPlay() {
    return this.lives > 0;
  }
};

player.canPlay = false; // sin efecto o con error: TypeError: Cannot set property canPlay of #<Object> which has only a getter
player.canPlay; // true 
```

Enter fullscreen mode Exit fullscreen mode

## 计算的财产

我们可以用*抛子*根据其他的设定一个*财产*的价值。
所有关于*和*的文章中所用的例子就是这个，我也不会少于:** 

```
const person = {
  firstName: 'John',
  lastName: 'Doe',
  get fullName() {
    return `${this.firstName}  ${this.lastName}`;
  }
};

person.fullName; // John Doe 
```

Enter fullscreen mode Exit fullscreen mode

我们每次咨询`person.fullName`时，都会执行*，并从另外两个*属性中返回**【更新值】。****

 **## 验证

*setter* 只接受一个参数:我们给予*财产*的价值。我们可以用*扇区*来验证*性质*的类型是否符合预期，格式是否正确等。

```
const player = {
  _lives: 0,
  set lives(value) {
    if (typeof value !== 'number') {
      throw TypeError('lives must be a number');
    }

    this._lives = value;
  }
};

player.lives = 'a lot'; // TypeError: lives must be a number 
```

Enter fullscreen mode Exit fullscreen mode

## 动态值

*getter*在查询时调用函数这一事实使我们能够获得 *properties* 的更新值，否则(如 *data properties* 将保持其创建时的值。

```
function createQueue() {
  const items = [];

  return {
    add(item) {
      items.push(item);
    },
    remove() {
      return items.pop();
    },
    get length() {
      return items.length;
    }
  };
}

const queue = createQueue();
queue.add('item');
queue.length; // 1 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，如果我们使用了一个指定给`items.length`的*数据属性* `length`，则其值在创建对象时始终返回`items`的长度，即 0。

## 反应性

正如前文所述，和*业馀爱好者*落后于部分书店的反应能力，是我们设定或改变任何*物业的价值时造成一切的原因*

聚合物称之为[【可观察到的变化】](https://polymer-library.polymer-project.org/1.0/docs/devguide/data-system#observable-changes)，其作用是将*性质*的值保存在一个物体上，其变化应引起其他效应(*性质效应*。

每当设置这些属性之一的值时，就会在 *setter* 中检查该值是否与以前的值相比有所变化，如果有变化，则会执行与该变化相关的所有操作。

在下面的捕捉中，我们可以看到一个*属性*的价值更新(*绑定*是如何产生于该*属性*的*7】中的*。

[![Captura del call stack desde que se cambia una property (binding) en Polymer hasta que se actualiza su valor en el DOM](img/75ece7b493ba24a4ae9d07ed7008e3f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SKJehtmy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tkrzkzygya20vxsgvtwh.png)

## 隐私

最后，*和*行业*允许我们提供一种访问我们不想意外或故意修改的属性的方法。*

 *在下面的例子中，我们想提供一种访问*pubs sub*的`channels`的方法，但我们不希望这种访问也允许修改原始对象。将物件的复本传回`channels`的*，以确保我们只能透过提供的 API(`subscribe`控制的方式修改`channels`。* 

```
// pubsub.js
const channels = {};

class PubSub {
  get channels() {
    return {
      ...channels
    };
  }
  subscribe(channel, callback) {
    channels[channel] = channels[channel] || [];
    channels[channel].push(callback);
  }
}

export default new PubSub();

// some-file.js
import pubsub from './pubsub';

pubsub.subscribe('my-channel', () => {
  console.log('hey!');
});

pubsub.channels['my-channel'] = 'foo'; 
pubsub.channels; // { 'my-channel': [ [Function] ] } 
```

Enter fullscreen mode Exit fullscreen mode*********