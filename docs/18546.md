# 用于反应的基本组件

> 原文：<https://dev.to/tetragius/basecomponent-for-react-2c5n>

在我的大多数项目中，我使用从其他组件的继承。我的基本组件是 base component。

> 我知道[《作文 vs 传承》](https://reactjs.org/docs/composition-vs-inheritance.html)这篇文章，这里仅是我的观点和一些来自生活经验的推论。

我的 BaseComponent 文件看起来像这样

```
import * as React from 'react';
import { Subject } from 'rxjs';

export interface IBaseComponentProps {
    hidden?: boolean;
}

export interface IBaseComponent<T> {
    _error?: boolean;
    view(template?: T): JSX.Element;
    errorView(): JSX.Element;
}

export default class BaseComponent<P = any, S = any, V = any>
    extends React.Component<Readonly<P> & IBaseComponentProps, S>
    implements React.Component<Readonly<P> & IBaseComponentProps, S>, IBaseComponent<V>{

    _error: boolean = false;

    constructor(props: P) {
        super(props);
    }

    componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
        this._error = true;
    }

    view(template?: V): JSX.Element {
        return null;
    }

    errorView() {
        return null;
    }

    render() {
        if (this.state && this._error) {
            return this.errorView();
        }

        const props = this.props;
        if (!props.hidden) {
            return this.view();
        }
        else {
            return null;
        }
    }
} 
```

进一步，当创建一个组件代替时，

```
class MyComponent extends React.Component<IProps, IState> 
```

我愿意(ITemplate -可选)

```
class MyComponent extends BaseComponent<IProps, IState, ITemplate> 
```

## 而我从这里得到了什么？

> 从继承的使用来看呢？

考虑列表项
的一个组件的例子

```
interface ISimpleListItemProps<T> extends IBaseComponentProps {
    item: IItem & T;
    onClick?: (item: IItem & T) => void;
}

class SimpleListItem<P, S, T> extends BaseComponent<ISimpleListItemProps<T> & P>{
    constructor(props) {
        super(props);
    }

    onClick(item: IItem & T) {
        this.props.onClick(item);
    }

    view(props) {
        const item = props.item;
        return (
            <div 
            className="simple-list-item" 
            onClick={() => this.onClick(item)}>{item.title}
            </div>
        );
    }
} 
```

首先，从 BaseComponent 继承的所有组件都显示隐藏属性
，如果我们需要隐藏一个组件，我们可以使用

```
 <SimpleListItem hidden={expression} ... /> 
```

而不是

```
 { expression && <SimpleListItem ... />} 
```

我还可以在不使用这个的情况下获得视图中的属性，并确定在组件崩溃时要显示的错误视图。

* * *

在我的例子中，BaseComponent 也有一个 stream 属性。

```
 ...
    _error: boolean = false;
    _stream: Subject<any>;

    get stream(): Subject<any> { return this._stream }
    set stream(val: Subject<any>) { this._stream.next(val) }

    constructor(props: P) {
        super(props);
        this._stream = this.props.stream; /*!!!*/
    }
    ... 
```

*这允许我为一个父组件中的所有组件添加一个公共消息总线，这在某些情况下很方便*

为此，还有一个 BaseContainer 组件，在这个组件中，你可以在构造函数中创建线程。这种容器的存在允许您更好地控制和维护代码。

```
 ...
    private _stream;

    get stream() { return this._stream };
    set stream(data) { this._stream.next(data) };

    constructor(props: P & IBaseContainerProps<T>, createStream?: boolean) {
        super(props);
        if (createStream) {
            this._stream = new Subject();
        }
    }
    ... 
```

* * *

返回到继承和列表项。

现在如果我想扩展列表项的功能，我可以从现有的
继承

```
interface IExtendedListItemProps<T> {
    onSelect?: (item: IExtendedItem & T) => void;
    onRemove?: (item: IExtendedItem & T) => void;
}

interface IExtendedListItemState {
    selected?: boolean;
}

export default class ExtendedListItem<P, S, T> extends SimpleListItem<IExtendedListItemProps<T> & P, IExtendedListItemState | S, IExtendedItem & T>{

    constructor(props) {
        super(props);
    }

    state = { selected: this.props.item.selected || false }

    onSelect(item: IExtendedItem & T) {
        this.setState({ selected: !this.state.selected })
        this.props.onSelect && this.props.onSelect(item);
    }

    onRemove(item: IExtendedItem & T) {
        this.props.onRemove && this.props.onRemove(item);
    }

    view() {

        const props = this.props;
        const state = this.state;
        const item = props.item;

        return (
            <div className={`extended-list-item${state.selected && ' extended-list-item__selected' || ''}`}>
                <div className="extended-list-item_select" onClick={() => this.onSelect(item)}></div>
                <>
                    {super.view()}
                </>
                <div className="extended-list-item_remove" onClick={() => this.onRemove(item)}></div>
            </div>
        );
    }
} 
```

新组件既有 **OnClick** 方法，又有两个新方法。我还在包装器中使用了 **super.view ()** 。

创建一个下拉列表项，它仍然有 delete 和 highlight 方法，但是覆盖了 **OnClick** 事件——它现在扩展了项

```
...
    onClick(item: IExpandableItem & T) {
        this.toggle()
        this.stream.next(item);
    }

    view(template?: { body?: JSX.Element, footer?: JSX.Element }) {

        const props = this.props;
        const state = this.state;
        const item = props.item;

        const footer = template && template.footer || <div className="simple-footer">[---]</div>
        const body = template && template.body || item.body;

        return (
            <div className={`expandable-list-item${state.selected && ' expandable-list-item__selected' || ''}`}>
                <div className={"expandable-list-item_head"}>
                    {super.view()}
                </div>
                {
                    state.expanded && <div className="expandable-list-item_body">{body}</div>
                }
                {footer}
            </div>
        );
    }
... 
```

现在我使用**模板**属性函数**视图**并允许我非常简单地创建一个带有新页脚
的新组件

```
export default class ExtendedExpandableListItem
    extends ExpandableListItem<IExtendedExpandableListItemProps, IExtendedExpandableListItemState, IExtendedExpandableItem>{
    view() {
        const item = this.props.item;
        const footer = <div className="special-footer">{item.footer}</div>;
        return super.view({ footer });
    }
} 
```

## 结论

在某些情况下，继承在 HOC 之前的代码或组件的组合中提供了明显的优势。代码的可读性增加了。我还注意到，继承的使用可以减少应用程序最终包的大小，因为继承将 webpack 变成了原型继承，组件的特设和组合变成了新的闭包函数

此外，从 BaseComponent 等通用组件继承的使用允许您将公共逻辑保存在一个地方，并且如果需要，可以重新定义其行为。

谢谢！

[我之前的文章](https://dev.to/tetragius/my-warm-state-management-library-for-react-based-on-proxy-api-23nd)