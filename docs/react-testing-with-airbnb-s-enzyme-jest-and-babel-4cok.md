# 用 Airbnb 的酵素、Jest 和 Babel 进行反应测试

> 原文：<https://dev.to/macmacky/react-testing-with-airbnb-s-enzyme-jest-and-babel-4cok>

## 嗨伙计们日安！

好了，伙计们，首先我们要从头开始配置 Jest，酵素。所以你可以知道我们要用什么样的模块或包。

在桌面上的命令行中执行此操作。

```
 md testing-with-enzyme && cd testing-with-enzyme 
```

Enter fullscreen mode Exit fullscreen mode

**md testing-with-enzyme** -创建一个名为 testing-with-enzyme
**&&**的目录-如果第一个命令没有抛出错误
**CD testing-with-enzyme**-将当前目录更改为 testing-with-enzyme

```
 npm init --y && npm i -D @babel/preset-env @babel/preset-react 
  @babel/plugin-proposal-class-properties @types/jest jest
  enzyme enzyme-adapter-react-16 && npm i -P react react-dom 
```

Enter fullscreen mode Exit fullscreen mode

好了，我不会解释所有这些包，但我们会让所有这些包与**酶**和**玩笑**一起工作。

```
 type nul > babel.config.js && type nul > jest.config.js && md Tests && md components 
```

Enter fullscreen mode Exit fullscreen mode

**为 **Windows 操作系统**键入 nul** 。**触摸**为 **UNIX 系统**。

我们的 **babel.config.js** 文件。

```
module.exports = {
  presets: ['@babel/preset-env', '@babel/preset-react'],
  plugins: ['@babel/plugin-proposal-class-properties']
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 **jest.config.js** 文件。

```
module.exports = {
  rootDir: '.',
  displayName: {
    name: 'enzyme-setup',
    color: 'blue'
  },
  runner: 'jest-runner',
  verbose: true,
  errorOnDeprecated: true,
  roots: ['./Tests'],
  moduleFileExtensions: ['js', 'jsx'],
  setupFilesAfterEnv: ['<rootDir>Tests/setupTest.js']
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的**测试**文件夹中创建一个 **setupTest.js** 文件。

```
import { configure } from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'

configure({
  adapter: new Adapter()
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的 **package.json** 文件。
T3![package.json](img/0578e9331f373a4990e4aff417bec9ae.png)T5】

在**组件**文件夹中有 4 个文件。

```
 type nul > App.js && type nul > Form.js && type nul > Header.js && type nul > List.js 
```

Enter fullscreen mode Exit fullscreen mode

我们的 **Header.js** 文件。

```
import React from 'react'

export default function Header({ message, handleToggleTheme, theme }) {
  return (
    <div className="header">
      <h1>{message}</h1>
      <button className="right" onClick={handleToggleTheme}>
        <i className={theme}></i>
      </button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 **Form.js** 文件。

```
import React from 'react'

export default function Form({ handleChange, value, handleClick }) {
  return (
    <div className="form">
      <input
        className="form-control"
        type="text"
        onChange={handleChange}
        value={value}
      />
      <button className="btn" onClick={handleClick}>
        Submit
      </button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 **List.js** 文件。

```
import React from 'react'

export default function List({ items }) {
  return (
    <ul className="list">
      {items.map(item => (
        <li className="list-item" key={item}>{item}</li>
      ))}
    </ul>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 **App.js** 文件。

```
import React, { Component } from 'react'
import Header from './Header'
import List from './List'
import Form from './Form'

export default class App extends Component {

  state = {
    listItem: '',
    items: [],
    isDarkTheme: false
  }

  handleChange = ({ target: { value } }) => {
    this.setState({
      listItem: value
    })
  }

  handleClick = () => {
    this.setState({
      items: [...this.state.items, this.state.listItem],
      listItem: ''
    })
  }

  handleToggleTheme = () => {
    this.setState({
      isDarkTheme: !this.state.isDarkTheme
    })
  }

  render() {
    const theme = this.state.isDarkTheme ? 'dark' : 'light'
    return (
      <div className={theme}>
        <Header
          theme={theme}
          message={this.props.message}
          handleToggleTheme={this.state.handleToggleTheme}
        />
        <Form
          handleChange={this.state.handleChange}
          value={this.state.listItem}
          handleClick={this.state.handleClick}
        />
        <List items={this.state.items} />
      </div>
    )
  }

}

App.defaultProps = {
  message: 'Hello World'
} 
```

Enter fullscreen mode Exit fullscreen mode

在 **Tests** 文件夹中创建一个 **index.test.js** 文件。

```
import React from 'react'
import App from '../components/App'
import Header from '../components/Header'
import Form from '../components/Form'
import List from '../components/List'
import { shallow, mount } from 'enzyme'

describe('Test App component', () => {
  let wrapper;

  beforeAll(() => {
    wrapper = shallow(<App />)
  })

  it('should not return an error', () => {
    expect(wrapper).toMatchSnapshot()
    console.log(wrapper.debug())
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的终端运行这个命令

```
 npm t 
```

Enter fullscreen mode Exit fullscreen mode

如果它没有抛出一个**错误**并且它**通过了**，那么你就可以开始了。

**浅**渲染和**全挂载**渲染有什么区别？

**浅**和**山**都给我们提供了这个非常有用的方法**调试**。

尝试将我们的 **index.test.js** 文件更新成这样。

```
import React from 'react'
import App from '../components/App'
import Header from '../components/Header'
import Form from '../components/Form'
import List from '../components/List'
import { shallow, mount } from 'enzyme'

describe('Test App component', () => {
  let shallowWrapper, mountWrapper;

  beforeAll(() => {
    shallowWrapper = shallow(<App />)
    mountWrapper = mount(<App />)

    console.log(shallowWrapper)
    console.log(mountWrapper)
  })

}) 
```

Enter fullscreen mode Exit fullscreen mode

### 结构使用调试方法。

**第一个** console.log 是这样的。

```
 console.log Tests/index.test.js:12
    <div className="light">
      <Header theme="light" message="Hello World" handleToggleTheme={[Function]} 
        />
      <Form handleChange={[Function]} value="" handleClick={[Function]} />
      <List items={{...}} />
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

**秒** console.log 是这样的。

```
 console.log Tests/index.test.js:13
    <App message="Hello World">
      <div className="light">
        <Header theme="light" message="Hello World" handleToggleTheme={[Function]}>
          <div className="header">
            <h1>
              Hello World
            </h1>
            <button className="right" onClick={[Function]}>
              <i className="light" />
            </button>
          </div>
        </Header>
        <Form handleChange={[Function]} value="" handleClick={[Function]}>
          <div className="form">
            <input className="form-control" type="text" onChange={[Function]} value="" />
            <button className="btn" onClick={[Function]} />
          </div>
        </Form>
        <List items={{...}}>
          <ul className="list" />
        </List>
      </div>
    </App> 
```

Enter fullscreen mode Exit fullscreen mode

**debug** 方法基本上给了我们组件的**结构**。当我们在 **shallow** 上使用它时，它不会给出我们组件的完整结构，我们看不到我们的 **Header** 、 **Form** 和 **List** 组件的 **JSX** 结构，但是当我们在 **mount** 上使用它时，它会给出我们组件的完整结构，直到我们的子组件使用的每个 **JSX** 元素。

**酶**给我们提供了有用的方法。

#### at(索引:数字)

根据给定的索引返回包装元素。

在我们的 **App** 组件上使用**浅**和**挂载**的区别。

```
 it('should have an "App" component "at" index of 0', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.at(0).type()).toBe(App);
  });

  it('should return an App', () => {
    let wrapper = mount(<App />);
    expect(wrapper.at(0).type()).toBe(App)
  }); 
```

Enter fullscreen mode Exit fullscreen mode

**第一次**测试失败，但是**第二次**测试通过，原因是在我们的**浅化**组件中索引 **0** 处的元素类型是 **div** ，不是 **App** ，而是在我们的**安装端**组件中是 **App** 参考**结构**部分将 **App** 更改为 **div**

#### childAt(索引:数字)

返回指定索引处的子级的新包装。

```
 it('should have a child component of type "Header" at "index" of 0', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.childAt(0).type()).toBe(Header);
  });

  it('should have a child element of type "div" at "index" of 0', () => {
    let wrapper = mount(<App />);
    expect(wrapper.childAt(0).type()).toBe('div')
  }); 
```

Enter fullscreen mode Exit fullscreen mode

基于我们**浅化 App** 的**结构**，第一个孩子应该是**头**，在我们**挂载 App** 中第一个孩子应该是 div。这两项测试应该通过。

#### 查找(选择器:酶选择器)

基本上是找到匹配给定选择器的每个节点。

**选择器**。

*   **find('div')** =查找当前包装上的每个' div '元素。
*   **find('div.something')** =在当前包装器上查找每个带有' something '类的' div '元素。
*   **find(' div[title = " okinawa "])**=查找每个属性为“title”且值为“Okinawa”的“div”元素。

*   **查找(' #okinawa')** =查找每一个 id 为“okinawa”的元素。

*   **找到(’。okinawa))**=查找每一个类为“Okinawa”的元素。

*   **find(' div # okinawa>span))**=查找作为 id 为“Okinawa”的“div”的
    直接子元素的每个“span”元素

*   **find(' div . okinawa+span ')**=查找位于类为“Okinawa”的“div”元素之后的每个“span”元素

*   **find(' div . okinawa span ')**=查找类为“Okinawa”的“div”元素中的每个“span”元素

*   **find(SomeComponent)** =查找带有“some component”
    控制符的每个元素

```
 function App({ children }){
     return (
       <div>
           {children}
        </div>
     )
   }   

   function SomeComponent(){
      return (
        <div>
         <h1>
            Hi!
         </h1>
        </div>
      )
   }

   it('should have length of "1" when finding "SomeComponent" comp', () => {
    const wrapper = shallow(<App>
         <SomeComponent />
     </App>
     )
    expect(wrapper.find(SomeComponent)).toHaveLength(1);
   }); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到所有有效的选择器。

#### 最近(选择器:酶选择器)

查找与选择器匹配的最接近的父级。它从自身开始遍历每个节点。

```
 it('should have an h1 with a text of "Hello World"', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.find(Header).closest('div.light')).toHaveLength(1);
  });

  it('should have a parent element of "div" with a class of "light"', () => {
    let wrapper = mount(<App />);
    expect(wrapper.find(Header).closest('div.light')).toHaveLength(1);
  }) 
```

Enter fullscreen mode Exit fullscreen mode

这两项测试应该会通过。

#### 包含(节点:节点|节点[])

测试包含包装是否有匹配的一个或多个子级。

```
 it('should have a node of <Header /> and the right props', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.contains(
      <Header theme="light" message="Hello World" handleToggleTheme= 
           {wrapper.instance().handleToggleTheme} />
    )).toBeTruthy();
  });

  it('should contain these two nodes', () => {
    const wrapper = mount(<App />);
    expect(wrapper.contains([
      <h1>Hi</h1>,
      <button className="right" onClick={wrapper.instance().handleToggleTheme}>
        <i className="light" />
      </button>
    ])).toBeTruthy();
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 **instance()** 方法从组件实例中获取对 **handleToggleTheme** 函数的引用。关于**实例**方法的更多信息将在后面介绍。这些测试应该会通过。

#### 包含 all matching elements(nodes:nodes[])

必须匹配当前包装上的所有节点。

```
 it('should have these two nodes when shallow mounting', () => {
   let wrapper = shallow(<App />);
    wrapper.setState({ listItem: '1' })
    expect(wrapper.containsAllMatchingElements(
      [
        <Form handleChange={wrapper.instance().handleChange} value="1" handleClick={wrapper.instance().handleClick} />,
        <Header theme="light" message="Hello World" handleToggleTheme={wrapper.instance().handleToggleTheme} />
      ]
    )).toBeTruthy();
  });

  it('should have these two nodes when mounting', () => {
    let wrapper = mount(<App />);
    expect(wrapper.containsAllMatchingElements([
      <h1>Hi</h1>,
      <button className="right" onClick={wrapper.instance().handleToggleTheme}>
        <i className="light" />
      </button>
    ])).toBeTruthy();
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 **setState** 来更新我们**州**的一个房产的价值。其工作原理与 **React 的** setState 相同。`this.setState({property: newValue})`。这些测试应该会通过。

#### 包含 any matching elements(nodes:nodes[])

必须至少匹配当前包装上的一个节点。

```
 it('should this Form with the right props', () => {
    expect(wrapper.containsAnyMatchingElements(
      [
        <Form handleChange={wrapper.instance().handleChange} value="1" handleClick={wrapper.instance().handleClick} />,
      ]
    )).toBeTruthy();
  });

  it('should return true because the "i" element is right while "div" element is not the right structure', () =>{
    expect(wrapper.containsAnyMatchingElements([
      <div className="form">
      </div>,
      <i className="light" />
    ])).toBeTruthy();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

你可能想知道为什么我们在**变浅的**部分有一个值为“1”的**值，那是因为我们在前面的部分使用了 **setState** 并更新了我们的 **listItem** 值为 **1** 。这些测试应该会通过。**

#### 先()

类似于 **at(0)** refer。

#### hasClass(类:字符串)

测试当前节点是否具有属性**类名**并检查其值。

```
 it('should have a class of "light"', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.hasClass('light')).toBeTruthy();
   });

   it('should have a class of "form-control"', () => 
     wrapper = mount(<App />);  
    {
    expect(wrapper.find(Form).find('#form').childAt(0).hasClass('form-control')).toBeTruthy();
  }) 
```

Enter fullscreen mode Exit fullscreen mode

这些测试应该会通过。

### html()

返回当前包装器的原始 html 字符串。

```
 it('should return the correct string', () => {
    let wrapper = shallow(<App >);
    expect(wrapper.childAt(2).html()).toBe('<ul class="list"></ul>')
  });

  it('should have an element with an id of "form"', () => {
    let wrapper = mount(<App >);
    wrapper.setProps({ message: 'Hi' });
    expect(wrapper.find('h1').html()).toBe('<h1>Hi</h1>')
  }) 
```

Enter fullscreen mode Exit fullscreen mode

这些测试也应该通过。

#### 实例()

返回当前包装的当前类实例，当在功能组件上使用时返回 null。**实例**方法只能在根节点上使用。

```
 it('should be an instance of App', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.instance()).toBeInstanceOf(App);
  });

  it('should be an instance of App', () => {
    let wrapper = mount(<App />);
    expect(wrapper.instance()).toBeInstanceOf(App);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这些测试应该会通过。

#### 调用(函数属性名)(..参数)

```
 it('should have a prop of "value" with a value of "12344"', () => {
    let wrapper = shallow(<App />);
    wrapper.find(Form).invoke('handleChange')({ target: { value: '12344' } });
    expect(wrapper.find(Form).prop('value')).toBe('12344');
  });

  it('should truthy value of prop "isDarkTheme"', () => {
    let wrapper = mount(<App />);
    wrapper.find(Header).invoke('handleToggleTheme')()
    expect(wrapper.state('isDarkTheme')).toBeTruthy();
  }) 
```

Enter fullscreen mode Exit fullscreen mode

这些测试也应该通过。我想你可能想知道我正在传递一个带有**目标**属性的对象，它的值是另一个带有**值**属性的对象的值，因为我的 **handleChange** 函数看起来像这样

```
 handleChange = ({ target: { value } }) => {
    this.setState({
      listItem: value
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 是(选择器:酶选择器)

检查选择器是否与当前包装匹配。

```
 it('should return false when checking with ".is"', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.find(List).find('ul').is('.list')).toBeFalsy();
  });

  it('should return true when checking with ".is"', () => {
    let wrapper = mount(<App />);
    expect(wrapper.find(List).find('ul').is('.list')).toBeTruthy();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

第一次测试失败并抛出错误的原因是因为
我们的元素结构在使用 **shallow** 时看起来像这样

```
 <div className="light">
      <Header theme="light" message="Hello World" handleToggleTheme={[Function]} 
        />
      <Form handleChange={[Function]} value="" handleClick={[Function]} />
      <List items={{...}} />
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

它不会渲染 ul 元素，但是当我们在 T2 山使用它时，它会工作。

#### isEmptyRender()

如果当前包装器返回`null`或`false`，则返回 true。

```
 it('should not be falsy because "App" does not return neither null or false', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.isEmptyRender()).toBeFalsy();
  });

  it('should return "Nothing" literally', () => {
    class Nothing extends React.Component {
      render() {
        return (
          null
        )
      }
    }
    let wrapper = mount(<Nothing />);
    expect(wrapper.isEmptyRender()).toBeTruthy();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这些测试应该会通过。由于我们在**渲染**方法上返回了 **null** 的原因，第二个测试通过了。

#### 键()

返回当前包装器的**键值**。

```
 it('should have a prop of items with a length of 2 and a key value of "japan"', () => {
    let wrapper = mount(<Form />);
    let form = wrapper.find(Form);
    form.invoke('handleChange')({ target: { value: 'okinawa' } });
    form.invoke('handleClick')();
    form.invoke('handleChange')({ target: { value: 'japan' } });
    form.invoke('handleClick')();

    expect(wrapper.find(List).prop('items')).toHaveLength(2);
    expect(wrapper.find(List).find('ul').childAt(1).key()).toBe('japan');
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 最后()

返回基于当前选定包装的最后一个节点。

```
 it('should return the last child type which is "List"', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.children().last().type()).toBe(List);
  });

  it('should return the last child type which is "div"', () => {
    let wrapper = mount(<App />)
    expect(wrapper.children().last().type()).toBe('div');
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 名称()

返回当前包装的“名称”。

```
 it('should return a name with a value of "div"', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.name()).toBe('div');
  });

  it('should return a name with a value of "App"', () => {
    let wrapper = mount(<App />);
    expect(wrapper.name()).toBe('App');
  }); 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果您在理解上有一点问题，请参考**结构**部分。

#### 过滤器(选择器:酶选择器)

它根据给定的选择器返回新的包装器。

```
 it('should have a prop of "item" with length of 3', () => {
    let wrapper = mount(<App />);
    let form = wrapper.find(Form);
    let values = ["ohio", "usa", "amawa"];
    values.forEach((value) => {
      form.invoke('handleChange')({ target: { value } });
      form.invoke('handleClick')();
    })

  expect(wrapper.find(List).find('ul li').filter('.list-item')).toHaveLength(3);

  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 道具()

返回当前包装器的道具对象

```
 it('should have a prop "items" with a value of []', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.find(List).props().items).toEqual([]);
  });

  it('should have a prop "message" with a value of "Hello World"', () => {
    let wrapper = mount(<App />);

    expect(wrapper.find(Header).props().message).toBe("Hello World");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 道具(按键:字符串)

返回当前包装的属性值。

```
 it('should have a prop "items" with a value of []', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.find(List).prop('items')).toEqual([]);
  });

  it('should have a prop "message" with a value of "Hello World"', () => {
    let wrapper = mount(<App />);

    expect(wrapper.find(Header).prop('message')).toBe("Hello World");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### setProps(newProps: any)

设置根节点的新 props 对象。它只能在根节点上使用。

```
 it('should have an updated prop "message" with a value of "What the fun"', () => {
    let wrapper = mount(<App />);
    wrapper.setProps({ message: 'What the fun' })
    expect(wrapper.find(Header).prop('message')).toBe("What the fun");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### setState(newState : any，callbackFunc: Function)

设置根节点的新状态对象。它只能在根节点上使用。

```
 it('should have an updated prop "isDarkTheme" with a value of true', () => {
    let wrapper = mount(<App />);
    wrapper.setState({ isDarkTheme: true });
    expect(wrapper.state('isDarkTheme')).toBeTruthy();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 模拟(事件:字符串，...参数)

调用当前包装上的事件。

```
 it('should have an updated value of "1234"', () => {
    let wrapper = mount(<App />);
    wrapper.find('input').simulate('change', { target: { value: '1234' } });
    expect(wrapper.state('listItem')).toBe('1234');
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 状态(按键:字符串)

返回状态属性的值。

```
 it('should a input with a value of "abc"', () => {
    let wrapper = shallow(<App />);
    wrapper.setState({ listItem: 'abc' });
    expect(wrapper.state('listItem')).toBe('abc');
  });

  it('should have an updated "message" prop with a value of "Hi"', () => {
    let wrapper = mount(<App />);
    wrapper.setProps({ message: 'Hi' });
    expect(wrapper.prop('message')).toBe('Hi');
  }) 
```

Enter fullscreen mode Exit fullscreen mode

#### 正文()

返回当前包装的文本。

```
 it('should a text of "Hello World"', () => {
    let wrapper = mount(<App />);
    expect(wrapper.find('h1').text()).toBe('Hello World');
  }); 
```

Enter fullscreen mode Exit fullscreen mode

### (类型)

返回当前包装的类型。

```
 it('should return the App', () => {
    let wrapper = shallow(<App />);
    expect(wrapper.at(0).type()).toBe('div');
  }); 

  it('should return the App', () => {
    let wrapper = mount(<App />);
    expect(wrapper.at(0).type()).toBe(App);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

查看这个[帖子](https://dev.to/flexdinesh/react-hooks-test-custom-hooks-with-enzyme-40ib)，为你的开发者制作你自己的**封面图片**。

## 感谢各位阅读这篇帖子。

# 过得愉快😃！。