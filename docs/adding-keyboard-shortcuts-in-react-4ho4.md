# 在 React 中添加键盘快捷键

> 原文：<https://dev.to/samba_code/adding-keyboard-shortcuts-in-react-4ho4>

键盘快捷键是 React 应用程序的一个很好的功能。它们帮助高级用户从你的应用程序中获得最大的收益，并且可以大大减少导航菜单等花费的时间。我目前正在建立一个基于 web 的 markdown 编辑器(因为这个世界肯定需要另一个这样的编辑器)。我的目标是完全的极简主义，这样当写作时，它只是原始文本，当你使用键盘快捷键时，会出现一个预览窗格。

快速的谷歌搜索显示出[反应热键](https://github.com/greena13/react-hotkeys)，这对这项任务来说似乎很棒。配置和设置非常简单。

```
const keyMap = {
    PREVIEW: "ctrl+shift+p",
};
class App extends Component {

    handlers = {
        PREVIEW: event => this.setState((state) => {
            return {
                ...state,
                preview: !state.preview
            }
        })

    };
...
<div className="App">
                <GlobalHotKeys keyMap={keyMap} handlers={this.handlers}/>
... the rest of your code here.... 
```

**keymap** 是一个动作列表，你可以将这些动作映射到你想要的快捷键上(你可以为你的命令起任何名字)。 **handlers** _ 是一个对象，将命令名映射到一个事件处理函数。在示例中，我更新了我的状态中的预览字段，该字段用于在我的应用程序中切换类名，以显示或隐藏预览 div。

将这段代码放入我的应用程序后，快捷键第一次起作用了，**，除了当我的光标在文本区域**的时候。鉴于我的应用程序几乎 100%是文本区，这是一个问题！我发现默认情况下，react-hotkeys 禁止快捷方式从文本区域和其他输入中触发，这使得
完全有意义。构建 webapps 的标准是快捷方式是单个字母(例如，“c”将在 gmail 中编写一封新邮件)，如果你正在打印一封邮件，你不希望它触发你的快捷方式。

react-hotkeys 有一个配置方法，允许你修改默认值。将此配置放在我的组件中会使热键的工作与焦点无关:

```
configure({
    ignoreTags: []
}) 
```

简单！