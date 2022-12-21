# 反应不可控性

> 原文：<https://dev.to/kelleyvanevert/an-uncontrollabilizer-for-react-2laj>

我只是想分享我对制作 jquense 的[uncontrolled](https://github.com/jquense/uncontrollable)的快速和简单的，希望合理的打字稿证明版本的看法，但用 Yury 的 [stackoverflow 答案](https://stackoverflow.com/a/55730764/710395)的感性来写。

用例是什么？您有一个 React 组件，其中包含一个甚至多个重要的值。您既希望能够将控制权委托给控制组件，也希望能够在周围的组件不想控制您时自己掌握控制权。

## 1/2 作为特设

其思想是:只需编写您的组件，就好像它是完全受控的一样，然后使用一个简单的封装器来填充任何必要的状态管理。真正困难的部分是正确的类型。(遗憾的是，`Exclude<string, keyof P>`又只是`string`，所以这实际上不起作用。)

这里有一个 CodeSandbox 的例子。

代码如下:

```
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;

// A "type function" that computes an uncontrolled version
//  of controlled component's props type, given
//  a value key, an onchange key (both currently part
//  of the props type), and a default value key (which should
//  be freshly chosen)
type Uncontrolled<
  P, // controlled component's props
  VK extends keyof P, // value key
  OCK extends keyof P, // onchange key
  DK extends Exclude<string, keyof P> // default value key
> = Omit<P, VK | OCK> & { [k in DK]: P[VK] };

// Turns a controlled component into a component that can both
//  be used in a controlled fashion or an uncontrolled fashion
function uncontrollabilize<
  P,
  VK extends keyof P,
  OCK extends keyof P,
  DK extends Exclude<string, keyof P>
>(
  C: React.FunctionComponent<P>,
  valueKey: VK,
  onChangeKey: OCK,
  defaultKey: DK
): React.FunctionComponent<P | Uncontrolled<P, VK, OCK, DK>> {
  return function Wrapped(props: P | Uncontrolled<P, VK, OCK, DK>) {
    // Using a flag which is only set once, to disable switching between
    //  controlled and uncontrolled usage of the same instance.
    const isControlled = useRef<boolean>(valueKey in props).current;

    // It would be theoretically more correct, and type-check,
    //  if this state initialization only occurs in the
    //  else branch below. But then it's less clear that
    //  the hook is always, or always-not, called.
    // So, stability first.
    const defaultValue = (props as any)[defaultKey];
    const [value, set_value] = useState<P[VK]>(defaultValue);

    if (isControlled) {
      return <C {...props as P} />;
    } else {
      const controllerProps = {
        [valueKey]: value,
        [onChangeKey]: set_value
      };
      return (
        // @ts-ignore
        <C {...props as Uncontrolled<P, VK, OCK, DK>} {...controllerProps} />
      );
    }
  };
} 
```

## 2/2 为钩形

当然，还有一个钩子版本，它更短，看起来更好，:D，但是它不允许你严格地输入你的组件的道具，这确实有点失去吸引力。也就是说，您需要将所有三个属性(value、onChange、defaultValue)设置为可选。

这里有一个 CodeSandbox 的例子。

```
function useUncontrollizable<T>(
  val?: T,
  set_val?: (newVal: T) => void,
  default_val?: T
): [T, (newVal: T) => void] {
  const isControlled =
    typeof val !== "undefined" && typeof set_val !== "undefined";
  const control = useState<T>(default_val);
  return isControlled ? [val, set_val] : control;
} 
```