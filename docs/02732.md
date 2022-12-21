# 在 Salesforce 中使用 Python 和 Selenium 禁用闪电体验

> 原文：<https://dev.to/katiekodes/disabling-lightning-experience-w-python-and-selenium-14m9>

你看到我懒得点击[无权限 Salesforce 简档(带 Python 和 Selenium)](https://dev.to/katiekodes/no-permissions-salesforce-profile-w-python-and-selenium-2fc7)中的按钮:看我再做一次，在我所有的自定义简档中关闭 Lightning 体验。

*(看着我因为简化了[Winter’20 发布功能而被 Salesforce 社区冷落吧，这个功能颠覆了](https://admin.salesforce.com/blog/2018/be-lightning-ready-by-the-winter-20-release)的闪电体验？)*

* * *

## 背景

在我工作的地方，我们将通过权限集，而不是通过个人资料，向我们的用户推出 Lightning 体验。

我们没有任何完整的个人资料，所以我们想确保他们在闪电体验进行时看不到任何关于闪电体验的东西。

(当我们的测试沙箱询问人们是否想在本周末升级后尝试 Lightning 体验时，我们的服务台已经接到了困惑的最终用户的电话。)

## 任务

我想循环查看我在`https://MyURL.com/00e`在 Salesforce 中找到的所有个人资料，检查是否有可编辑的**系统权限**名为“**闪电体验用户**”，并确保它未被选中。

在“设置”中，这位于给定简档的配置页面的“系统权限”区域中的“简档”下。

原来，每个概要文件的该页面的可编辑版本都可以在:

```
https://MyURL.com/00eSOMETHING/e?s=SystemPermissions 
```

…其中`00eSOMETHING`是个人资料*的 ID(可以在`https://MyURL.com/00e`带你去，够方便】*的个人资料链接中显示)。

* * *

## 代码

像以前一样，我将“抓取”Salesforce 网页，并“劫持”我自己的浏览器，以节省几次点击。

[![XKCD comic about the perils of trying to automate processes](img/0659b44a2218b0fad4ec52a71f7f35dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4uFpNKSD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/automation.png)

*   是的，当然这个[比手动点击花了我更多的时间来编码](https://xkcd.com/1319/)。
*   也就是说，现在我可以针对任何组织重用我的代码。
    *   当然，直到 Salesforce 更改了他们设置页面后面的 HTML 并破坏了我的所有代码。*(这就是截屏的危险。)*

请注意，这与“经典”用户界面中的设置相反 HTML 在 Lightning 体验中可能完全不同。

*(我不知道——我关机了！)*

### 第一步:登录

首先，我运行了下面的 Python 代码

```
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver import Chrome
browser = Chrome(executable_path='C:\\exampleprograms\\ChromeDriver\\chromedriver.exe')
browser.get('https://test.salesforce.com') 
```

这导致 Chrome 的一个特殊实例在我的电脑上弹出。

我照常使用我的用户名和密码登录 Salesforce。

### 第二步:获取我的对象链接

然后我注释掉上面的代码，没有让[我的 Python IDE](https://katiekodes.com/setup-python-windows-anaconda/#write-your-first-python-program) 清除`browser`变量*的值(这是我的 IDE 从一个“运行”按钮点击到下一个*的正常行为)，我运行了下面的代码:

```
editableLinksToVisit = []
browser.get('https://cs99.salesforce.com/00e')
profilerows = browser.find_elements_by_xpath("//table[contains(@class, 'x-grid3-row-table')]")
for profilerow in profilerows:
    profileLink = profilerow.find_element_by_css_selector("div[id$='ProfileName']").find_element_by_css_selector("a[href*='/00e']")
    profileBaseURL = profileLink.get_attribute('href')
    profileEditURL = profileBaseURL + '/e?s=SystemPermissions'
    editableLinksToVisit.append({'text':profileLink.text, 'editLink':profileEditURL}) 
```

上面的代码是这样做的:

1.  `browser.get()`命令让我访问了 Chrome 中的一个新网站——就好像我自己在我的浏览器栏中输入了那个 URL 并点击了“回车”
    *   这真的很让人神魂颠倒——你真的看着你的电脑在没有你的情况下做事情，就像有人接管了控制权一样。
2.  我告诉`browser.get()`的网址是“个人资料”屏幕的网址。
    *   请注意，如果您使用的配置文件列表视图没有显示所有的配置文件，或者至少没有显示所有的自定义配置文件，那么这个脚本就不会运行得很好。
3.  当我运行`browser.get()`时，存储在我的程序的`browser`变量中的内容发生了变化。
4.  然后，我使用各种内置于我已经存储在`browser` *(我忘了具体是什么)*中的[数据类型](https://en.wikipedia.org/wiki/Data_type)的[方法](https://en.wikipedia.org/wiki/Method_(computer_programming))，比如`.find_element_by_css_selector()`，来[抓取](https://en.wikipedia.org/wiki/Web_scraping)这个特定网页的内容。
    *   从技术上来说，我是在抓取 DOM，或者你使用 Firefox 或 Chrome 开发者控制台“inspect element”工具时看到的东西，而不是抓取 HTML 源代码。它们的内容非常细微的不同。过去，你可以通过查看 HTML 的源代码来判断它是由什么组成的。如今，JavaScript 可以在最后一刻将 HTML“注入”到“DOM”中，而您的浏览器*实际上是*基于 DOM 呈现网页，所以如果您想要解析网页背后的代码，您必须知道如何检查 DOM 中真正的内容。
5.  我的`for`循环在页面上寻找一个`<div>...</div>`标记集内的链接，该标记集的`id`属性以指向包含文本“`/00e`”的 URL 的“`ProfileName`”结尾
6.  然后我获取对象的名称*(根据链接的文本)*和它的 URL。
7.  最后，我通过在链接末尾添加一个`/e?s=SystemPermissions`来编辑链接——现在，当我访问我的链接时，我将直接进入可编辑的“系统权限”配置页面，而不是访问个人资料概述页面。

我的对象名称及其链接的大列表是我存储为`editableLinksToVisit`的内容。

### 第三步:点击大量按钮

我再次注释掉上面的代码，没有让我的 Python IDE 清除任何变量的值*(这是我的 IDE 从一个“运行”按钮点击到下一个按钮的正常行为)*，做了一个延伸并运行了下面的代码*(运行 30 个概要文件大约需要 **1-2 分钟)*** :

```
for linkDict in editableLinksToVisit:
    print()
    print(linkDict.get('text'))
    browser.get(linkDict.get('editLink'))

    try:
        submitbutton = browser.find_element_by_css_selector("input[type='submit'][value='Save'][id$='button_pc_save']")
    except NoSuchElementException:
        submitbutton = None

    try:
        checkedCheckbox = browser.find_element_by_css_selector("input:enabled:checked[type='checkbox'][title='LightningExperienceUser']")
    except NoSuchElementException:
        checkedCheckbox = None

    if submitbutton is not None and checkedCheckbox is not None:
        print('submit ' + str(submitbutton is not None))
        print('checkbox ' + str(checkedCheckbox is not None))
        checkedCheckbox.send_keys(" ")
        submitbutton.send_keys("\n")
        print('done')
    else:
        print('There is nothing to save.') 
```

这段代码是我保存在`editableLinksToVisit`中的所有描述-URL 链接对 *( [字典](https://www.w3schools.com/python/python_dictionaries.asp) )* 的循环。

下面是它对列表中每个链接的作用:

1.  它会写下一个我能读懂的纸条，告诉我它将要劫持我的浏览器来访问哪个对象的 URL
2.  它劫持了我的浏览器并访问了那个网址。
3.  它读取页面的 DOM 代码来找到 Save 按钮。
    *   如果没有，它会记录下来。
4.  它读取页面的 DOM 代码来查找标题为`LightningExperienceUser`的页面上当前选中和不选中的第一个复选框。
    *   如果没有，它会记录下来。
5.  如果它发现任何“可以使用取消检查”的东西，它就会这样做。
    *   我用键盘模仿在“跳”到复选框后按空格键，而不是 Selenium 的`.click()`命令，因为`.click()`工作不可靠，StackOverflow 上有人说这可能，他们是对的。🤷
6.  它点击保存按钮*(同样，我通过虚拟敲击键盘上的“enter”来模拟，而不是点击，因为出于某种原因，这样更可靠)*。

你可以看着你的浏览器在没有你的情况下“行驶”。挺好玩的。

还有许多`print()`注释，这样你就可以从你运行 Python 代码的软件的“标准输出”终端上观察到什么，并预测它在列表中进行了多远。

* * *

## 结论

所以…就这样吧。为懒惰的管理员设计的一个新的聚会诡计。

玩得开心点，不要忘了在您禁用所有自定义配置文件中的权限集后，将您*希望*能够看到 Lightning 体验的权限给那些人。

您可以通过以下方式实现:

1.  创建权限集
2.  进入*的*“系统权限”区域，点击“编辑”
3.  选中“Lightning 体验用户”复选框并点击“保存”
4.  将用户分配给相关的权限集