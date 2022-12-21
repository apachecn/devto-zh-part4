# JavaScript 中的反应式原语(以及其他很酷的 OxJS)

> 原文：<https://dev.to/jfet97/reactive-primitives-in-javascript-and-other-cool-stuff-with-oxjs-4mhe>

好吧好吧，我知道你在想什么:这不可能！

[![](img/5aa332078f73da793ff0d3c7123d25c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1FNVdzEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.drcommodore.it/wp-content/uploads/2019/03/dsmGaKWMeHXe9QuJtq_ys30PNfTGnMsRuHuo_MUzGCg.jpg)

# 谵妄开始了

不久前，我想知道是否有可能在 JavaScript 中的实体之间创建直接的反应关系。
换句话说，我希望能够编写由下面的伪代码表示的概念代码:

```
// create a source
const $source = { foo: 42 };

// create a reactive primitive that will be updated
// when the 'foo' field of the source changes
rx primitive = $source.foo;
primitive; // 42

// create a reactive object that will be updated
// when changes happen into the source
rx object = $source;
object; // { foo: 42 };

// change the 'foo' field source
$source.foo = 'hi';

// the reactive primitive was updated
primitive; // 'hi'
// the reactive object as well
object; // { foo: 'hi' }

// change the whole source
$source = { foo: { bar: 'baz' } };

// the reactive object was updated
object; // { foo: { bar: 'baz' } }
// the reactive primitive too, but unfortunately is no more a primitive
primitive; // { bar: 'baz' } 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的反应性有什么意义，我喜欢定义**封装反应性**或**低水平反应性**？
它有助于观察结构内部的变化，这些变化很容易成为事件驱动应用程序中的事实来源。

让我们来谈谈 VueJS 的计算属性，我从中获得了灵感来构建 OxJS 的原始反应性系统。我不打算在这里解释 VueJS 反应系统，但我可以链接[这个有趣的视频系列](https://www.vuemastery.com/courses/advanced-components/the-introduction)，它包含了许多有用的信息。
对于每个 Vue 组件，我们可以考虑内部**数据**对象和组件的父组件传递给它的**道具**的联合作为真实的来源:

```
export default {
    data() {
        // the internal data object
        return {
            age: 22
        }
    },

    props: {
        // a numerical multiplier prop
        multiplier: {
            type: Number,
            default: 1,
        }
    },

    // here the magic
    computed: {
        result() {
            return this.multiplier * this.age + 1;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

计算属性是一种特殊类型的属性，它是通过将组件的其他属性与一些自定义逻辑相结合而得到的。在我们的例子中，`result`计算属性将成为使用`multiplier`属性和`age`数据属性的数学运算的结果。
每次这些变化之一，`result`属性将被重新计算，因此得名，正确地更新。

对于 VueJS 开发者来说，计算属性非常有用，而且不可替代；每个 VueJS 组件的命脉，因为它们使反应变得轻而易举。

所以我问自己:为什么不试着从 VueJS 中提取这种类型的反应呢？几天后 **OxJS** 诞生了。

# 史诗音乐

**[OxJS](https://github.com/jfet97/oxjs)** 是用 TS 编写的概念验证库，支持封装的反应性。

让我们试着创建一个可观察和可反应的对象:

```
const { ox } = require('oxjs');

// it creates an observable source
const $source = ox.observable({
    years: 32,
});

// it creates a reactive object
const reactiveObject = ox.observer(() => {
    // we are referencing the observable here
    const years = $source.years;

    // an object is returned
    // so we are creating a reactive object
    return {
        age: years,
    }
});

// initial evaluation
console.log(reactiveObject); // { age: 32 }

// we change the stored 'years' inside $source
$source.years = 28;

// the 'reactiveObject' is updated
console.log(reactiveObject); // { age: 28 } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，可观察对象和观察者的创建非常简单；此外，后者会尽快得到通知。显然，我们并不局限于一个观测者对应一个可观测值:一个可观测值可以被任意数量的观测者使用，一个观测者可以使用它需要的多少个可观测值。

# 反应原语呢？

就是这里:

```
const { ox } = require('oxjs');

// it creates two observable sources
const $source1 = ox.observable({
    years: 32,
});

const $source2 = ox.observable({
    name: 'Mario',
});

// it creates an observer that will behave as a string
const stringObserver = ox.observer(() => `My name is ${$source2.name} and I'm ${$source1.years} years old`);

// initial evaluation
console.log(`${stringObserver}`); // My name is Mario and I'm 32 years old

// we change the stored 'years' inside $source1
$source1.years = 28;

// the 'stringObserver' is updated
console.log(`${stringObserver}`); // My name is Mario and I'm 28 years old

// we change the stored 'name' inside $source2
$source2.name = 'Luigi';

// the 'stringObserver' is updated
console.log(`${stringObserver}`); // My name is Luigi and I'm 28 years old 
```

Enter fullscreen mode Exit fullscreen mode

正如你将看到的，我的反应式原语并不完美，因为它们不是真正的原语。否则我的 API 将无法更改它们所引用的值。事实上，由于 JS lang 本身的原因，它们基于原语包装器和一个奇怪的 ES6 代理黑客，这个黑客有一些限制。

[![](img/5e42a3dca93a5d6168168901dee3a920.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7uf52MSg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/wSLcr7s/limited-JS.jpg)

但是如果你稍微注意一下，它们适用于广泛的情况。大多数时候你不会感觉到不同。我希望如此。

# 引擎盖下是怎么回事？

我无法解释我自豪地写下的所有反应式黑客，但我可以尝试描述当一个反应式实体需要更新时会发生的奇迹。

让我们假设这是我们真理的来源:

```
const $source = ox.observable({
    city: 'Carrara',
    country: 'Italy',
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们质疑下面这个奇怪的观察者，他可能是`string`也可能是`object` :

```
let weirdObserver = ox.observer(() => {
    const city = $source.city;
    const country = $source.country;

    if (city && city.length > 5) {
        return `${city} is located in ${country}`;
    } else {
        return {
            city,
            country
        }
    }
});

// initial evaluation
console.log(`${weirdObserver}`); // "Carrara is located in Italy" 
```

Enter fullscreen mode Exit fullscreen mode

知道每次使用的`$source`字段之一改变时，传递给`ox.observe`的箭头函数被调用，主要问题是:如何改变`weirdObserver`标识符所指向的内容？
如果不依赖闭包，就会导致人们认为使用`ox.observe`和 **OxJS** 的方式发生变化，因此 JS 中没有办法。

因此，如果我们不能移动`weirdObserver`标识符，我们甚至不能将它设置为一个原语，因为在 JS 中，如果一个原语值存储在其中，两个不同的标识符不能指向同一个内存区域。

等等，为什么我们需要另一个参考？

因为如果`weirdObserver`是不可移动的，我们需要对它所指向的同一事物的另一个引用——这个引用将以某种方式由 **OxJS** 存储——来执行更改，所以可以说`weirdObserver`也“看到它们”。

总结一下到目前为止所说的话:

*   不允许使用原语(这就是我使用原语包装器的原因)
*   `weirdObserver`不可移动，永远指向同一个物体
*   还有另一个对由`weirdObserver`指向的相同对象的引用，用于执行更改

现在另一个问题出现了:那个物体应该能够完全改变它的性质。如果它应该从一个数组转换成一个数字包装器，然后从一个地图转换成一个承诺呢？是的，我夸大其词了，但我相信你现在已经抓住了要点:没有合并策略或原型黑客可以帮助我们。

那又怎样？ES6 代理！

我发现的解决方案是返回一个几乎完全透明的代理，在这个代理上，目标在运行时是动态变化的。通常这是不可能的，除非所有陷阱都被重定向。我知道，这是一个又大又丑的变通办法。但我能够让黑客在大多数情况下具有弹性。

[![](img/66b46b34fa285f4ba3dafbad949f84a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kvt5qe3j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.memecdn.com/trust-me-i-amp-039-m-an-engineer_o_1897979.jpg)

回到我们的例子，`weirdObserver`引用的代理最初将有一个字符串包装器作为目标。每次源改变时，即使没有超过长度限制，代理的目标也会改变。

如果新的`$source.city`值的长度大于 5，新的目标将是一个新的字符串包装器，否则将是一个新的`{ city, country }`对象。由于代理的性质，客户，也就是使用`weirdObserver`标识符的客户，将能够在前一种情况下使用所有`String.prototype`的方法，并在后一种情况下执行几乎所有允许的操作。

# oxj 可以做的其他事情

我懒得创建 README 的克隆版本。请点击查看[。](https://github.com/jfet97/oxjs)

# 结论

正如我所说，这个库只是一个概念的证明，IDK，如果没有一个框架来保护我们，这种反应可能适合于实际应用。如果被滥用，也许它的“隐含”性质会很快产生问题。我想听听你对此的看法。

此外，有时我喜欢仅仅为了乐趣而开发，而不太担心干净和有效的代码，也不考虑我所创造的真实用例。这是其中的一个例子。我把更多的精力放在了提升我的 TS 技能上(很明显，我不得不为此付出很多，但收效甚微😂)以及达到我的主要目标:把反应能力提升到更高的水平。