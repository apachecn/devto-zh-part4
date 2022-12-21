# 将学习 JS 基本方法作为游戏任务

> 原文：<https://dev.to/ljzn/make-learning-js-basic-methods-as-game-tasks-4ipi>

当我开始学习 JavaScript 时，我经常对搜索文档感到厌倦。我花了很多时间在标准库中寻找一个简单的函数。直到有一天，我再也不能忍受在这上面浪费时间了。我想，为什么不把它们都记在脑子里呢？

但是记住 std lib 中的函数是一项枯燥的工作。为了增加这样做的乐趣，我编写了一个简单的脚本，它将生成一个 todo 列表，其中包含 js 方法名和到该方法的 MDN 文档的链接。

```
const FundamentalObjects = [
    Object,
    Function,
    Boolean,
    Symbol,
    Error
]

const NumbersAndDates = [
    Number,
    BigInt,
    Math,
    Date
]

const TextProcessing = [
    String,
    RegExp
]

const IndexedCollections = [
    Array,
    Int8Array,
    Uint8Array,
    Uint8ClampedArray,
    Int16Array,
    Uint16Array,
    Int32Array,
    Uint32Array,
    Float32Array,
    Float64Array,
    BigInt64Array,
    BigUint64Array
]

const KeyedCollections = [
    Map,
    Set,
    WeakMap,
    WeakSet
]

const StructuredData = [
    ArrayBuffer,
    SharedArrayBuffer,
    Atomics,
    DataView,
    JSON
]

const ControlAbstractionObjects = [
    Promise,
    // Generator,
    // GeneratorFunction,
    // AsyncFunction 
]

const Reflection = [
    Reflect,
    Proxy
]

const Internationalization = [
    Intl,
    Intl.Collator,
    Intl.DateTimeFormat,
    Intl.ListFormat,
    Intl.NumberFormat,
    Intl.PluralRules,
    Intl.RelativeTimeFormat,
    Intl.Locale
]

const WASM = [
    WebAssembly,
    WebAssembly.Module,
    WebAssembly.Instance,
    WebAssembly.Memory,
    WebAssembly.Table,
    WebAssembly.CompileError,
    WebAssembly.LinkError,
    WebAssembly.RuntimeError
]

const ObjectsToLearn =
    FundamentalObjects
    .concat(NumbersAndDates)
    .concat(TextProcessing)
    .concat(IndexedCollections)
    .concat(KeyedCollections)
    .concat(StructuredData)
    .concat(ControlAbstractionObjects)
    .concat(Internationalization)
    .concat(WASM)

const listMethodsOf = (object) => {
    try {
        return Object.getOwnPropertyNames(object.prototype)
            .filter(f => !['caller', 'callee', 'arguments'].includes(f))
            .filter(f => {
                try {
                    let bool = typeof(object['prototype'][f]) == 'function'
                    return bool
                } catch (e) {
                    return false
                }
            })
    } catch (e) {
        return []
    }
}

let r =
    ObjectsToLearn
    .filter(o => o.name)
    .map(o => '# ' + o.name + '\n' +
        listMethodsOf(o)
        .map(f => '- [ ] [' + f + '](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/' + o.name + '/' + f + ')')
        .join('\n')
    )
    .join('\n')

console.log(r) 
```

Enter fullscreen mode Exit fullscreen mode

然后复制控制台中的内容，粘贴你的要点，你就得到一个 JavaScript(浏览器)中所有主对象方法的可点击的待办事项列表。可以在这里访问结果:[https://gist . github . com/Ljzn/d 93 da E3 de 64660 e 66 be 598 ee 88 fa 57 be](https://gist.github.com/Ljzn/d93dae3de64660e66be598ee88fa57be)。

编码快乐！

2019.08.06 更新:

我把代码改成了两个函数:“listOfJSObjectsDoc”和“listOfWebAPIDoc”。

```
const listMethodsOf = (object) => {
    try {
        return Object.getOwnPropertyNames(object.prototype)
            .filter(f => !['caller', 'callee', 'arguments'].includes(f))
            .filter(f => {
                try {
                    let bool = typeof(object['prototype'][f]) == 'function'
                    return true
                } catch (e) {
                    return false
                }
            })
    } catch (e) {
        return []
    }
}

const listOfWebAPIDoc = modules =>
    toDoc(modules, 'https://developer.mozilla.org/en-US/docs/Web/API/')

const listOfJSObjectDoc = modules =>
    toDoc(modules, 'https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/')

const toDoc = (modules, baseURL) =>
    modules
    .filter(o => o.name)
    .map(o => '# ' + o.name + '\n' +
        listMethodsOf(o)
        .map(f => `- [ ] [${f}](${baseURL}${o.name}/${f})`)
        .join('\n')
    )
    .join('\n') 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
let modules = [MediaStream, MediaStreamTrack]

console.log(listOfWebAPIDoc(modules)) 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 https://jsbin.com/dujeril/1/edit?js,console 尝试一下。

编码快乐！