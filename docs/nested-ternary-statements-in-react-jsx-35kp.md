# React JSX 中的嵌套三元语句

> 原文：<https://dev.to/samba_code/nested-ternary-statements-in-react-jsx-35kp>

在发现我们的 eslint 已经停止运行*一段时间*后，我今天花了大部分时间试图修复我们 React 应用程序中的一大堆 eslint 和 a11y 问题。我打了一个相当有趣的:

```
/*eslint no-nested-ternary: "error"*/ 
```

也就是说，不要这样做:

```
const drink = dislikeCoke ? 'fanta' : likesCherry ? 'cherryCoke' : 'dietCoke'; 
```

我认为一般来说，每个人都可以得到支持。这是不可读的代码，即使用缩进分割成多行，也应该分解成 if 语句。

然而，这是 React 中非常常见的模式，我们可以使用三元语句在组件中进行条件呈现。

```
 <h1>Data Loader!</h1>
        { this.state.loading ? 
        <h2>It is Loading.</h2>
          : this.state.data ? 
          <h2>{this.state.data}</h2>
          :<h2>There was no result!</h2> 
        } 
```

(这是一个很做作的例子)。

我在网上查了一会儿，发现最好的替代方法是将三元组的第二部分提取到一个无状态的功能组件中。组件仍然可以存在于同一个文件中，所以它仍然很容易理解，而且我发现这是封装 UI 代码的一种很好的方式。

```
const DataDisplay = ({data}) => data ? 
          <h2>{data}</h2>
          :<h2>There was no result!</h2> 

          ...

          { this.state.loading ? 
        <h2>It is Loading.</h2>
          : <DataDisplay data={this.state.data}/>
        } 
```

完整示例代码如下:
[https://codepen.io/samberic/embed/JQKbMd?height=600&default-tab=result&embed-version=2](https://codepen.io/samberic/embed/JQKbMd?height=600&default-tab=result&embed-version=2)