# 为什么我不再在 React 组件上散布道具

> 原文：<https://dev.to/aurelio/why-i-stopped-spreading-props-on-react-components-o3f>

在 React 组件上散布道具是一个非常常见的模式，我很容易就学会了，并且从第一天起就爱上了它。随着时间的推移，我学会了理解在不同的上下文中使用 spread 运算符的含义，并得出结论:在 React 中，大多数时候最好避免使用 spreading props。我们来看看为什么。

## 我停止了散布道具...但也不尽然

> 谨慎使用散布道具

 **首先，我想先澄清一下。这个帖子的标题是故意误导(阅读:clickbaity)。道具传播有好的用例，是一种可以有效使用的表达性和简洁的模式。

演示道具传播如何真正发光的一个很好的场景是 [HOCs](https://reactjs.org/docs/higher-order-components.html) 。让我们看看这个例子。

```
const withDouble = Comp => props => (
// We could wrap any type of Component here so we just pass all props down as they are
  <Comp {...props} value={props.value * 2} />
);

const SomeInput = ({ onChange, value, disabled }) => (
  <input value={value} disabled={disabled} onChange={e => onChange(e.target.value)}/>
);

const Doubled = withDouble(SomeInput);

const MyCal = () => {
  const [val, setVal] = React.useState(0);

  return (
    <>
    <SomeInput value={val} onChange={setVal} />
    <Doubled value={val} disabled />
    </>
  );
} 
```

你可以试试上面的代码[这里](https://jsfiddle.net/npsgLhf1/)。

是一个极其有限的基本计算器，只能将我们在字段中输入的内容翻倍。
在这种情况下，我们使用`withDouble`作为通用包装器，可以包装和增强任何组件。
因此，它必须保持对它将沿树向下传送的道具的警觉。

能够传播这样的道具真的很强大，因为我们可以自由地增强我们可能拥有的任何组件。在上面的例子中，我们可以看到稍后传递“disabled”实际上就是为了这个原因。

```
<Doubled value={val} disabled /> // this prop will be forwarded to `SomeInput` that will render a read only field 
```

## 漂亮总比难看好。

我们来看看`SomeInput`是如何实现的。

```
const SomeInput = ({ onChange, value, disabled }) => (
<input value={value} disabled={disabled} onChange={e => onChange(e.target.value)}/> ); 
```

既然我们喜欢上了散布道具的新技能，我们可能会想这么做:

```
const SomeInput = ({ onChange, ...props }) => (
<input {...props} onChange={e => onChange(e.target.value)}/> ); 
```

乍一看，这看起来更有吸引力——至少对我来说——我不否认我对这看起来如此简洁感到满意。

如果我们试着这样做，这个组件还是会工作。我们刚刚介绍的问题现在可能还不明显，但是我们已经无法控制我们的底层`input`将获得哪种支持。试试这个，你会看到`randomProp`会被愉快地转发给`<input />`本身。

```
<SomeInput onChange={alert} randomProp={2} /> 
```

如果我们保留了最初的实现，那么杂散属性就会被忽略。

```
const SomeInput = ({ onChange, value, disabled }) => (
<input value={value} disabled={disabled} onChange={e => onChange(e.target.value)}/>
);

<SomeInput
  onChange={alert} // known props, it will be passed down
  randomProp={2} // unknown one, ignored
/> 
```

虽然这看起来很简单，但是随着代码库的规模和复杂性的增长，这样的问题变得越来越普遍。应用一层又一层的组件，而不进行任何检查，将很难跟踪数据流到哪里，以及哪些属性应用到哪里。

## 面对暧昧，拒绝猜测的诱惑。

最新的例子是使用类型检查器来帮助标记不存在的属性。实际上，在遍布各处的现有代码基础上引入类型定义也不是最令人愉快的体验。
如果你喜欢打字或者流，试着写一个类型定义如下:

```
const MyComp = ({
  type,
  value,
  ...rest
}: Props) => (
  const className = type === 'round' ? 'Btn--round' : 'Btn';

  return (
    <Actionable className={className} {..rest}>{value}</Actionable>
  )
)

type Props = {
  type?: 'round',
  value: React.Node
  // and??
} 
```

猜测`value`和`type`相当简单。那么`...rest`呢？形状应该是什么样的？
我们要么变得草率并允许`any`，这让我怀疑我们为什么还要尝试键入这个东西，要么我们必须打开‘可操作的’实现，检查那里是如何处理道具的，并希望那里没有另一个传播(这是非常可能的)，否则我们将打开另一个文件。

一旦完成，我还将检查“MyComp”的所有实例，以确保随机或过时的道具不会被错误地传递。如果这听起来很乏味，那是因为它确实如此。

让我们将其与另一个实现进行比较。

```
const MyComp = ({
  type,
  value,
  colour,
  size,
  onClick,
  onHover
}: Props) => (
const className = type === 'round' ? 'Btn--round' : 'Btn';

return (
  <Actionable
    onHover={onHover}
    onClick={onClick}
    className={className}
    colour={colour}
    size={size}>
      {value}
  </Actionable>
  )
) 
```

虽然我们不能 100%确定这个道具列表中的每一种类型，但是我们可以做很多事情，而不需要做更多的工作。

```
type Props = {
  type?: 'round',
  value: React.Node,
  colour: string,
  size: string | number,
  onClick: () => void,
  onHover: () => void
}; 
```

虽然这并不完美，但比我们上面的要好很多。我甚至会说下一次糟糕的努力比第一次更好！至少我们列出了所有可以接受的道具！

```
type Props = {
  type?: any,
  value: any,
  colour: any,
  size: any,
  onClick: any,
  onHover: any
}; 
```

这个概念可能看起来是特定于打字的，但是我认为它很好地代表了在阅读以这种或那种方式编写的代码时所需要的认知努力的差异。

通过列出我们的道具，我们远离陷阱，并确保代码的读者一目了然。

## 显性比隐性好。

总之，虽然散布道具是一种强有力的模式，我们必须意识到它的缺点，并且意识到这种技术有它的位置和优点，但肯定不是银弹。

清楚地列出道具有助于我们传达我们功能的意图和能力。传播它们可以用来达到某种目的，但是不应该以牺牲可读性或安全性为代价。

## 参考文献

随着时间的推移，我发现传播道具的陷阱实际上记录在不止一份官方文件中。

> Spread 属性很有用，但是它们也很容易将不必要的属性传递给不关心它们的组件，或者将无效的 HTML 属性传递给 DOM。我们建议谨慎使用这种语法。
> [反应文件](https://reactjs.org/docs/jsx-in-depth.html#spread-attributes)

*   文章开头的第一句引语来自 [Airbnb 的 JavaScript 风格指南](http://airbnb.io/javascript/react/)。

此外，您可能有兴趣知道:

*   本文中不止一处引用了 Python 的[禅。](https://www.python.org/dev/peps/pep-0020/)

*   封面图片由[拉克伦·邓普西](https://unsplash.com/@lachlanjdempsey)拍摄

*原发表于我的[博客](https://nobitagit.github.io/blog/Why-I-stopped-spreading-props-react/)* 。**