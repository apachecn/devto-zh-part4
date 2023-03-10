# 将 React 挂钩与 Vue 组合 API 进行比较

> 原文：<https://dev.to/voluntadpear/comparing-react-hooks-with-vue-composition-api-4b32>

Vue 最近推出了 [Composition API RFC](https://vue-composition-api-rfc.netlify.com) ，这是一个新的 API，用于编写受 React Hooks 启发的 Vue 组件，但有一些有趣的区别，我将在本文中讨论。这个 RFC 从[开始，之前的版本叫做基于功能的组件 API](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md) ，受到了[来自社区某些部分的大量批评](https://github.com/vuejs/rfcs/pull/42)，因为担心 Vue 开始变得更加复杂，不像人们一开始喜欢的简单库。

Vue 核心团队解决了围绕第一个 RFC 的困惑，这个新的 RFC 提出了一些有趣的调整，并对提议的更改背后的动机提供了进一步的见解。如果你有兴趣给 Vue 核心团队一些关于新提议的反馈，你可以在 GitHub 上参与[讨论](https://github.com/vuejs/rfcs/pull/78)。

**注意:Vue 组合 API 是一项正在进行中的工作，将来可能会有变化。在 Vue 3.0 到来之前，关于 Vue 组合 API 没有什么是 100%确定的。**

React [钩子](https://reactjs.org/docs/hooks-intro.html)允许你“挂钩”React 功能，比如组件状态和副作用处理。钩子只能在函数组件内部使用，它允许我们在不需要为组件创建类的情况下，为组件添加状态、副作用处理等等。自从 2018 年[他们的介绍](https://youtu.be/dpw9EHDh2bM?t=690)，这个社区立刻爱上了他们。

React 核心团队准备的采用策略是不弃用类组件，这样您就可以更新 React 版本，开始尝试新组件并保留现有组件而不做任何修改。

因此，让我们开始研究 React Hooks 和 Vue Composition API 的不同方面，并注意我们可能会发现的某些差异⏯

## 反应钩

示例:

```
import React, { useState, useEffect } from "react";

const NoteForm = ({ onNoteSent }) => {
  const [currentNote, setCurrentNote] = useState("");
  useEffect(() => {
    console.log(`Current note: ${currentNote}`);
  });
  return (
    <form
      onSubmit={e => {
        onNoteSent(currentNote);
        setCurrentNote("");
        e.preventDefault();
      }}
    >
      <label>
        <span>Note: </span>
        <input
          value={currentNote}
          onChange={e => {
            const val = e.target.value && e.target.value.toUpperCase()[0];
            const validNotes = ["A", "B", "C", "D", "E", "F", "G"];
            setCurrentNote(validNotes.includes(val) ? val : "");
          }}
        />
      </label>
      <button type="submit">Send</button>
    </form>
  );
}; 
```

`useState`和`useEffect`是 React 钩子的一些例子。它们允许它在功能组件中添加状态和运行副作用。我们将在后面看到更多的挂钩，你甚至可以创建自定义的挂钩。这为代码的可重用性和可扩展性开辟了新的可能性。

## 视图组成 API

示例:

```
<template>
  <form @submit="handleSubmit">
    <label>
      <span>Note:</span>
      <input v-model="currentNote" @input="handleNoteInput">
    </label>
    <button type="submit">Send</button>
  </form>
</template>

<script>
import { ref, watch } from "vue";
export default {
  props: ["divRef"],
  setup(props, context) {
    const currentNote = ref("");
    const handleNoteInput = e => {
      const val = e.target.value && e.target.value.toUpperCase()[0];
      const validNotes = ["A", "B", "C", "D", "E", "F", "G"];
      currentNote.value = validNotes.includes(val) ? val : "";
    };
    const handleSubmit = e => {
      context.emit("note-sent", currentNote.value);
      currentNote.value = "";
      e.preventDefault();
    };

    return {
      currentNote,
      handleNoteInput,
      handleSubmit,
    };
  }
};
</script> 
```

Vue Composition API 以一个名为`setup`的新组件选项为中心。它提供了一组新的函数，用于向我们的 Vue 组件添加状态、计算属性、观察器和生命周期挂钩。

这个新 API 不会让原来的 API(现在称为“基于选项的 API”)消失。该提议的当前版本允许开发者甚至将两个组件 API 结合在一起。

*注意:你可以在 Vue 2.x 中使用 [@vue/composition-api](https://github.com/vuejs/composition-api) 插件来尝试一下。*

## 代码的执行

Vue 组合 API 的`setup`函数在`beforeCreate`钩子之后(在 Vue 中,“钩子”是一个生命周期方法)和`created`钩子之前被调用。这是 React 钩子和 Vue Composition API 之间的第一个区别， **React 钩子在每次组件渲染时运行，而 Vue `setup`函数在创建组件**时只运行一次。因为 React 钩子可以运行多次，所以渲染函数必须遵循某些规则，其中之一是:

> 不要在循环、条件或嵌套函数中调用钩子。

这里有一个直接来自 React docs 的代码示例来演示这一点:

```
function Form() {
  // 1\. Use the name state variable
  const [name, setName] = useState('Mary');

  // 2\. Use an effect for persisting the form
  if (name !== '') {
    useEffect(function persistForm() {
      localStorage.setItem('formData', name);
    });
  }
  // 3\. Use the surname state variable
  const [surname, setSurname] = useState('Poppins');

  // 4\. Use an effect for updating the title
  useEffect(function updateTitle() {
    document.title = `${name}  ${surname}`;
  });

  // ...
} 
```

React 在内部跟踪我们在组件中使用的所有钩子。在这个例子中，我们使用了四个钩子。注意第一次`useEffect`调用是如何有条件地完成的，因为在第一次渲染时`name`状态变量将被赋予默认值`'Mary'`，条件将被评估为`true`，React 将知道它需要按顺序跟踪所有这四个钩子。但是如果在另一个渲染上`name`是空的会怎么样呢？那么，在这种情况下，React 不知道在第二个`useState`钩子调用时返回什么😱。为了避免这一问题和其他问题，强烈推荐使用 React 挂钩时使用一个 [ESLint 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)，它默认包含在 [Create React App](https://github.com/facebook/create-react-app) 中。

如果我们只是想运行效果，如果`name`不是空的呢？我们可以简单地将它移动到`useEffect`回调:

```
useEffect(function persistForm() {
  if (name !== '') {
    localStorage.setItem('formData', name);
  }
}); 
```

回到 Vue，类似于前面例子的东西应该是这样的:

```
export default {
  setup() {
    // 1\. Use the name state variable
    const name = ref("Mary");
    // 2\. Use a watcher for persisting the form
    if(name.value !== '') {
      watch(function persistForm() => {
        localStorage.setItem('formData', name.value);
      });
    }
   // 3\. Use the surname state variable
   const surname = ref("Poppins");
   // 4\. Use a watcher for updating the title
   watch(function updateTitle() {
     document.title = `${name.value}  ${surname.value}`;
   });
  }
} 
```

由于`setup`方法将只运行一次，我们可以利用组成 API 的不同函数(`reactive`、`ref`、`computed`、`watch`、生命周期挂钩等)。)作为循环或条件语句的一部分。然而，`if`语句也只会运行一次，所以它不会对`name`的变化做出反应，除非我们将它包含在`watch`回调:
中

```
watch(function persistForm() => {
  if(name.value !== '') {
    localStorage.setItem('formData', name.value);
  }
}); 
```

## 宣告状态

`useState`是用 React 钩子声明状态的主要方式。您可以将初始值作为参数传递给调用，如果初始状态的计算开销很大，您可以将其表示为一个只在初始渲染期间执行的函数。

```
const [name, setName] = useState("Mary");
const [age, setAge] = useState(25);
console.log(`${name} is ${age} years old.`); 
```

它返回一个数组，第一个元素是 state，第二个元素是 setter 函数。通常，你使用[数组析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring)来抓取它们。

一个方便的替代方法是使用`useReducer`,它接受一个 Redux-like reducer 和一个更常见变体的初始状态。还有一个变种是[惰性初始化](https://reactjs.org/docs/hooks-reference.html#lazy-initialization) :

```
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}
const [state, dispatch] = useReducer(reducer, initialState); 
```

然后，您可以像使用`dispatch({type: 'increment'});`一样使用`dispatch`功能。

Vue 的工作方式因其反应性质而不同。您有两个主要的函数来声明状态:`ref`和`reactive`。

`ref`返回一个 reactive 对象，其中包含的内部值由它的`value`属性访问。你可以对原始值或对象使用`ref`,在对象的情况下，它们是深度反应的。

```
const name = ref("Mary");
const age = ref(25);
watch(() => {
  console.log(`${name.value} is ${age.value} years old.`);
}); 
```

`reactive`另一方面，只能接受一个对象作为它的输入，并返回它的一个反应代理。请注意，反应性会影响所有嵌套属性。

```
const state = reactive({
  name: "Mary",
  age: 25,
});
watch(() => {
  console.log(`${state.name} is ${state.age} years old.`);
}); 
```

RFC 有一个比较`ref`和`reactive`的[全段](https://vue-composition-api-rfc.netlify.com/#ref-vs-reactive)。最后总结了使用它们的可能方法:

> 1.  Use ref and reactive, just like declaring basic type variables and object variables in ordinary JavaScript. When using this style, it is recommended to use a type system that supports IDE.
>     
>     
> 2.  Use reactive as much as possible, and remember to use toRefs when returning reactive objects from composite functions. This reduces the mental burden of referees, but it does not eliminate the need to be familiar with concepts.

使用`ref`时要记住的是，你需要记住通过 ref 的`value`属性访问包含的值(除了在模板中，Vue 允许你省略它)。而`reactive`如果你破坏了物体，你会失去反应。因此，您需要有一个对对象的引用，并通过它访问您定义的状态属性。

Composition API 提供了两个助手函数来处理 refs 和 reactive 对象。如果需要的话，可以使用`isRef`来有条件地获取`value`属性(例如`isRef(myVar) ? myVar.value : myVar`),`toRefs`将一个反应性对象转换为一个普通对象，其中它的所有属性都自动转换为引用。当从自定义组合函数返回时特别有用(因此允许从调用方使用析构并保持反应性)。

```
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2
  })

  return toRefs(state)
}

const {foo, bar} = useFeatureX(); 
```

## 如何跟踪依赖关系

React 中的`useEffect`钩子允许我们在每次渲染后运行某些副作用(比如订阅、获取数据或使用 Web APIs，比如存储),并在下次执行回调之前或组件卸载时可选地运行一些清理。默认情况下，所有`useEffect`注册的函数将在每次渲染后运行，但是我们可以定义实际的状态和属性依赖关系，这样如果相关的依赖关系没有改变，React 将跳过某个`useEffect`钩子的执行(例如，一次渲染是因为另一次状态更新)。回到我们之前的`Form`例子，我们可以传递一个依赖数组作为`useEffect`钩子的第二个参数:

```
function Form() {
  const [name, setName] = useState('Mary');
  const [surname, setSurname] = useState('Poppins');
  useEffect(function persistForm() {
      localStorage.setItem('formData', name);
  }, [name]);

  // ...
} 
```

这样，只有当`name`改变时，我们才会更新`localStorage`。React 钩子的一个常见错误是忘记在 dependencies 数组中彻底声明所有的依赖项。你可能会导致你的`useEffect`回调函数没有用最新的依赖项更新，而是引用了以前渲染的旧值。幸运的是， [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) 包含了一个 lint 规则，可以警告缺少的依赖项。

`useCallback`和`useMemo`也使用依赖关系数组参数来决定它们是否应该分别返回与上次执行相同的回调或值的记忆版本。

在 Vue Composition API 的情况下，我们可以使用`watch`函数来执行响应道具或状态变化的副作用。由于 Vue 的反应系统，依赖性将被自动跟踪，并且当依赖性改变时，注册的函数将被反应性地调用。回到我们的例子:

```
export default {
  setup() {
    const name = ref("Mary");
    const lastName = ref("Poppins");
    watch(function persistForm() => {
      localStorage.setItem('formData', name.value);
    });
  }
} 
```

在我们的观察器第一次运行之后，`name`将作为一个依赖项被跟踪，当它的值在以后发生变化时，观察器将再次运行。

## 访问组件的生命周期

在处理 React 组件的生命周期、副作用和状态管理时，钩子代表了心智模型的完全转换。React 社区的活跃成员 Ryan Florence[表达了从类组件到挂钩](https://twitter.com/ryanflorence/status/1125041041063665666)的心理转变，正如 React 文档所指出的:

> 如果你熟悉 React 类生命周期方法，你可以把`useEffect`钩子看作是`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合。

然而，我们可以控制`useEffect`何时开始运行，让我们更接近生命周期中运行副作用的心理模型:

```
useEffect(() => {
  console.log("This will only run after initial render.");
  return () => { console.log("This will only run when component will unmount."); };
}, []); 
```

但是再一次，当使用 React 钩子时，更习惯的做法是停止从生命周期方法的角度考虑，而是考虑我们的效果依赖于什么状态。顺便说一下，Svelte 的创始人 Rich Harris 在纽约 React meetup 上发布了一些有见地的幻灯片，其中他探讨了 React 为实现未来的新功能(例如并发模式)而做出的妥协，以及 Svelte 与此有何不同。它将帮助你理解从考虑副作用发生的组件生命周期到*副作用成为渲染本身的一部分*的转变。来自 React 核心团队[的 Sebastian Markbå ge 在此](https://gist.github.com/sebmarkbage/a5ef436427437a98408672108df01919)进一步阐述了 React 的发展方向，以及与 Svelte 或 Vue 等反应系统的妥协。

另一方面，Vue 组件 API 仍然允许我们访问带有`onMounted`、`onUpdated`和`onBeforeUnmount`等
的[生命周期挂钩](https://vue-composition-api-rfc.netlify.com/api.html#lifecycle-hooks)(生命周期方法在 Vue 世界中的等价名称)

```
setup() {
  onMounted(() => {
    console.log(`This will only run after initial render.`); 
  });
  onBeforeUnmount(() => {
    console.log(`This will only run when component will unmount.`);
  });
} 
```

所以在 Vue 的例子中，思维模式的转变是停止思考通过哪些组件选项(`data`、`computed`、`watch`、`methods`、生命周期挂钩等来组织代码。)它们属于这样一种类型，在这种类型中，您可以拥有不同的功能，每个功能处理一个特定的功能。RFC 包括一个[全面的例子，以及通过选项组织和通过逻辑关注组织的比较](https://vue-composition-api-rfc.netlify.com/#code-organization)。React 钩子也有这个好处，并且从一开始就受到了社区的好评。

## 自定义代码

React 团队希望用钩子关注的一个方面是为开发人员提供一种比以前社区采用的替代方案更好的编写可重用代码的方式，如[高阶组件](https://reactjs.org/docs/higher-order-components.html)或[渲染道具](https://reactjs.org/docs/render-props.html)。[定制挂钩](https://reactjs.org/docs/hooks-custom.html)是他们想出的答案。

自定义钩子只是普通的 JavaScript 函数，在其中使用了 React 钩子。他们遵循的一个惯例是，他们的名字应该以`use`开头，这样人们一眼就能看出它是用来做钩子的。

```
export function useDebugState(label, initialValue) {
  const [value, setValue] = useState(initialValue);
  useEffect(() => {
    console.log(`${label}: `, value);
  }, [label, value]);
  return [value, setValue];
} 
```

当
的值发生变化时，这个小小的定制钩子示例可以在登录到控制台时替代`useState`

```
const [name, setName] = useDebugState("Name", "Mary"); 
```

在 Vue 中，复合函数相当于钩子，具有相同的逻辑提取和可重用性目标。事实上，我们可以在 Vue 中有一个类似的`useDebugState`合成函数:

```
export function useDebugState(label, initialValue) {
  const state = ref(initialValue);
  watch(() => {
    console.log(`${label}: `, state.value);
  });
  return state;
}

// elsewhere:
const name = useDebugState("Name", "Mary"); 
```

*注意:按照惯例，复合函数也像 React Hooks 一样使用`use`作为前缀，以表明它是一个复合函数，并且它属于`setup`*

## 参

React `useRef`和 Vue `ref`都允许你引用一个子组件(在 React 中是一个类组件或者用`React.forwardRef`包装的组件)或者你附加的 DOM 元素。

反应:

```
const MyComponent = () => {
  const divRef = useRef(null);
  useEffect(() => {
    console.log("div: ", divRef.current)
  }, [divRef]);

  return (
    <div ref={divRef}>
      <p>My div</p>
    </div>
  )
} 
```

视图:t0]

```
export default {
  setup() {
    const divRef = ref(null);
    onMounted(() => {
      console.log("div: ", divRef.value);
    });

    return () => (
      <div ref={divRef}>
        <p>My div</p>
      </div>
    )
  }
} 
```

注意，在 Vue 的情况下，`@vue/composition-api` Vue 2.x 插件不支持在`setup()` [返回的渲染函数上用 JSX 分配模板引用，但是根据](https://github.com/vuejs/composition-api#template-refs)[当前 RFC](https://vue-composition-api-rfc.netlify.com/api.html#template-refs) ，上述语法在 Vue 3.0 中将有效。

然而,`useRef` React 钩子不仅对访问 DOM 元素有用。您可以将它用于任何类型的可变值，这些值是您希望在渲染之间保留的，但不是您的状态的一部分(因此当它们发生变化时不会触发重新渲染)。你可以把它们想象成类组件中的[【实例变量】](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)。下面是一个例子:

```
const timerRef = useRef(null);
useEffect(() => {
  timerRef.current = setInterval(() => {
    setSecondsPassed(prevSecond => prevSecond + 1);
  }, 1000);
  return () => {
    clearInterval(timerRef.current);
  };
}, []);

return (
  <button
    onClick={() => {
      clearInterval(timerRef.current);
    }}
  >
    Stop timer
  </button>
) 
```

在 Vue 组合 API 中，正如我们在本文前面的几乎所有例子中看到的，`ref`可以用来定义反应状态。使用组合 API 时，模板引用和反应性引用是统一的。

## 附加功能

因为 React 钩子在每个渲染上运行，所以不需要 Vue 的`computed`函数的等价物。你可以自由声明一个包含基于状态或道具的值的变量，它将指向每次渲染的最新值:

```
const [name, setName] = useState("Mary");
const [age, setAge] = useState(25);
const description = `${name} is ${age} years old`; 
```

在 Vue 的情况下，`setup`函数只运行一个。因此需要定义计算的属性，这些属性应该观察到某个状态的变化并相应地更新(但是只有当它们的依赖关系之一发生变化时):

```
const name = ref("Mary");
const age = ref(25);
const description = computed(() => `${name.value} is ${age.value} years old`); 
```

像往常一样，记住 refs 是容器，值是通过`value`属性访问的；)

但是如果计算一个值是昂贵的，会发生什么呢？你不会想每次组件渲染时都计算它。React 包含了`useMemo`钩子来表示:

```
function fibNaive(n) {
  if (n <= 1) return n;
  return fibNaive(n - 1) + fibNaive(n - 2);
}
const Fibonacci = () => {
  const [nth, setNth] = useState(1);
  const nthFibonacci = useMemo(() => fibNaive(nth), [nth]);
  return (
    <section>
      <label>
        Number:
        <input
          type="number"
          value={nth}
          onChange={e => setNth(e.target.value)}
        />
      </label>
      <p>nth Fibonacci number: {nthFibonacci}</p>
    </section>
  );
}; 
```

`useMemo`还期望依赖数组知道何时应该计算新值。 **React 建议您将`useMemo`用作性能优化，而不是保证值将保持记忆**，直到任何依赖关系发生变化。

*顺便提一下:肯特·c·多兹有[一篇非常好的文章](https://kentcdodds.com/blog/usememo-and-usecallback)，解释了很多不需要`useMemo`和`useCallback`的情况。*

Vue 的`computed`执行自动依赖跟踪，所以它不需要依赖数组。

`useCallback`与`useMemo`相似，但用于记忆回调函数。事实上，`useCallback(fn, deps)`相当于`useMemo(() => fn, deps)`。它的理想用例是当我们需要保持渲染之间的引用相等时，例如，我们将回调传递给用`React.memo`定义的优化的子组件，我们希望避免它不必要的重新渲染。由于 Vue 组合 API 的性质，没有与`useCallback`等价的东西。在`setup`函数中的任何回调都只会被定义一次。

## 上下文并提供/注入

React 使用了`useContext`钩子作为读取指定上下文的当前值的新方法。像往常一样，返回值被确定为祖先树中最接近的`<MyContext.Provider>`组件的`value`属性。它相当于类中的`static contextType = MyContext`或`<MyContext.Consumer>`组件。

```
// context object
const ThemeContext = React.createContext('light');

// provider
<ThemeContext.Provider value="dark">

// consumer
const theme = useContext(ThemeContext); 
```

Vue 有一个类似的 API，叫做 provide/inject。它作为组件选项存在于 Vue 2.x 中，但是添加了一对`provide`和`inject`函数作为组合 API 的一部分，在`setup`函数:
中使用

```
// key to provide
const ThemeSymbol = Symbol();

// provider
provide(ThemeSymbol, ref("dark"));

// consumer
const value = inject(ThemeSymbol); 
```

请注意，如果您想要保留反应性，您必须明确地提供一个`ref` / `reactive`作为值。

## 暴露值来渲染上下文

在 React 的例子中，由于所有的钩子代码都在组件定义上，并且您返回了想要在同一个函数中呈现的 React 元素，所以您可以像在任何 JavaScript 代码中一样完全访问范围内的任何值:

```
const Fibonacci = () => {
  const [nth, setNth] = useState(1);
  const nthFibonacci = useMemo(() => fibNaive(nth), [nth]);
  return (
    <section>
      <label>
        Number:
        <input
          type="number"
          value={nth}
          onChange={e => setNth(e.target.value)}
        />
      </label>
      <p>nth Fibonacci number: {nthFibonacci}</p>
    </section>
  );
}; 
```

在 Vue 的情况下，如果你在`template`或`render`选项中定义了你的模板，或者如果你正在使用单个文件组件，你需要从`setup`函数返回一个对象，包含你想要暴露给模板的每一个值。您的 return 语句可能会变得冗长，因为您可能想要公开许多值，这一点需要注意，正如 RFC :
中提到的

```
<template>
  <section>
    <label>
      Number:
      <input
        type="number"
        v-model="nth"
      />
    </label>
    <p>nth Fibonacci number: {{nthFibonacci}}</p>
  </section>
</template>
<script>
export default {
  setup() {
    const nth = ref(1);
    const nthFibonacci = computed(() => fibNaive(nth.value));
    return { nth, nthFibonacci };
  }
};
</script>
} 
```

实现 React 中相同行为的一种方法是从`setup`选项本身返回一个渲染函数:

```
export default {
  setup() {
    const nth = ref(1);
    const nthFibonacci = computed(() => fibNaive(nth.value));
    return () => (
      <section>
        <label>
          Number:
          <input
            type="number"
            vModel={nth}
          />
        </label>
        <p>nth Fibonacci number: {nthFibonacci}</p>
      </section>
    );
  }
}; 
```

然而，模板在 Vue 中更受欢迎，所以公开一个带有值的对象肯定会是你在 Vue Composition API 中经常遇到的事情。

## 结论

对于这两个框架来说，现在都是激动人心的时刻。自从 2018 年引入 React Hooks 以来，社区已经在它们的基础上建立了令人惊叹的东西，自定义 Hooks 的可扩展性允许[许多开源贡献](https://usehooks.com)可以轻松地添加到我们的项目中。Vue 从 React Hooks 中获得灵感，并以一种适合框架的方式对它们进行调整，并作为所有这些不同技术如何拥抱变化并分享想法和解决方案的一个例子。我等不及 Vue 3 的到来，并看到它开启的可能性。

感谢您的阅读，并继续建设令人惊叹的东西🚀