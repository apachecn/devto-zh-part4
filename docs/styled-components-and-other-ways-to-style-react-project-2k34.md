# 样式化组件和其他样式化 React 项目的方法

> 原文：<https://dev.to/nabendu82/styled-components-and-other-ways-to-style-react-project-2k34>

我通常使用样式化的组件，大多数我在工作中使用的新 CSS。在任何 react 项目中，样式化组件的主要好处之一是，当项目越来越大时，才能真正理解。

大型复杂项目中使用普通 CSS 或 scss 的 CSS 很快就会变得复杂。参与该项目的新开发人员发现，做一个小小的 CSS 更改也真的很难。因为那是很小的 CSS 变化破坏了很多东西。

除此之外，这个刚刚样式化的组件适合创建组件的 react 生态系统。我们很快就会在我们将要创造的例子中看到道具的力量。

现在有另外两种流行的方法，在 react 项目中使用 CSS。

### 1。CSS 样式表

首先是通常的 CSS 样式表。我们可以在一个普通的组件中使用如下的。我们有一个普通的功能组件叫做 **SolidBox** 。

```
 import React from 'react';
    import './SolidBox.css';

    const SolidBox = () => (
      <div className="SolidBox">
        <p className="SolidBox_content">Get started with CSS styling</p>
      </div>
    );

    export default SolidBox; 
```

然后，我们导入一个名为 SolidBox.css 的 css 文件，并像在普通的 HTML、CSS 设置中一样使用它。

```
 .SolidBox {
      margin: 40px;
      border: 5px solid black;
    }

    .SolidBox_content {
      font-size: 15px;
      text-align: center;
    } 
```

但是我通常在如下 react 项目的根 **index.js** 文件中使用它。

```
 import React from 'react';
    import ReactDOM from 'react-dom';
    import Routes from './routes';
    import store from './store';
    import { Provider } from 'react-redux';
    import './index.css';

    ReactDOM.render(
        <Provider store={ store }>
            <Routes />
        </Provider>,
      document.getElementById('root')
    ); 
```

还有 **index.css** ，路由颜色 HTML 和 body css 如下图。

```
 :root {
      --yellow: #ffc600;
      --black: #272727;
    }

    html {
      box-sizing: border-box;
      font-family: "Gloria Hallelujah", cursive, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';
      font-weight: 900;
      font-size: 10px;
      color: var(--black);
      text-shadow: 0 2px 0 rgba(0, 0, 0, 0.07);
    }

    *,
    *:before,
    *:after {
      box-sizing: inherit;
    }

    body {
      background:#018DED url(http://unsplash.it/1500/1000?image=881&blur=50);
      background-size:cover;
      min-height: calc(100vh - 100px);
      background-attachment: fixed;
      letter-spacing: -1px;
    }

    h1,
    h2,
    h3,
    h4,
    h5,
    h6 {
      margin: 0 0 5px 0;
    } 
```

### 2。内嵌样式

第二种方式是内联样式。但是它不同于普通的 HTML 内联样式。需要注意的一件主要事情是 CSS 属性的每个键都是用 **camelCase** 编写的。并且**引号**也是指定值所必需的。于是 **font-size** 变成了 **fontSize** 而 **15px** 变成了**‘15px’**。

无论我们使用什么样的 HTML 元素，都必须指定 style = like*style = { renderButtonDisabled }*

我主要用它们来做一些小样式的修改，只在组件中使用。如下所示的按钮示例。

```
 const renderButton = {
        backgroundColor: '#f5f5f5',
        color: 'black',
        border: '2px solid #f5f5f5',
        borderRadius: '20px',
        cursor: 'pointer',
        fontSize: '1.6rem',
        fontWeight: '600',
        height: '40px',
        minWidth: '200px',
        padding: '0 24px',
        transition: 'box-shadow .3s',
        fontFamily: 'inherit',
        marginLeft: '4px'
    }

    ....

    <input type="button" **style={renderButton}** onClick={ this.handleFormSubmit } value="Submit" /> 
```

还有另一种方法来使用它们，我用它们来做很小的 CSS 改变，比如像下面这样将文本居中。

```
 <div style={{ textAlign: 'center' }}>
      I am a centered text.                       
    </div> 
```

### 3。样式组件

第三种方法是使用类似 styled-components 的库，这是 CSS-in-JS 库的一种类型。但它是最受欢迎的一种，任何现代的 react 项目都应该使用它。尤其是我从零开始的那个。

你必须安装 npm 才能使用它，详情见[链接](https://www.styled-components.com/docs/basics#installation)。

让我们看看下面这个真实的例子，它是我为了解决一个编码挑战问题而创建的。其中**的 PageWrapper、HeaderTitle、FlexResult、FlexBoxRev** 都是**样式的组件**。我们在一个名为 **StyledComponents.js** 的不同文件中定义它们。这个文件包含所有的样式组件，但是我们只导入我们需要的组件。

```
 import React, { Component } from 'react';
    import { connect } from 'react-redux';
   import { PageWrapper, HeaderTitle, FlexResult, FlexBoxRev } from '../../molecules/StyledComponents';

    class ResultPage extends Component {
        render() {
            return (
                <PageWrapper wider>
                    <FlexResult>
                        <FlexBoxRev toCenter noRightMargin>
                            <HeaderTitle toWhite>Success! Congratulations on finding Falcone.</HeaderTitle>
                            <HeaderTitle small topMargin>Time Taken: {this.props.timeData}</HeaderTitle>
                        </FlexBoxRev>
                    </FlexResult>
                </PageWrapper>
            );
        }

    }

    const mapStateToProps = ({ dataReducer }) => ({
        findData: dataReducer.findData,
        timeData: dataReducer.timeData
    });

    export default connect(mapStateToProps, null)(ResultPage); 
```

包含所有样式组件的文件。

```
 import styled from 'styled-components';

    const PageWrapper = styled.div`
      max-width: ${ props => (props.wider ? '96%' : '85%') };
      margin: 1% auto;
    `;

    const GridHome = styled.div`
      display: grid;
      grid-template-rows: 80px 200px 80px;
      grid-row-gap: 10px;
      align-items: center;
    `;

    const HeaderWrapper = styled.div`
        background: var(--yellow);
        height: 5%;
        padding: 1%;
    `;

    const FlexResult = styled.div`
        display: flex;
        align-items: center;
        justify-content: center;
        height: 100vh;
    `;

    const FlexBoxRev = styled.div`
        display: flex;
        flex-direction: column;
        margin-right: ${ props => (props.noRightMargin ? '' : '8%') };
        align-items: ${ props => (props.toCenter ? 'center' : 'flex-start') };
        justify-content: center;
    `;

    const FlexBoxNav = styled.div`
      display: flex;
      align-items: center;
      justify-content: ${ props => (props.toBtw ? 'space-between' : 'space-around') };  
    `;

    const HeaderTitle = styled.div`
        font-size: ${ props => (props.small ? '18px' : '25px') };
        margin-top: ${ props => (props.topMargin ? '5%' : '') };
        text-align: ${ props => (props.toCenter ? 'center' : '') };
        font-weight: bold;
        line-height: 1.25;
        color: ${ props => (props.toWhite ? '#f5f5f5' : '#333333') };
    `;

 export { PageWrapper, GridHome, FlexBoxNav, HeaderTitle, HeaderWrapper, FlexBoxRev, FlexRevRadio, FlexResult }; 
```

需要注意的一件主要事情是，我们正在使用一种叫做道具的东西。因为有了道具，我们可以重用样式化的组件。让我们以 PageWrapper 为例。我们可以用它作为`<PageWrapper>…</PageWrapper>`，将*最大宽度设置为 85%* 。
如果喜欢* *..** ，将*最大宽度设为 96%*

```
 const PageWrapper = styled.div`
      max-width: ${ props => (props.wider ? '96%' : '85%') };
      margin: 1% auto;
    `; 
```

另外要注意的是，我们可以通过`<HeaderTitle small topMargin>Time Taken: {this.props.timeData}</HeaderTitle>`
使用多个道具

```
 const HeaderTitle = styled.div`
        font-size: ${ props => (props.small ? '18px' : '25px') };
        margin-top: ${ props => (props.topMargin ? '5%' : '') };
        text-align: ${ props => (props.toCenter ? 'center' : '') };
        font-weight: bold;
        line-height: 1.25;
        color: ${ props => (props.toWhite ? '#f5f5f5' : '#333333') };
    `; 
```

希望你喜欢这篇文章。你可以在我的 github repo [链接](https://github.com/nabendu82/geekSolution)中找到完整的项目。
后会有期:)