# 用样式化组件魔术从 HTML 标签中清理代码

> 原文：<https://dev.to/niconiahi/clean-your-code-from-html-tags-with-styled-components-magic-2jdk>

有多少次你查看你的代码寻找一个特定的 div，浪费了几分钟，直到你找到它。或者你可能没有找到它，试图通过你用来设计它的类来搜索 div，但是糟糕的是，你真的不记得这个类的名字了。糟透了，对吧？

### 风格组件救驾

这是我使用 Styled Components 的经验，这是一个 CSS 库，它在实现 CSS 代码时给你很大的能力。

#### 酷事 1 号

假设您有以下组件:

```
const Component = () => (
    <div class='container'>
      <div class='header'>
        <p>some text</p>
      </div>
      <main>
        <div class='main_left'>
          <p>some text</p>
        </div>
        <div class='main_right'>
          <p>some text</p>
        </div>
      <main>
      <div class='footer'>
        <p>some text</p>
      </div>
    </div>
  ) 
```

很简单吧？

现在想象一下这个

```
import styled from 'styled-components'

const Container = styled.div`
  // css code here
`

const MainLeft = styled.div`
  // css code here
`

const MainRight = styled.div`
  // css code here
`

const Footer = styled.div`
  // css code here
`

const Component = () => (
    <Container active>
      <Header>
        <p>some text</p>
      </Header>
      <main>
        <MainLeft>
          <p>some text</p>
        </MainLeft>
        <MainRight>
          <p>some text</p>
        </MainRight>
      <main>
      <Footer>
        <p>some text</p>
      </Footer>
    </Container>
  ) 
```

干净多了吧。请注意，生成的组件并不是真正的组件(*它们是样式组件*)，您可以在其中生成 JS 逻辑，它只是包装了 HTML 标签的 CSS 代码定义，并以易于查找的名称导出。

我看的方式是这样的: **HTML 标签**+**class _ name**=**styled component**

#### 酷事二号

需要记住的是:**它是可重复使用的！**灵活复用。

为什么*灵活地*可重用？

一方面，您可以在另一个 JS 文件中声明样式化的组件，并将其导入到 **any** React 组件中。

另一方面，你也可以这样做:

想象一下这样一种情况，你有一个*选择* HTML 标签和一个*按钮* HTML 标签，最后你希望它们看起来一样。您已经完成了对*选择*标签的样式化，并且您将从*按钮*标签开始。*等等*，试试这个。

当然，您首先声明选择样式化的组件样式。

```
const Select = styled.select`
  width: 400px;
  height: 400px;
  background-color: blue;
  border: 1px solid red;
` 
```

这样做之后，您可以在另一个 HTML 元素中从这个 Select 组件继承所有的**样式**。

我在样式中使用粗体，因为它继承了 CSS，所以:

```
 width: 400px;
  height: 400px;
  background-color: blue;
  border: 1px solid red; 
```

让我们继续

```
const Select = styled.select`
  width: 400px;
  height: 400px;
  background-color: blue;
  border: 1px solid red;
` 
```

假设您想要一个与 Select 具有相同样式的`button`。在组件中，你可以这样使用它:

```
const Component = () => (
  <Select as="button" />
) 
```

我们在 Button 声明中所说的是:从 Select and 中获取所有样式，但将其呈现为一个 button HTML 标记。请注意，现在选择 recieves 的属性是 HTML 按钮标记的属性。(所以，没有选项)。

#### 酷事三号

现在假设你需要根据组件中的状态给一个 HTML 标签着色，比如:

```
const Component = () => {
  const someCondition = useState(false)

  return (
    <p className={`base ${someCondition ? 'when_true' : 'when_false'}`}>some text</p>
  )
} 
```

那么，我在这里看到了什么错误。几件事:

1.  您需要定义两个类(一个用于条件真，一个用于条件假)
2.  您可能需要创建 3 个类(一个用于 **p** HTML 标签的基本样式，一个用于仅当条件为真时应用的样式，一个用于仅当条件为假时应用的样式)

在普通的 CSS 代码中:

```
<style>
  .base {
    background-color: grey;
    font-size: 1.5rem;
    font-weight: bold;
  }

  .when_true {
    color: blue;
  }

  .when_false {
    color: red;
  }
</style> 
```

现在用样式组件的力量**道具** :

```
import styled from 'styled-components'

const Paragraph = styled.p`
  background-color: grey;
  font-size: 1.5rem;
  font-weight: bold;
  color: ${props => props.conditionName ? 'blue' : 'red'} `

const Component = () => {
  const [someCondition, setSomeCondition] = useState(false)

  return (
    <Paragraph conditionName={someCondition}>some text</Paragraph>
  )
} 
```