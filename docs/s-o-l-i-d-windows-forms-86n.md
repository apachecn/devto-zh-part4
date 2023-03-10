# 南 O . L.I.D Windows 窗体

> 原文：<https://dev.to/nickproud/s-o-l-i-d-windows-forms-86n>

我们都知道 Windows 窗体已经过时了。好像*真的*老了。(尤其是在软件方面)。的[1.0 版附带的 Windows 窗体。NET framework 早在 2002 年 2 月 13 日](https://en.wikipedia.org/wiki/.NET_Framework_version_history)，远在我投身于编码生涯之前。

当我开始我的编程生涯时，Windows 窗体可能已经是一个经验丰富的 UI 框架，但是像许多开发人员一样。NET(甚至 VB6)，Windows 窗体编辑器是你玩了控制台后初尝的地方。

我一直以为他们会很快被抛弃。很快，我将主要使用 XAML 开发桌面应用程序的前端，甚至利用[electronic](https://electronjs.org/)的优势将 web 应用程序部署为本地桌面应用程序。

但是没有。我仍然发现自己面临着支持 Windows 窗体应用程序的需求，你知道吗？

那很好。

但是这是否意味着我们必须以一种 archiac 的方式来构造我们的代码呢？当然不是！

过去的十年见证了软件工程实践中一个奇妙的新运动的扩散，那就是*干净代码运动。*

像 Robert c .(Bob 叔叔)Martin 这样的干净代码先驱已经为易读、易管理和易维护的代码铺平了道路，在石头上刻下了所有开发人员都应该注意的新的神圣戒律:

#### s . o . l . I . d 原则

如今，大多数开发人员都提到了这些新原则，但是如果你需要一个可靠的定义[，可以去维基百科查看一下。](https://en.wikipedia.org/wiki/SOLID)书的形式有很多更容易消化的解释。我推荐罗伯特·c·马丁的经典之作 *[干净代码:敏捷软件工艺手册](https://www.amazon.co.uk/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_1?keywords=clean+code&qid=1563476866&s=gateway&smid=A3P5ROKL5A1OLE&sr=8-1)*

#### 是的，但是谁会关心 Windows 窗体中的 SOLID 呢？！

我喜欢认为我尽可能地坚持这些原则。这并不容易，但没有什么值得做的事情是在开始。然而，当涉及到为 UI 开发代码时，我不得不承认有时会有点懒。

我更像是一名“后端”开发人员。我喜欢制造引擎。当然，我希望所有东西看起来都很好，并且易于使用，但是我对前端开发不感兴趣。当我最近为我的 Windows 事件日志自动化工具 *AutoEvent、*开发前端时，这一点开始显现出来，它恰好使用 Windows 窗体。(是的，我仍然不时地使用 Windows 窗体。我更喜欢 web 前端，但有时 Windows 窗体就是你所需要的！)

我编写代码来管理主页呈现 UI 控件的方式，并在单击特定选项卡时显示/隐藏不同的控件。一个基本的开关。

当你在一个基本的应用程序中进行用户界面开发时，在小代码块上添加新的功能是很常见的，但是我发现自己写了一些草率的切换逻辑。以 below button 事件为例，它根据我是单击*'*Settings '还是' Triggers '选项卡来更改可见的 UI 元素。

```
 private void RibbonControlAdv1_SelectedTabItemChanged(object sender, SelectedTabChangedEventArgs e)
        {
            switch(ribbonControlAdv1.SelectedTab.Text)
            {
                case "Settings":
                    panelSettings.Visible = true;
                    panel1.Visible = false;
                    lblMailServerConfig.Visible = true;
                    lblProcessLog.Visible = true;
                    lblSMTPPassword.Visible = true;
                    lblSMTPPort.Visible = true;
                    lblSMTPUsername.Visible = true;
                    lblProcessLog.Visible = true;
                    txtProcessLog.Visible = true;
                    textSMTPPassword.Visible = true;
                    textSMTPPort.Visible = true;
                    textSMTPServerName.Visible = true;
                    textSMTPUsername.Visible = true;
                    break;
                case "Triggers":
                    panel1.Visible = true;
                    panelSettings.Visible = false;
                    lblMailServerConfig.Visible = false;
                    lblProcessLog.Visible = false;
                    lblSMTPPassword.Visible = false;
                    lblSMTPPort.Visible = false;
                    lblSMTPUsername.Visible = false;
                    lblProcessLog.Visible = false;
                    txtProcessLog.Visible = false;
                    textSMTPPassword.Visible = false;
                    textSMTPPort.Visible = false;
                    textSMTPServerName.Visible = false;
                    textSMTPUsername.Visible = false;
                    break;
                default:
                    break;
            }
        } 
```

根据你的观点，很明显这是肮脏的。或者你可能没什么可抱怨的。它工作正常吗？

嗯，是的，这是...功能强大，但不太容易维护。例如，事件开始时只有一个视图控件的管理，但是我一直在添加更多的控件。很快，我不得不管理一个新的标签页，它本身有越来越多的控件在被点击时显示。

此时，我甚至还没有完成需要添加的选项卡的数量。潜在的结果是一个难看的长 switch 语句。

但这如何打破固体？

#### S 代表“单一责任”

这段代码“做一件事吗？”我想是的，但不明显。事实上，它做了几件应该被封装的事情。它检查大量不同的潜在按钮点击，然后在同一事件中逐个设置控件可见性值。

难道不是看起来很多“东西”都被塞进了一个 UI 事件中吗？怎么才能清理干净呢？

#### 你好抽象！我多么爱你。

```
public abstract class MainFormLayout
    {
        protected frmMain FormInstance;

        public MainFormLayout(ref frmMain formInstance)
        {
            this.FormInstance = formInstance;
        }

        public abstract void Activate();
    } 
```

我创建了一个名为 *MainFormLayout* 的抽象基类。这个类将作为一组派生类的模板，这些派生类接受一个表单(在我们的例子中是我们想要打开控件的主表单),然后允许我们调用一个单独的 *Activate()* 方法，这个方法简单地打开所有正确的基于你点击的导航标签的 UI 控件。因此，如果你点击了*触发器*，而不是在按钮点击触发的事件中循环通过一组肮脏、笨拙的开关案例，它只是抓取了一个 *TriggersLayout* 类的实例，该类从我们的基类派生并负责控制渲染。

```
 public class TriggersLayout : MainFormLayout
    {

        public TriggersLayout(ref frmMain formInstance):base(ref formInstance)
        {

        }
        //here is our Activate method, overriden from base class.
        public override void Activate()
        {
            //We can make Activate() call some methods as needed.
            DisableSettingsPanel();
            EnableTriggerPanel();
        }

        private void EnableTriggerPanel()
        {
            ((Panel)(FormInstance.Controls["panel1"])).Visible = true;
            foreach(Control control in FormInstance.Controls["panel1"].Controls)
            {
                control.Visible = true;
            }
        }

        private void DisableSettingsPanel()
        {
            var settingsPanel = ((Panel)(FormInstance.Controls["panelSettings"]));
            if(settingsPanel != null) settingsPanel.Visible = false;
        }

    } 
```

这个类已经知道从请求的布局中选择哪些控件，并且通过一个方法调用，它对我们需要的控件执行必要的迭代，并使它们可见，但是我们如何获得这个类呢？

#### 只对分机开放！(感谢工厂)

我们使用*工厂模式创建一个静态类。一个工厂类做了一个更简单的逻辑在抽象顶层所做的事情。它会检查您请求的布局，然后将其发送回来。它的功能更多一点，因为你发送一个请求，它只是给你一个答案；在这种情况下，答案就是您需要的布局。不多不少。在这个例子中，我创建了一个 *MainFormUIController。*它可能被称为控制器，但它实际上仍然只是一个工厂类，因为它获取并激活所需的配置。* 

```
 public static class MainFormUIController
    {
        public static void SwitchLayout(frmMain form, string layout)
        {
            var thisLayout = GetLayout(ref form, layout);
            thisLayout.Activate();
        }

        private static MainFormLayout GetLayout(ref frmMain form, string layout)
        {
            switch(layout.ToLower())
            {
                case "triggers":
                    return new TriggersLayout(ref form);
                case "settings":
                    return new SettingsLayout(ref form);
            }

            return new UnknownLayout(ref form);
        }

    } 
```

这种方法论更有利于 SOLID 的开放/封闭原则，因为我们不必修改任何底层逻辑来添加更多布局。我们可以简单地创建一个新的派生布局类，并将其添加到切换列表中。是的，我们一开始就有一个切换列表，但是你能想象这个列表会有多长吗？？如果你不相信，看看事件的新代码，因为我们创建了工厂和布局类:

```
 private void RibbonControlAdv1_SelectedTabItemChanged(object sender, SelectedTabChangedEventArgs e)
        {
                MainFormUIController.SwitchLayout(this,  ribbonControlAdv1.SelectedTab.Text);
        } 
```

切换选项卡上控件的逻辑现在只需一行。一行。

此外，我们正在满足另一个坚实的范式:依赖倒置。

这个事件不再依赖于直接知道 UI 控件是如何被找到的，需要哪些控件，它们被安排在哪里等等。它有一个向下的依赖项，底层代码有来自最高抽象层的最小依赖项，以引用主表单和布局名称的形式。

我们甚至可以消除对空检查的依赖。例如，如果我们给工厂发送一个它不识别的布局怎么办？我们返回 null 吗？

请不要。不要返回 null。

相反，我返回一个设计用来捕捉错误并抛出异常的布局。

```
 public class UnknownLayout : MainFormLayout
    {
        public UnknownLayout(ref frmMain formInstance):base(ref formInstance)
        {

        }

        public override void Activate()
        {
            throw new Exception("Requested from layout was not recognised");
        }
    } 
```

当工厂试图调用 *Activate()* 时，会抛出一个自动异常，并在 Messagebox 中显示一条清晰的错误消息。我仍然不确定这种“零避免”的设计模式是否适合其中一个坚实的领域，所以如果有人能启发我，请随时联系。

所以你有它。Windows 窗体可能很老，但它和 Angular 或 Vue 等性感的 UI 框架一样值得可靠的设计实践。你可能不得不使用这个老平台，为什么不呢？它仍然允许企业快速构建极其强大的应用程序，从而节省时间和金钱。微软甚至证实，Windows 窗体不仅会被开源，还会被 T2 支持。净 5。

因此，在有人可悲地将 Windows 窗体弃之不用之前(这可能需要一段时间)，保持它们的稳固。