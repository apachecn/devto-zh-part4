# 我对 JS 中枚举的看法

> 原文：<https://dev.to/jaredkent/my-take-on-enums-in-js-1g50>

你有没有发现自己写了这样的东西？

```
const PERIODS = {
  week: 'week',
  day: 'day',
  hour: 'hour',
  minute: 'minute'
};

// OR

const STATUSES = {
  pending: 0,
  inReview: 1,
  approved: 2,
  rejected: 3
}; 
```

我发现自己一直在使用这种模式，并在我工作过的大量代码库中看到了这种模式。需要一组相关的*常量*值是很常见的，事实上，很多语言都有一个专门描述它们的结构:枚举。遗憾的是，JavaScript 不是这些语言中的一种。这就是为什么我们只剩下一个半心半意的选择，这些“恒定对象”的定义。也许有一天，我们可能会将枚举作为一种内置的语言特性( [enum 实际上是 JavaScript 中的一个保留字](http://www.javascripter.net/faq/reserved.htm))，但现在我们只剩下我认为的:一个缺乏的替代物。

尽管我贬低了这种“恒定对象”模式，但我没有解释为什么它们缺乏，或者为什么枚举解决了它们的任何问题。在 JavaScript 中，当用`const`指定我们的对象定义时，我们只是被阻止重新分配变量，而不是实际上被阻止完全改变那个变量。如果我使用`const`定义了某个对象`x`，我可以稍后去修改它的一个属性，添加一个新的，或者完全`delete`它。当涉及到**真实常数**值时，这并不理想。我们希望避免程序中的某个地方有人进来拿走了我们的“待定”状态，导致各种不良行为。虽然安全是 enums 最重要的特性，但我也相信我们的“常量对象”有点长，可以使用一些*语法糖*使它们更好一些(因为最终我们每天都要编写这种代码)。所以我决定尝试在我的代码中实现类似 enum 的行为。

> 警告:我不使用 typescript，它们有很好的枚举，正是我所说的。然而，我并不总是有机会编写 typescript，许多人也没有，所以我想要一些可以在任何 JS 项目中使用的东西。

当我在查看其他语言中的枚举并幻想在 JavaScript 中使用它们时，我看到了由 [Ron Buckton](https://twitter.com/rbuckton) 提出的在 ECMAScript 中使用枚举的建议[(这个人是从事 TypeScript 工作的高级工程师，所以我认为他对枚举略知一二)。我真的很喜欢这个提议，它植根于 Java、C++、C#和 TypeScript 的枚举，并且对功能有非常清晰的定义。我非常喜欢的一个特性是他的提议的核心，那就是“自动初始化”和“自动初始化器”的使用。这意味着你可以通过简单地指定你想要使用的初始化器来决定赋予你的枚举的值，就像这样:](https://github.com/rbuckton/proposal-enum) 

```
enum Colors of Number {
  red,
  green,
  blue
}

enum PlayState of String {
  idle,
  running,
  paused
} 
```

这似乎是消除“常量对象”模式中使用的笨拙的对象键/值语法的完美方法。与其直接指定枚举的值，还不如隐含指定。现在很明显，我能编写的任何工具都不具备提案中所有的专业语法的细微之处(如果我不花很大力气去编写一个成熟的巴别塔插件之类的东西)。所以我决定采用半函数式的方法，我可以传入初始化函数来设置我的枚举创建器，然后将我的枚举定义传入那个创建器。让我们看看我最初的例子在我设计的语法中是什么样子的:

```
const PERIODS = enums(string)('week', 'day', 'hour', 'minute');
const STATUSES = enums(number)('pending', 'inReview', 'approved', 'rejected'); 
```

这种格式赋予了该工具强大的功能和发展潜力。传递给`enums`的*自动初始化器*函数(像`string`和`number`)被设计成像你传递给`Array.prototype.map`的映射函数一样工作。只要初始化器为传递给它的每个枚举返回一个值，工具就会用那个值创建一个枚举。一个初始化函数被提供了当前的枚举值和先前分配的值:`function initializer(currentEnum[, previousValue]) {}`这允许你基于最后一个值修改给定的枚举或增量。希望这个初始化器 API 足够健壮，允许大量的定制，所以你的特定用例可以被打包和重用。`number`和`string`自动初始化器都被捆绑到核心包中。下面是`string`和`number`的实现:

```
function string(en) {
  return en; // Super simple, we just return the given enum string
}

// number is the default initializer, if you don't pass anything in to `enums` it uses this function
function number(en, prevVal) {
  return prevVal !== undefined && prevVal !== null ? prevVal + 1 : 0;
} 
```

为了展示一个定制的例子，返回给定枚举键的大写字符串值的初始化器可能看起来像这样:

```
function capitalize(enm) {
  return enm.charAt(0).toUpperCase() + enm.slice(1);
} 
```

显然，并不是每种情况都是一成不变的，有时我们需要为每个枚举定制值，但这些值并不完全基于枚举字符串。为了处理这个问题，该工具提供了对*覆盖语法*的支持，以允许直接指定值，而不是依赖于自动初始化。这最终需要用户传入一个完整的 JS 对象来构造枚举:

```
const COLORS = enums()({ red: '#f44242', green: '#27c65a', blue: '#003bff' }); 
```

这时你可能会问:“如果我只是想写一个对象，为什么要用这个工具呢？现在打字就更长了！”。这就是安全的天然优势所在，让我们深入研究一下实现。

## 枚举实现

下面是`enums`工具的实现，只有 39 行:

```
function enums(initializer = number) {
  function generator(...args) {
    if (!args || !args.length) return undefined;
    const enums = args.length > 1 ? args : args[0];
    let en = {};
    let pv;

    if (Array.isArray(enums)) {
      for (let val of enums) {
        const v = initializer(val, pv);
        pv = v;
        en[val] = v;
      }
    } else if (typeof enums === "object") {
      for (let val of Object.entries(enums)) {
        const key = val[0];
        const value = val[1];
        if (!value || Array.isArray(value) || typeof value === "object") {
          const v = initializer(key, pv);
          pv = v;
          en[key] = Object.freeze(v);
        } else {
          en[key] = Object.freeze(value);
        }
      }
    }

    giveMethods(en); // Not showing this method definition for conciseness, check out Github for the 'full' source

    return Object.freeze(en);
  }

  return generator;
} 
```

你可能已经注意到了 [Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) 的使用，这是我们试图保证 enum 实现的不变性和安全性的方法。`Object.freeze`将通过"[失败]或者静默或者抛出一个 TypeError 异常" * "来防止从我们的底层 JS 对象中添加或移除属性。它还将防止值被更改，“可写和可配置的属性被设置为假...”*.使用 freeze 和 const，我们能够模拟对象的不变性，以确保我们的枚举不会被我们或其他开发人员修改。冻结也有它的局限性，它只能冻结一个物体中的一层。在实现中，不仅顶级“枚举”对象被冻结，而且任何自定义的值也在顶级被冻结。这意味着，如果您将枚举值赋给嵌套对象，嵌套对象仍有变异的危险。确保这样做时，在将对象分配给枚举之前“深度冻结”它们。深度冻结被排除在这个实现之外，因为深度嵌套对象的使用不是我经常看到的模式，但是我想确保向任何可能有这个用例的人指出这一点。

> *引用`Object.freeze`上链接的 MDN 文档

除了不变性的安全性，Ron Bucktons enum 提案还定义了一组 enum 类型上允许的方法。

```
let Enum: {
  keys(E: object): IterableIterator<string | symbol>;
  values(E: object): IterableIterator<any>;
  entries(E: object): IterableIterator<[string | symbol, any]>;
  has(E: object, key: string | symbol): boolean;
  hasValue(E: object, value: any): boolean;
  getName(E: object, value: any): string | undefined;
  format(E: object, value: any): string | symbol | undefined;
  parse(E: object, value: string): any;
  create(members: object): object;
  flags(descriptor: EnumDescriptor): EnumDescriptor;
}; 
```

与其在包中包含一个全局的`Enum`对象，不如直接在你的枚举:
中调用这些方法

```
const PERIODS = enums()('week', 'day', 'hour', 'minute'); // auto-initialized to 'number'

PERIODS.keys() // ['week', 'day', 'hour', 'minute']
PERIODS.values() // [0, 1, 2, 3]
PERIODS.entries() // [['week', 0], ['day', 1], ['hour', 2], ['minute', 3]]
PERIODS.has('month') // false
PERIODS.hasValue(0) // true
PERIODS.getName(0) // 'week' 
```

在创建的 enum 上包含这些方法(并打破建议格式)的最初原因是为了便于使用，因为不能为`Enum`创建真正的**全局**对象，所以当您需要这些帮助函数时，也必须导入它们。我仍然不确定这是否是 tools API 的正确选择，希望随着继续使用和一些社区测试，这种实现可以最终成为最适合其用户的 API。

## 结论

我喜欢枚举，真的希望我们可以在 JavaScript 中拥有一个真正的枚举，而不必使用 TypeScript。然而，现在这个小工具将举行我，我希望它也激起你的兴趣！你可以用 npm 或 yarn 安装[fun-enum](https://www.npmjs.com/package/fun-enums)，并在你的项目中使用它。这个包仅仅是一个 **807 字节**的压缩文件，具有**零**依赖性，并且测试了超过 **95%** 的代码覆盖率！欢迎在 github 知识库上留下评论或问题，感谢阅读！