# 写了我的第一个巴别塔插件！(很简单)

> 原文：<https://dev.to/sanketmaru/wrote-my-first-babel-plugin-its-simple-1l79>

要写一个巴别塔插件，首先你需要了解巴别塔插件是如何工作的。我将写下我需要写插件的东西:-

1.  浏览巴别塔手册的基础知识。基础知识将有助于理解什么是 AST，解析，AST 的遍历(巴别塔的阶段)。
2.  玩 [Ast-explorer](https://astexplorer.net) 这将有助于识别 Ast 的不同访问者，以及您希望抓住哪个访问者来操纵路径。
3.  用 web pack config[create app . dev](https://createapp.dev/)设置一个 CRA (create-react-app)或一个简单的节点项目对于快速完成工作很有用。
4.  调试，调试，调试........调试其他插件代码。设置 vscode 来调试 node_modules 中的 babel 插件。我将会在一个独立的博客上报道调试插件和开发巴别塔插件时遇到的困难

```
 {  "type":  "node",  "request":  "launch",  "name":  "Launch Program",  "program":  "${workspaceFolder}/scripts/build.js"  } 
```

**动机**

有可能您的部分代码无法在移动设备上执行，功能对用户完全隐藏。代码仍然驻留在包中，由用户下载。

这个巴别塔插件将删除开发者标记为桌面的代码，并且永远不会包含在程序块中。

你是怎么做到的？很简单..

```
visitor: {
    Program(programPath) {
      programPath.traverse({
        ClassMethod(path) {
          if (path.node.key.name.endsWith(classMethodEnv)) {
            path.remove();
          }
        },
        JSXElement(path) {
          path.node.openingElement.attributes.forEach(ele => {
            if (ele.name.name === jsxEnv)
              path.remove();
          });
        }
      });
    }
  } 
```

我们希望转换 AST，使得每个类方法和 JSXElement 在匹配条件时都被访问和删除。
[巴别塔:游客](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md#toc-visitors)

> 我让插件使用一个程序(路径)访问者，然后为所有的访问者做一个 path.traverse。这样做是为了在插件第一时间显式处理整个文件。如果没有做到这一点，那么它不能确保插件的排序，你的访问者可能不会被访问，因为它已经被其他插件编译成不同的状态。

你可以让你的插件工作的另一个选择是使用一个类访问者，并从那里遍历类方法。

现在我们已经完成了[插件](https://github.com/sanketmaru/babel-plugin-mobile-optimizer-react/blob/master/index.js)，我们将使用它作为 react 应用来测试它。

*发布为 npm 库[此处](https://www.npmjs.com/package/babel-plugin-mobile-optimizer-react)T3】*

**用途**

您的 React 组件

```
export default class App extends Component {

handleClick_mobile() {
  console.log("Mobile Handle Click");
}

handleClick_desktop() {
  console.log("Desktop handle Click");
}

render() {
  return (
    <div>
      <table>
        <tr data-mobile onClick={this.handleClick_mobile.bind(this)}>
          <td>Mobile</td>
        </tr>
        <tr data-desktop onClick={this.handleClick_desktop.bind(this)}>
          <td>Web</td>
        </tr>
      </table>
    </div>
  );
}
} 
```

将被传送到:-

```
export default class App extends Component {

handleClick_mobile() {
  console.log("Mobile Handle Click");
}

render() {
  return (
    <div>
      <table>
        <tr data-mobile onClick={this.handleClick_mobile.bind(this)}>
          <td>Mobile</td>
        </tr>
      </table>
    </div>
  );
}
} 
```

上面，插件已经移除了名为 *data-desktop* 和以 *_desktop* 结尾的类方法的 tr

我们已经将环境变量作为
传递给插件

```
plugins: [
[require.resolve('babel-plugin-mobile-optimizer-react'), {
  "JSX_ENV": "data-desktop", // remove jsx code matching with name as data-desktop
  "CLASS_METHOD_ENV": "_desktop" // remove class method ending with _desktop
}]
] 
```

希望这对你自己写巴别塔插件有帮助。完整的代码可以在我的 github repo 上找到

插件:-[babel-plugin-mobile-optimizer-react](https://github.com/sanketmaru/babel-plugin-mobile-optimizer-react)
在 react app 中的用法:- [react-labs](https://github.com/sanketmaru/react-labs)

在下一篇博客中，我会提到调试 babel 插件以及如何设置 vscode 来调试它。