# 样式组件，再来一次

> 原文：<https://dev.to/stereobooster/styled-components-one-more-time-5g0l>

**UPD** : [关于 div vs 按钮](https://dev.to/stereobooster/debate-that-never-happened-5b98)。

最初 [styled-components](https://github.com/styled-components/styled-components) 作为一个库出现在 2016 年。这个想法是由[格伦·马登](https://github.com/geelen)提出的。它是 [CSS-in-JS](https://github.com/stereobooster/css-in-js-101) 解决方案，但在我看来，最强大的部分不是 CSS-in-JS，而是快速创建小组件的能力。

考虑下面的片段:

```
<h1 style={{ fontSize: "1.5em", textAlign: "center", color: "palevioletred" }}>
  Hello World, this is my first styled component!
</h1> 
```

vs

```
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

Hello World, this is my first styled component!; 
```

如您所见，这段代码中有更多的语义。你可以很容易地判断出这是一个标题，否则就很难判断了(在`h1`的情况下，可以猜测，但如果是`div`？..).

我们当然可以在没有样式组件的情况下做同样的事情:

```
const Title = ({ children }) => (
  <h1
    style={{ fontSize: "1.5em", textAlign: "center", color: "palevioletred" }}
  >
    {children}
  </h1> ); 
```

但是让我们面对现实:没有人这样做。对于样式化组件，这是自然而然的事情。这是简化版，不传道具，不处理`ref`属性。

风格化组件与 a11y 完美搭配。比如，代替这个:

```
<>
  <div
    role="button"
    aria-expanded={expanded}
    aria-controls={sectionId}
    id={labelId}
    className={styles.Label}
    onClick={() => onToggle && onToggle(index)}
  >
    {title}
    <span aria-hidden={true}>{expanded ? "▲" : "▼"}</span>
  </div>
  <div
    role="region"
    aria-labelledby={labelId}
    id={sectionId}
    hidden={!expanded}
    className={styles.Panel}
  >
    {expanded && (isFunction(children) ? children() : children)}
  </div> </> 
```

我们可以写

```
const Label = styled.div("Label");
Label.defaultProps = { role: "button" };

const Panel = styled.div("Panel");
Panel.defaultProps = { role: "region" };

<>
  <Label
    aria-expanded={expanded}
    aria-controls={sectionId}
    id={labelId}
    onClick={() => onToggle && onToggle(index)}
  >
    {title}
    <span aria-hidden={true}>{expanded ? "▲" : "▼"}</span>
  </Label>
  <Panel aria-labelledby={labelId} id={sectionId} hidden={!expanded}>
    {expanded && (isFunction(children) ? children() : children)}
  </Panel> </>; 
```

很好，不是吗？

## 替代品

这个想法如此受欢迎，以至于被其他图书馆复制。

### CSS-in-JS

*   [情感](https://emotion.sh/docs/styled)
*   [JSS](https://github.com/cssinjs/styled-jss)

### 【零运行时间】CSS-in-JS

*   [利纳里亚](https://github.com/callstack/linaria#syntax)

### 样式属性

*   [无样式组件](https://github.com/stereobooster/unstyled-components)

### CSS 模块

*   [CSS-模块-组件](https://github.com/stereobooster/css-modules-components)
*   [style-style](https://github.com/akameco/styled-style)
*   [css-to-js-loader](https://github.com/theKashey/css-to-js-loader)

## DIY

让我们编写样式化组件的(非常)简化的实现，让事情变得不那么神秘。所以我们从这个开始:

```
const Title = ({ children }) => (
  <h1
    style={{ fontSize: "1.5em", textAlign: "center", color: "palevioletred" }}
  >
    {children}
  </h1> ); 
```

1)我们需要传递道具:

```
const Title = ({ children, ...props }) => (
  <h1
    style={{ fontSize: "1.5em", textAlign: "center", color: "palevioletred" }}
    {...props}
  >
    {children}
  </h1> ); 
```

2)我们需要处理`ref`

```
const Title = React.forwardRef(({ children, ...props }, ref) => (
  <h1
    style={{ fontSize: "1.5em", textAlign: "center", color: "palevioletred" }}
    ref={ref}
    {...props}
  >
    {children}
  </h1> )); 
```

3)让我们用`as`属性来配置标签(有些库称之为`is`或`use`):

```
const Title = React.forwardRef(({ children, as = "h1", ...props }, ref) =>
  React.createElement(
    as,
    {
      style: { fontSize: "1.5em", textAlign: "center", color: "palevioletred" },
      ...props,
      ref
    },
    children
  )
); 
```

4)让我们添加一种覆盖样式的方法

```
const Title = React.forwardRef(({ children, as = "h1", ...props }, ref) =>
  React.createElement(
    as,
    {
      ...props,
      style: {
        fontSize: "1.5em",
        textAlign: "center",
        color: "palevioletred",
        ...props.style
      },
      ref
    },
    children
  )
); 
```

5)让我们生成组件

```
const styled = defaultAs =>
  React.forwardRef(({ children, as = defaultAs, ...props }, ref) =>
    React.createElement(
      as,
      {
        ...props,
        style: {
          fontSize: "1.5em",
          textAlign: "center",
          color: "palevioletred",
          ...props.style
        },
        ref
      },
      children
    )
  );

const Title = styled("h1"); 
```

6)让我们从外部传递默认样式

```
const styled = defaultAs => defaultStyles =>
  React.forwardRef(({ children, as = defaultAs, ...props }, ref) =>
    React.createElement(
      as,
      {
        ...props,
        style: {
          ...defaultStyles,
          ...props.style
        },
        ref
      },
      children
    )
  );

const Title = styled("h1")({
  fontSize: "1.5em",
  textAlign: "center",
  color: "palevioletred"
}); 
```

7)给组件
添加显示名称

```
const styled = defaultAs => defaultStyles => {
  const component = React.forwardRef(
    ({ children, as = defaultAs, ...props }, ref) =>
      React.createElement(
        as,
        {
          ...props,
          style: {
            ...defaultStyles,
            ...props.style
          },
          ref
        },
        children
      )
  );
  component.displayName = `${defaultAs}💅`;
  return component;
}; 
```

8)根据属性定制样式

```
const isFunction = x => !!(x && x.constructor && x.call && x.apply);

const styled = defaultAs => defaultStyles => {
  const component = React.forwardRef(
    ({ children, as = defaultAs, ...props }, ref) =>
      React.createElement(
        as,
        {
          ...props,
          style: {
            ...(isFunction(defaultStyles)
              ? defaultStyles(props)
              : defaultStyles),
            ...props.style
          },
          ref
        },
        children
      )
  );
  component.displayName = `${defaultAs}💅`;
  return component;
};

const Title = styled("h1")(() => ({
  fontSize: "1.5em",
  textAlign: "center",
  color: "palevioletred"
})); 
```

9)过滤非 html 属性，让我们过滤掉`propTypes` :
中的所有属性

```
const filterObject = (rest, shouldForwardProp) =>
  Object.keys(rest)
    .filter(shouldForwardProp)
    .reduce((obj, key) => {
      obj[key] = rest[key];
      return obj;
    }, {});

const styled = defaultAs => defaultStyles => {
  const component = React.forwardRef(
    ({ children, as = defaultAs, ...props }, ref) =>
      React.createElement(
        as,
        {
          ...(component.propTypes
            ? filterObject(props, key =>
                Object.keys(component.propTypes).includes(key)
              )
            : props),
          style: {
            ...(isFunction(defaultStyles)
              ? defaultStyles(props)
              : defaultStyles),
            ...props.style
          },
          ref
        },
        children
      )
  );
  component.displayName = `${defaultAs}💅`;
  return component;
}; 
```

10)奖金。让我们用`Proxy`代替第一个函数:

```
const styled = Proxy(
  {},
  {
    get: (_, defaultAs, __) => defaultStyles => {
      const component = React.forwardRef(
        ({ children, as = defaultAs, ...props }, ref) =>
          React.createElement(
            as,
            {
              ...(component.propTypes
                ? filterObject(props, key =>
                    Object.keys(component.propTypes).includes(key)
                  )
                : props),
              style: {
                ...(isFunction(defaultStyles)
                  ? defaultStyles(props)
                  : defaultStyles),
                ...props.style
              },
              ref
            },
            children
          )
      );
      component.displayName = `${defaultAs}💅`;
      return component;
    }
  }
);

const Title = styled.h1({
  fontSize: "1.5em",
  textAlign: "center",
  color: "palevioletred"
}); 
```

现在你知道里面是什么了！