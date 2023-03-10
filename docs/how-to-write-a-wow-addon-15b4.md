# “如何编写一个 WoW 插件”

> 原文：<https://dev.to/joegannon/how-to-write-a-wow-addon-15b4>

所以你想写一个 WoW 插件？好吧，你最好习惯蹩脚的文件，旧网站，和不足的例子。事实上，这一切都是社区驱动的，没有任何来自暴雪的官方消息，这并没有让事情变得更容易。我最近写了我的第一个经典插件，名为[轨道销售](https://github.com/JoeGannon/TrackSales)，所以我知道你的痛苦。不过，这并不完全是悲观的看法，实际上只需要理解几个关键概念，其他的一切都会水到渠成。虽然本指南不是专门针对零售版或经典版的，但请使用零售版。我写这个用的版本是`80200`。

首先，如果你正在为你的插件而挣扎，我能给你的最好建议是不要去谷歌，不要去读文档，不要读这篇文章，而是去读其他的插件！我发现这无疑是学习如何做事的最好方法。一般来说，谷歌搜索和阅读文档都是很好的工具，但是在开发时，没有什么比看看其他人是如何做事情更好的了。以更容易理解的小插件为目标。阅读别人的代码永远是学习的好方法。你会惊讶于仅仅通过阅读别人的代码就能学到很多东西。克隆您最喜欢的开源项目，并在某一天检查它！

## 你好阿泽罗斯

我们将从 Hello World 示例开始。在`_retail_/Interface/Addons`中创建一个名为`MyAddon`的文件夹，在里面创建一个名为`MyAddon.lua`的 lua 文件。将该语句`print("Hello Azeroth")`放入文件中。

接下来我们需要告诉 WoW 关于我们的插件，这是在`toc`文件中完成的。创建一个名为`MyAddon.toc`的新文件，并添加下面的代码片段。

```
# Your client version is probably different than 80200
# In game run "/run print((select(4, GetBuildInfo())))" and use that for the interface number

## Interface: 80200
## Version: 1.0.0
## Title: My Addon 
## Notes: My First Addon
## Author: You 

MyAddon.lua
# additional lua files 
```

这告诉 WoW 我们的插件支持什么客户端版本(如果当前版本比`80200`新，你会看到“插件过期”的消息)，我们的插件名称，以及我们的插件需要的文件。我们稍后将回到`toc`文件，但是现在加载 WoW。在角色选择屏幕上查看`My Addon`是否在附加菜单中。如果不是，那么请再次检查所有内容是否正确。当你进入游戏时，你应该会看到“你好，艾泽拉斯”出现在聊天界面。

## 关键概念

构建一个插件真的可以归结为这些概念。剩下的就是填充你的插件的细节。

*   [事件](#events)
*   [挂钩](#hooks)
*   [保存的变量](#sv)
*   [Ace](#ace) (第三方助手，可选)
*   [控制台命令](#console)
*   [UI](#ui) (抱歉，我不会报道这个，想法见[扩展角色统计](https://gist.github.com/JoeGannon/288ac79a9edadd6b639c138f6320b865))
*   [测试](#testing)

## 事件

当游戏世界中发生各种动作时，事件被触发，例如打开邮件，学习新的法术等。我们可以用下面的代码注册事件
。

```
MyAddon = { }

local frame = CreateFrame("Frame")
frame:RegisterEvent("MAIL_SHOW")
frame:RegisterEvent("AUTOFOLLOW_BEGIN")

-- this syntax doesn't work in classic * 
frame:SetScript("OnEvent", function(this, event, ...)
    MyAddon[event](MyAddon, ...)
end)

function MyAddon:MAIL_SHOW()
    print("Mail Shown")
end

function MyAddon:AUTOFOLLOW_BEGIN(...)
    local player = ...
    print("Following "..player)
end 
```

`MyAddon = { }`本质上只是一个名称空间。Lua 实际上没有名称空间，但它和许多语言如`c#`、`java`和`c++`有相同的概念。这样我们可以避免使用全局变量。

不幸的是，除了“这就是你注册事件的方式”之外，我不能分享太多关于`CreateFrame`的内容。如果你想创建一个 UI，你也可以使用框架。

我不知道那个疯狂的语法是什么，我只知道它将有效地调度所有事件。它按照惯例
工作，所以你必须确保函数名与事件名匹配。如果你不想被这种惯例所束缚，那么`AceEvent`可以帮上忙。

当你打开邮箱并开始关注一个玩家时，你应该会看到消息出现。你可以在这里找到其他事件[。](https://wowwiki.fandom.com/wiki/Event_API)

## 挂钩

钩子与事件非常相似，除了钩子在`original`函数(内置 WoW 函数)之前或之后触发。
钩子允许你修改传递给原始函数的参数，或者如果你愿意，根本不调用原始函数...所以你可能会把事情搞砸。“安全钩”是使用的安全版本。为了创建一个挂钩，我们将使用`hooksecurefunc`，它是一个“安全的后挂钩”，对于您的用例，您可能需要前挂钩或后挂钩， [docs](https://wowwiki.fandom.com/wiki/Hooking_functions) 。

```
MyAddon = { }

hooksecurefunc("PickupContainerItem", function(...) MyAddon:PickupContainerItem(...) end)
hooksecurefunc("TakeInboxMoney", function(mailId) MyAddon:TakeInboxMoney(mailId) end)

function MyAddon:PickupContainerItem(container, slot)    
    local _, _, _, _, _, _, link = GetContainerItemInfo(container, slot)

    print(link)
end

function MyAddon:TakeInboxMoney(mailId)   
    local _, _, sender, subject, money = GetInboxHeaderInfo(mailId)    

    print("Sender "..sender, "Subject "..subject, "Money "..money)
end 
```

`GetContainerItemInfo`和`GetInboxHeaderInfo`是 WoW apis，你可以在这里找到其他函数[。如果你左键点击你的物品清单中的一个项目，你应该看到它的项目链接打印。测试`TakeInboxMoney`发送一些黄金到一个 alt 并从邮件中获取。不要自动启动它！那就需要`AutoLootMailItem`钩了。如果它不起作用，尝试禁用其他插件，然后再试一次。钩子可能有些复杂，参见](http://wowprogramming.com/docs/api_categories.html)[注释](https://wowwiki.fandom.com/wiki/API_hooksecurefunc)部分。或者，试试`AceHook`套餐。

## 保存的变量

`SavedVariables`是保存到磁盘的数值，可以在游戏过程中使用。注销时，这些值存储在 WTF 文件夹中(魔兽文本文件而非 WTF)。有两种类型的保存变量，`SavedVariables`和`SavedVariablesPerCharacter`。它们分别基于每个帐户和每个角色进行保存。不要像我一样被欺骗，它们只是全局变量！您只需将保存的变量添加到`toc`文件中。

```
## SavedVariables: AccountDB
## SavedVariablesPerCharacter: CharacterDB

# you can use multiple variables comma delimited 
```

在你的插件中，当有人第一次用它登录时，你通常会想要初始化它们。`PLAYER_LOGIN`事件
(如果使用 Ace 的话为`OnEnable`)可能是最好的地方，因为我发现一些 WoW apis 在其他时候还没有准备好。让我们添加一个登录计数器来演示。

```
MyAddon = { }

local frame = CreateFrame("Frame")

-- trigger event with /reloadui or /rl
frame:RegisterEvent("PLAYER_LOGIN")

frame:SetScript("OnEvent", function(this, event, ...)
    MyAddon[event](MyAddon, ...)
end)

function MyAddon:PLAYER_LOGIN()

    self:SetDefaults()

    AccountDB.loginCount = AccountDB.loginCount + 1  
    CharacterDB.loginCount = CharacterDB.loginCount + 1

    print("You've logged in "..AccountDB.loginCount.." times")
    print(UnitName("Player").." logged in "..CharacterDB.loginCount.." times")
end

function MyAddon:SetDefaults()

    if not AccountDB then 

        AccountDB = {
            loginCount = 0
        }

        print("Global Initialized")
    end

    if not CharacterDB then 

        CharacterDB = {
            loginCount = 0
        }

        print("Character Initialized")
    end
end 
```

## 王牌

如果你一直在阅读插件，你可能听说过[王牌](https://www.wowace.com/projects/ace3/pages/getting-started)。起初，我认为 WoW 和 Ace 之间有一些特殊的东西，但不，Ace 只不过是 WoW API 上的第三方包装库。它旨在使*一些*事情变得更简单，我说一些是因为对于您的用例来说，Ace 可能会碍事(我抛弃了 AceDB)。只有在你真的需要的时候才使用 Ace，一些模块有一个陡峭的学习曲线，很容易陷入 Ace 的细节而不是专注于重要的部分——写你的插件！在这篇文章中，我将展示如何使用`AceHook`和`AceConsole`。`Ace Console`将用于聊天命令，也包含 Ace 的`Print`功能。

从[站点](https://www.wowace.com/projects/ace3)下载 Ace，并将文件解压到某个地方。在你的 addon 文件夹中创建一个名为`libs`的新文件夹。将文件夹`LibStub`、`AceAddon-3.0`、`AceConsole-3.0`、`AceHook-3.0`复制到`libs`中。接下来在插件的根文件夹中创建一个名为`embeds.xml`的新文件，并添加下面的

```
<Ui xmlns="http://www.blizzard.com/wow/ui/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.blizzard.com/wow/ui/ ..\FrameXML\UI.xsd">
    <Script file="libs\LibStub\LibStub.lua"/>
    <Include file="libs\AceAddon-3.0\AceAddon-3.0.xml"/>
    <Include file="libs\AceConsole-3.0\AceConsole-3.0.xml"/>
    <Include file="libs\AceHook-3.0\AceHook-3.0.xml"/>
</Ui> 
```

现在我们将通过把`embeds.xml`添加到`toc`文件
中来告诉 WoW 这些库

```
## Interface: 80200
## Version: 1.0.0
## Title: My Addon 
## Notes: My First Addon
## Author: You

MyAddon.lua
embeds.xml 
```

现在嵌入了 Ace，我们可以对 lua 文件
进行访问

```
--the ace way of initializing addons, add the ace modules 
--you want to use as trailing parameters in :NewAddon()
MyAddon = LibStub("AceAddon-3.0"):NewAddon("MyAddon", "AceConsole-3.0", "AceHook-3.0")

function MyAddon:OnInitialize()             

    self:SecureHook("PickupContainerItem")

    local frame = CreateFrame("Frame")
    frame:RegisterEvent("MAIL_SHOW")
    frame:RegisterEvent("AUTOFOLLOW_BEGIN")

    frame:SetScript("OnEvent", function(this, event, ...)
        MyAddon[event](MyAddon, ...)
    end)

    self:Print("Hello from Ace Console")
end

function MyAddon:OnEnable()
    --initialize Saved Variables and other start up tasks
end

function MyAddon:PickupContainerItem(container, slot)    
    _, _, _, _, _, _, link = GetContainerItemInfo(container, slot)

    self:Print(link)
end

function MyAddon:MAIL_SHOW()
    print("Mail Shown")
end

function MyAddon:AUTOFOLLOW_BEGIN(...)
    local player = ...
    print("Following "..player)
end 
```

现在你的插件被 Ace 引导了。改变的是插件的初始化方式和新的`OnInitialize`和`OnEnable`方法。`OnInitialize`可以用来注册事件，创建钩子，以及其他你在启动时可能需要做的事情。

如果一切正常，你应该在登录时看到打印的`Hello from Ace Console`。打开邮箱，跟着随机的
玩家，那些应该也还能用。

## 控制台命令

要在你的插件中使用斜杠命令，你可以使用 [Ace 控制台](https://www.wowace.com/projects/ace3/pages/api/ace-console-3-0)
来注册命令和解析参数。

```
MyAddon = LibStub("AceAddon-3.0"):NewAddon("MyAddon", "AceConsole-3.0")

function MyAddon:OnInitialize()             

    self:RegisterChatCommand("myaddon", "SlashCommands")
end

-- /myaddon 1 2 3
function MyAddon:SlashCommands(args)

    local arg1, arg2, arg3 = self:GetArgs(args, 3)  

    self:Print(arg1, arg2, arg3)
end 
```

您可以选择手动处理参数或使用 [Ace Config](https://www.wowace.com/projects/ace3/pages/getting-started#title-4) ，这完全取决于您的用例。我会先从手动开始。

## 用户界面

抱歉，我没有为`Track Sales`写 UI，所以我不会报道它。你可以查看一下[扩展角色统计](https://gist.github.com/JoeGannon/288ac79a9edadd6b639c138f6320b865)的早期版本，了解一些基本概念，访问[诅咒](https://www.curseforge.com/wow/addons/extended-character-stats)了解最新消息。我建议你最后做这一块，并确保后端工作第一。无论在什么样的环境中，一起开发 UI 和后端已经够难的了。

## 测试

开发一个插件最困难的部分是测试它。可悲的是，没有魔兽世界沙盒或“插件开发岛”你可以去，并有无限的测试能力。一切都将不得不在游戏中手动完成。一个好处是你可以随时用`/run MyAddon:FunctionName()`调用你的插件的函数。有了这个，通常最好的办法是尽可能地隔离 WoW api 调用，并且将插件的大部分逻辑放在可以用`/run`轻松测试的函数中。你还必须到处散布`Print`声明。有几个[调试命令](https://wowwiki.fandom.com/wiki/Blizzard_DebugTools)可能也很有用。要使代码更改生效，运行`/reloadui`或`/rl`。

## 什么绊倒了我

对我来说，有几件事不是很明显，那就是如何调用其他文件中的函数，以及如何引用其他文件中的 Ace 模块。这是通过从 WoW 传入每个 lua 文件的变量来完成的。您可以通过将`local addonName, ns = ...`放在任何文件的顶部来访问它们。`addonName`将是一个包含你的插件名称的字符串，`ns`作为一个名称空间。这个[线程](https://www.wowinterface.com/forums/showthread.php?t=49349)详细说明了这一点。

要查看添加两个新文件`MyAddonModule.lua`和`MyAddonHelper.lua`的示例，不要忘记将它们添加到`toc`文件中！

```
-- MyAddon.lua

MyAddon = LibStub("AceAddon-3.0"):NewAddon("MyAddon")

function MyAddon:Test()             

    MyAddonModule:SayHello()
end

-- MyAddonModule.lua

MyAddonModule = MyAddon:NewModule("Module", "AceConsole-3.0")

local addonName, ns = ...

function MyAddonModule:SayHello()               

    self:Print("Hello from Module")
    ns:SayHelloHelper()
end

-- MyAddonHelper.lua

local addonName, ns = ...

function ns:SayHelloHelper()                

    print("Hello from Helper")
end 
```

用`/run MyAddon:Test()`测试

有了这些关键概念，你就可以构建任何插件。棘手的部分是弄清楚你需要哪些[挂钩](http://wowprogramming.com/docs/api_categories.html)和
[事件](https://wowwiki.fandom.com/wiki/Event_API)。会成为你最好的朋友，熟悉命名惯例也会有所帮助。如果你有问题，总会有像 WoW addon reddit 这样的论坛。

## 进一步阅读

*   [编码提示](https://wowace.gamepedia.com/Coding_Tips)
*   [钩子 Api](http://wowprogramming.com/docs/api_categories.html)
*   [事件 Api](https://wow.gamepedia.com/Events)
*   [经典具体信息](https://wow.gamepedia.com/Porting_older_addons_to_Classic) *
*   [王牌](https://www.wowace.com/projects/ace3/pages/getting-started)