# 如何用代码画 ER 图

> 原文：<https://dev.to/sakko/how-i-code-not-draw-my-er-diagram-4kc3>

又见面了，

这一次我想分享我是如何只用代码编写 ER 图的。

我用的工具是 PlantUML。planttext.com 有一个在线工具

> 我为什么要这么做？我讨厌使用鼠标，但我喜欢能够在 git 中对我的 ER 图进行版本控制。

## 准备

### 在线

如果你不想设置任何东西，就继续使用 planttext.com

### 局部/原子

安装这些软件包

`plantuml plantuml-viewer`或`plantuml plantuml-preview`

然后为您的 ER 图创建一个文件。

```
touch ~/Desktop/example.pu 
```

Enter fullscreen mode Exit fullscreen mode

对于所有 PlantUML 文件，我通常选择`*.pu`。

## 好玩的部分

把这个放到你的文件中，然后使用 cmd+shift+p 切换预览，然后输入 plantuml。

```
@startuml
  skinparam linetype ortho
  skinparam packageStyle rectangle
  skinparam shadowing false
  skinparam class {
    BackgroundColor White
    BorderColor Black
    ArrowColor Black
  }
  hide members
  hide circle

  User1 ||-up-|{ Role1
  User2 ||-down-|{ Role2
  User3 ||-left-|{ Role3
  User4 ||-right-|{ Role4

@enduml 
```

Enter fullscreen mode Exit fullscreen mode

这是`One and Only One`到`One or Many`

如果你不指定方向，它将默认为向下。

[![](img/b0e27ba7356ef07d04e66deadf65ec02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BIkiedhE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dh50mb15uce3uuas8lpd.png)

## 更多选项

```
@startuml
  skinparam linetype ortho
  skinparam packageStyle rectangle
  skinparam shadowing false
  skinparam class {
    BackgroundColor White
    BorderColor Black
    ArrowColor Black
  }
  hide members
  hide circle

  User1 --|{ Role1
  User2 o--|{ Role2
  User3 |o--|{ Role3
  User4 --o{ Role4
  User5 --{ Role5

@enduml 
```

Enter fullscreen mode Exit fullscreen mode

我无法让`o--|{`正常工作。

[![](img/24886b4fac479a9ec5572b7ea793ff4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WRWxyRup--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/stua17mhxecajy5f56i5.png)

## 反之亦然

```
@startuml
  skinparam linetype ortho
  skinparam packageStyle rectangle
  skinparam shadowing false
  skinparam class {
    BackgroundColor White
    BorderColor Black
    ArrowColor Black
  }
  hide members
  hide circle

  User1 }|-- Role1
  User2 }o-- Role2
  User3 }--|| Role3
  User4 }--o| Role4
  User5 }--o Role5

@enduml 
```

Enter fullscreen mode Exit fullscreen mode

你可以试着把它换成另一种方式。

[![](img/dfa3c9f7e5be036324c374c7dbc912cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H3bGehrw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xj6hngposzy4ihxx3fe8.png)

## 显示属性

通过移除`hide members`(第 10 行)，您可以向您的类添加属性。如果重新添加，所有属性都将被隐藏。

```
@startuml
  skinparam linetype ortho
  skinparam packageStyle rectangle
  skinparam shadowing false
  skinparam class {
    BackgroundColor White
    BorderColor Black
    ArrowColor Black
  }
  hide circle
  class "User" as User1 {
    - int id
    - int age
    - string name
    + ([role]) roles()
    + ([user_role]) user_roles()
  }
  class Role {
    - int id
    - string name
  }
  class "UserRole" as User1Role {
    - int id
    - int user_id
    - int role_id
  }
  User1 ||-right-|{ User1Role
  Role ||-left-|{ User1Role
@enduml 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/c50e8ff40e75bb16c7c88b6fddbe2688.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7DR0qUm4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y7ly7hgd6of4nmr8dnj6.png)

就这样，希望你喜欢我的做法。

我还从 https://github.com/preston/railroady 的[那里得到了一个用于 rails 应用的宝石](https://github.com/preston/railroady)[https://github.com/sakKo/prailroady](https://github.com/sakKo/prailroady)

让我知道我是否能改进我的职位。谢谢。