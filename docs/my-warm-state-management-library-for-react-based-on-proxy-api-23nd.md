# react 的状态管理库。基于代理 API。

> 原文：<https://dev.to/tetragius/my-warm-state-management-library-for-react-based-on-proxy-api-23nd>

我为自己创造了它，但也许有人会喜欢它:)

[https://codepen.io/tetragius/embed/xoOeqj?height=600&default-tab=result&embed-version=2](https://codepen.io/tetragius/embed/xoOeqj?height=600&default-tab=result&embed-version=2)
[演示](https://tetragius.github.io/raxy/example/dist/#/route-three)

#### 动机

> 我想和国家一起工作，就像和一个普通的物体一样，不关心任何事情。我不想像 redux 那样写动作和减少器。想要简单的东西。隐藏复杂的逻辑。

#### 迎接 Raxy

[Github](https://github.com/Tetragius/raxy)

Raxy 的意思是 react 加代理(JS 代理 API)。而它的背后隐藏着所有与国家的合作。给你一个代理对象

Raxy 重约 2kb 或 5kb，带有用于 IE 的聚合填充物。

#### 安装和使用

##### 安装

很简单:)

> npm 安装-保存 raxy

然后在项目中连接它

```
import Raxy from 'raxy'; // with polyfill
import Raxy from 'raxy/next'; // without polyfill 
```

接下来，创建一个存储，在这个例子中它是一个简单的对象，但是它可以有很深的嵌套。Raxy 也代理数组(稍后会详细介绍)

```
const ToDo = {
    list: [
        {title: 'item 1', finished: false},
        {title: 'item 2', finished: false},
        {title: 'item 3', finished: false},
    ]
}
const {state, subscribe, connect} = new Raxy({ToDo}); 
```

**状态** —代理存储

那么你就有了两种倾听变化的机制。第一个的*是连接方法。它将 react 组件和组件状态的存储映射函数作为其参数。* 

```
export ConnectedComponent = connect(Component, store => ({list: store.list})); 
```

**连接** —返回包装的组件

*第二个*机制是 subscribe 方法；它将回调作为参数，返回改变后的状态。映射函数类似于 connect 方法。

```
const subscriber = subscribe((state) => console.log(state), s => ({...s})); 
```

**Subscribe** —返回一个对象，有两种方法: **on** 和 **off**

**off** —暂停订阅

的**—续订您的套餐**

如果有必要，你可以创建一个反应钩子

```
function useRaxy(mapper) {
    const [data, setState] = useState(mapper(state));

    useEffect(() => {
        let subscriber = subscribe(s => setState(s), mapper);
        return () => { // dont forget unsubscribe when dismount
            subscriber.off();
            subscriber = null;
        }
    });

    return data;
} 
```

##### 用法

现在，您可以简单地将代理对象作为普通对象进行更新。

```
state.list = state.list.map(i => {
            if(i === this.props.item){
                i.finished = !i.finished;
            }
            return i;
        }); 
```

或者这样，因为数组也被代理

```
state.list[1] = {title: 'item 1', finished: true};
state.list[1].finished = false; 
```

或者即使如此

```
state.nested.a = 'new value'; 
```

如果你需要一种来自 redux
的动作

```
const action = (c, e) => {
    const state = Object.assign({}, store.state);
    state.nested.c = c;
    state.nestedAgain.e = e;
    Object.assign(store.state, state);
}

action(4, 5); 
```

你也可以使用历史和 redux-dev-tools

```
const history = createHashHistory({ basename: '/' });

const {state, subscribe} = new Raxy({
    list: [
        { label: 'item 1', status: false },
        { label: 'item 2', status: false },
        { label: 'item 3', status: true },
    ],
    /*!!*/ location: history.location, /*!!*/
    nested: { b: 2 }
});

state.subscribe(location => console.log(location), state => ({ location: state.location }));

history.listen(location => state.location = location); 
```

```
const {state, subscribe} = new Raxy({});

const devTools = window.__REDUX_DEVTOOLS_EXTENSION__.connect();

devTools.init({ value: state });

subscribe(state => devTools.send('change state', { value: state }), state => ({ ...state })); 
```

## 工作原理

[源文件](https://github.com/Tetragius/raxy/blob/master/src/core.ts)

Raxy —通过在所有嵌套对象或数组上调用代理方法来递归包装存储库对象。只有“set”方法被代理。

通过调用 subscribe 或 connect，一个新的订户被添加到堆栈中。为了防止内存泄漏，您可以取消订阅更新，并使用 revoke 方法删除代理对象。

在“设置”方法的截取功能中，在记忆的先前状态和新状态之间进行比较。(状态由专用 in subscribe 或 connect 的映射功能返回)

如果状态是新的，那么调用 subscribe 的回调方法，或者包装组件的 setState 方法。

对于一个组件，如果它被卸载，状态更新会自动取消。

> 如果你想了解更多信息，可以参考-> [Github](https://github.com/Tetragius/raxy)