# React 和 Typescript 中状态管理的设计模式

> 原文：<https://dev.to/beccaliz/design-patterns-for-state-management-in-react-and-typescript-5da7>

在任何交互式 web 应用程序中，我们都有状态。菜单打开和关闭，表单跟踪值，模态出现和消失。但是随着我们的应用程序的增长，我们如何以一种不会导致我们失去理智的方式来跟踪这种状态呢？

## 定义

让我们从定义一些术语开始。当我们谈论国家管理时，我们实际上在谈论两件事。本地状态管理和全局状态管理。

### 本地

局部状态是一个单独组件的状态。例如，菜单、模态、表单。这里概述的每个策略都可以用来管理本地状态。

### 全局

全局状态在整个应用程序中都可用。为此，我们经常使用 Flux 或 Redux 这样的工具，不过我在这里也会介绍一些其他的策略。全局状态可能用于当前登录的用户、警报、缓存的 API 响应数据或更复杂的组件交互。

### 什么时候应该使用局部或全局状态？

我的经验是通常使用本地状态，直到你需要全局状态。独立地跟踪自身状态的组件更容易测试和交互。例如，如果我正在为一个组件编写一个自动化测试，或者使用一个像 [Storybook](https://storybook.js.org) 这样的工具，我不想为了让组件正常工作而模仿我的全局状态管理。

我们可以使用像复合组件(一组一起使用并共享状态的组件)这样的策略在多个组件之间共享状态，而不使它们的状态全局可用。

## 状态管理策略

### 反应过来的状态

React 在类组件中提供内置状态。这是获取和设置状态的最基本方法，将在这里的多个示例中使用。

例如，我们可以创建一个带有`expanded`状态属性的简单卡片组件。这个属性可以用 React 组件类中的`setState`来更新。

```
class Home extends React.Component {
  state = {
    visible: false
  };

  render() {
    return (
      <Container>
        <Button onClick={() => this.showModal()}>Click me!</Button>
        <Modal visible={this.state.visible} onClose={() => this.hideModal()}>
          <h1>Surprise!</h1>
          <Button onClick={() => this.hideModal()}>Close</Button>
        </Modal>
      </Container>
    );
  }

  private showModal() {
    this.setState({
      visible: true
    });
  }

  private hideModal() {
    this.setState({
      visible: false
    });
  }
} 
```

[https://codesandbox.io/embed/goude?module=/src/Home.tsx](https://codesandbox.io/embed/goude?module=/src/Home.tsx)

这可能是我们的第一个策略，因为它最容易使用和理解。但是，这个基本方法只能在 React 组件类中使用。对于功能组件，我们可以使用一个`useState`钩子来完成同样的事情。

```
const Home: React.SFC = () => {
  const [visible, setIsVisible] = React.useState(false);

  function showModal() {
    setIsVisible(true);
  }

  function hideModal() {
    setIsVisible(false);
  }

  return (
    <Container>
      <Button onClick={showModal}>Click me!</Button>
      <Modal visible={visible} onClose={hideModal}>
        <h1>Surprise!</h1>
        <Button onClick={hideModal}>Close</Button>
      </Modal>
    </Container>
  );
}; 
```

[https://codesandbox.io/embed/bmjb9?module=/src/Home.tsx](https://codesandbox.io/embed/bmjb9?module=/src/Home.tsx)

这种方法的常见缺陷是重复。如果我想在我的应用程序中有多种模态呢？我可以使用这个基本组件，只复制状态逻辑，或者我可以创建一个只处理状态的抽象。这是我们可能使用渲染道具或上下文的地方。

### 渲染道具

在过去的几年中，这种模式作为一种将状态从父组件传递到子组件的方式获得了很大的流行，这种方式比高阶组件稍微显式一些。它可以用几种不同的方式实现，但是这个例子是把孩子渲染成一个传递状态属性的函数。

在这个例子中，我们将创建一个`ModalManager`，它将一个`expanded`和`toggle`道具传递给它的子对象，这些道具可用于打开和关闭模态。

```
const Home: React.SFC = () => {
  return (
    <Container>
      <ModalManager>
        {({ showModal, hideModal, visible }) => {
          return (
            <React.Fragment>
              <Button onClick={() => showModal()}>Click me!</Button>
              <Modal visible={visible}>
                <h1>Surprise!</h1>
                <Button onClick={() => hideModal()}>Close</Button>
              </Modal>
            </React.Fragment>
          );
        }}
      </ModalManager>
    </Container>
  );
}; 
```

[https://codesandbox.io/embed/50nymw58rk?module=/src/Home.tsx](https://codesandbox.io/embed/50nymw58rk?module=/src/Home.tsx)

这种模式在组件和它的状态之间创建了一个依赖关系的倒置，减少了与状态相关的重复。然而，当过度使用这种策略时，会导致所谓的“渲染道具地狱”。当一个组件被如此多层次的嵌套所包装，变得几乎不可读时，就会发生这种情况。

出于这个原因，2019 年渲染道具在很大程度上被钩子取代。

### 挂钩

钩子是 React 最酷的新玩具，但我保证我没有把它们放在这里只是为了听起来时髦和新潮。[钩子 API](https://reactjs.org/docs/hooks-reference.html) 是 React 对基于类的组件状态(如上所示)和渲染道具地狱的一些缺点的回答。

我们可以创建一个自定义钩子来替换上面例子中的渲染道具。这个定制钩子提供了相同的功能，但是语法略有不同。

```
function useModal(initialVisible = false) {
  const [visible, updateVisible] = React.useState(initialVisible);

  function showModal() {
    updateVisible(true);
  }

  function hideModal() {
    updateVisible(false);
  }

  return { visible, showModal, hideModal };
}

const Surprise: React.SFC = () => {
  const { showModal, hideModal, visible } = useModal();
  return (
    <React.Fragment>
      <Button onClick={() => showModal()}>Click me!</Button>
      <Modal visible={visible}>
        <h1>Surprise!</h1>
        <Button onClick={() => hideModal()}>Close</Button>
      </Modal>
    </React.Fragment>
  );
}; 
```

[https://codesandbox.io/embed/ivb8c?module=/src/Home.tsx](https://codesandbox.io/embed/ivb8c?module=/src/Home.tsx)

钩子可以和功能组件一起使用，如果规范的变化需要组件跟踪状态，那么就减少了将组件转换成类组件的需要。钩子仍然有其局限性——它们只能在功能组件的顶层使用。此外，为了在已建立的项目中使用钩子，可能需要 React 升级。

自定义钩子和渲染道具是解决重复问题的好方法。但是如果我想确保一次只显示一个模态呢？或者我想把负责显示模态的组件和负责打开或关闭模态的组件分开？这是上下文的一个用例。

### 上下文

[上下文 API](https://reactjs.org/docs/context.html) 为单个组件提供了访问共享状态的方法。Context 也是一个很好的解决道具钻取问题的方法，或者通过多层嵌套来访问子组件中的道具。上下文允许我们创建提供者组件(控制状态的父组件)和消费者组件(可以访问状态的子组件)。

我们可以全局使用上下文来与整个应用程序共享状态，或者我们可以在单个视图中使用它来创建复合组件，正如我们在本例中看到的那样。在这个例子中，我们创建了一个`ModalProvider`来跟踪可见的模态 ID，并向下传递一个函数来打开和关闭任何模态。任何具有上下文消费者的组件现在都可以从提供者那里访问这些变量和函数，而不需要显式地接收 props。

注意:在这个例子中，我们使用了`useContext`钩子，尽管我们也可以使用带有`Context.Consumer`组件和渲染道具的上下文。

```
const Home: React.SFC = () => {
  const { showModal } = React.useContext(ModalContext);
  return (
    <Container>
      <Button onClick={() => showModal("kittens")}>Click me!</Button>
      <Modal id="kittens">
        <h1>Kittens!</h1>
        <Image src="/assets/kittens.gif" />
        <Button onClick={() => showModal("error")}>Close</Button>
      </Modal>
    </Container>
  );
}; 
```

[https://codesandbox.io/embed/xo4x32o74z?module=/src/Home.tsx](https://codesandbox.io/embed/xo4x32o74z?module=/src/Home.tsx)

使用这种策略时，请记住，虽然每个组件都可以使用上下文消费者，但并不是应用程序中的每个组件都应该使用。在一个组件中使用上下文会将它耦合到一个上下文提供者，并且在使用或测试这个组件时需要您使用一个提供者或模拟它。例如，按钮、表单域等底层组件。也许应该接受回调属性，而不是使用上下文来保持它们尽可能的灵活和可重用。

## 我应该使用哪种设计模式？

这些模式都不是解决所有状态管理问题的通用解决方案。大多数应用程序可能应该使用多种策略的组合，最好区分只在一个地方使用的状态和可以共享的状态。也许像 Redux 这样的库对于更复杂的全局状态交互来说是一个很好的解决方案，而基本的 React state 或 render props 对于单个组件状态来说更好。

尽可能地保持您的模式一致，并愿意随着工具和需求的变化重新审视您的选择。