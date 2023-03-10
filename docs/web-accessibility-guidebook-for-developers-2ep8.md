# 开发者网页可访问性指南

> 原文：<https://dev.to/telerik/web-accessibility-guidebook-for-developers-2ep8>

## 无障碍介绍

在为 React 的本地 UI 组件套件 [KendoReact](https://www.telerik.com/kendo-react-ui/) 实现[可访问性合规性](https://www.telerik.com/kendo-react-ui/components/accessibility/)(第 508 节，WCAG 2.0 和 WAI-ARIA)的过程中，我们了解了许多关于基本和高级可访问性的主题。通过这篇文章，我们的目标是向不同水平的工程师同事介绍网页可访问性，并分享我们的实践知识和最佳实践。

根据 W3C 的定义，可访问性意味着网站、工具和技术的设计和开发是为了方便残障人士使用。更具体地说，人们可以:感知、理解、导航、与网络互动，并对网络做出贡献。

关于可访问性的一个很好的例子是你可以不用看就能使用你的网站。如果你不能用你的视力来阅读内容或用鼠标与之互动，你花了几个小时开发的所有功能和细节将如何工作？想象一下，您需要听一个描述 UI 的屏幕阅读器，并且导航可能不通过传统的鼠标或键盘输入。

### 为什么易访问性经常被忽视

虽然有很多原因可以解释为什么可访问性不是无处不在，尽管理想情况下应该是这样，但是有三个原因非常突出。

第一，对于自己不太理解的东西，很难迁就。大多数时候，我们缺乏的不是动机，而是关于残疾如何阻止人们与我们的网站互动的教育。这包括缺乏关于存在哪些残疾类型以及如何适应这些类型的知识。我们只是不知道问题的来龙去脉。

第二，让你的应用程序具有可访问性需要大量的工作。从理解标准的前提开始，你需要一路遵循这些标准，将所需的特性和功能设计到你的应用中。当然，接下来是测试你的方法是否产生了想要的结果——大部分测试只能手工完成。本文中描述的实践将使这一努力变得更加容易，但是我们仍然在谈论一项严肃的事业。

第三个是经济上的争论，不管正确与否，它主导了现代决策:在大多数情况下，少数客户(或用户)会受到残疾的影响，这是推迟在下一个版本中实现这些可访问性改进的理由。对于企业来说，更容易证明将精力集中在对大多数人有益的事情上，而不是为一小部分用户提供改进，这些改进可能让人感觉应用程序实际上没有前进。

### 为什么易访问性很重要

#### 伦理道德

残疾人每天都要面对很多挑战。如果他们是你的客户或用户，让他们与你的 web 应用程序进行交互是最基本的人类礼仪。

#### 市场

有数据显示，全球有 10 亿人患有某种形式的残疾，T2 有 20%的互联网用户患有某种形式的残疾。这仍然是少数，但它包含的人数比我们大多数人想象的要多得多。

#### 合法

随着这一领域立法的发展，您的企业越来越有可能被法律要求具有可访问性。我们将回到这个问题，因为下一节将集中讨论这个确切的主题。

#### 用户体验

可访问性指南旨在帮助人们更容易地访问和使用您的网站。作为一个副作用，它们中的大多数都提高了可用性，并直接惠及所有用户，包括那些没有残疾的用户。例如，可读文本不仅帮助视力不好的人，而且帮助所有用户。

#### 工程

许多关于可访问性的良好实践通常是良好的工程和设计原则。通常是写得不好的代码不可访问。对于我们这些努力掌握自己技术的人来说，可访问性只是做好工作的问题。

#### 名声

拥有一个比竞争对手更容易访问的网站是一种竞争优势。这也有助于建立对你的品牌的好感。

#### SEO

搜索引擎优化的良好实践和网页可访问性之间有一些重叠。例如，使用适当的描述性属性来编写语义 HTML，如标签、视频转录、图像标题，以及使用标题和标题标签，都可以提高网站的 SEO 和可访问性。

### 立法

全球当前的立法正朝着可访问性成为网络的强制性特征的方向发展。在美国，美国残疾人法案( [ADA](https://www.ada.gov/) )涵盖了无障碍。许多发达国家都有类似的法律，例如，英国有 2010 年的[平等法案。实际上，这些法律意味着法律要求公共部门组织和企业遵守](http://www.legislation.gov.uk/ukpga/2010/15/contents)[网页内容无障碍指南(WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/) 。

你要考虑的不仅仅是你的客户和用户。如果您的组织有 50 名或更多的员工，您需要确保您容纳任何残疾人。这意味着你的内部网络工具也必须是可访问的。

此外，如果你是为政府工作的承包商，除了上述内容之外，你还需要在工作中遵守康复法案第 508 节。根据法律，所有美国政府服务都需要遵守第 508 条。

这些法律不仅仅是善意的表示。越来越多的律师事务所根据无障碍立法采取法律行动。Progress 有一篇关于该主题的详细文章供进一步阅读，名为“[无障碍与法律](https://dev.to/telerik/accessibility-and-the-law-164c-temp-slug-7160342)”

## 残疾类型和无障碍最佳实践

有四大残疾类型- **听力**、**视力**、**运动**和**认知**残疾。每种类型都包括许多条件。它们在与 web 交互时会带来不同的挑战，并且需要不同的方法来解决这些挑战。让我们探讨一些针对每种不同类型残疾的最佳实践。你会注意到这些实践中的大部分不是关于我们使用的底层技术，而是关于我们如何设计我们的软件。这意味着参与开发过程的每个人都可以为更好的可访问性做出贡献。

#### 听觉(听觉)障碍

听力残疾从轻度听力损失到耳聋不等。帮助这些用户的最佳方式是避免仅依靠声音来传达关键信息。相反，可以并行添加另一种介质来提供支持。例如，如果您使用视频，请确保它支持全字幕字幕。如果你使用音频，提供一份抄本。字幕和文字稿要写满，不要漏掉关键台词。在后面的段落中，我们将列出可读性准则。它们强烈适用于字幕和抄本。除此之外，对于视频和音频，要确保背景噪音最小化，这样传达的信息就尽可能清晰可闻。

#### 视觉障碍——视力低下

适应弱视的主要方法是拥有一个可读的界面。UI 元素需要大而清晰。然而，文本更复杂，在后面的段落中，我们将列出可读性准则。它们旨在帮助视力低下的人。

对比度是另一个重要的方面。UI 中元素和颜色之间的高对比度将有助于视力低下的人。有一些工具可以用来检查对比度对于患有这种疾病的人来说是否足够。在这里你可以找到网页可及性倡议(WAI) 推荐的[工具。在现在使用的大多数页面设计中，对比度确实是有问题的。下面是一个符合 WCAG 的高对比度主题的例子。如此高的对比度对于常规的主题来说效果不好，你可能不想牺牲网站的视觉吸引力。一个好的折衷办法是在你的网站上包含一个高对比度主题作为选项，就像改变语言的选项一样。](https://www.w3.org/WAI/ER/tools/)

[![Web accessibility - Contrast](img/6a31de7c7de134374dd99c15391d2244.png "Web accessibility - Contrast")](https://res.cloudinary.com/practicaldev/image/fetch/s--jhgSJtIJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/contrast.png%3Fsfvrsn%3D35e2e94e_1)

#### 视觉障碍——失明

盲人使用屏幕阅读器。这些应用程序解析 HTML 并使用自然语言将其描述给用户。针对屏幕阅读器的开发有其特殊性，因此本文后面的部分将专门讨论它们。此外，失明用户使用的输入设备也会有所不同。使用鼠标需要视力。盲人将需要完整的键盘支持。

#### 视觉障碍——色盲

色盲也不是一种单一的疾病——有不同类型的色盲。请记住，下面的解释非常简单。异常是感知绿光的困难，也是最常见的。对红光的感知有困难被称为原发性异常，这种情况不太常见。这两种情况的可见光谱有些相似，这种情况通常被称为红绿色盲。三色异常是一个蓝色感知的问题，非常罕见。

每种情况的严重程度也各不相同，从轻微的感知问题到完全不能感知颜色。当颜色感知受到部分影响时，我们使用前缀-nomaly，当颜色完全无法感知时，我们使用前缀 nopia。色盲是以灰度看到一切的情况，非常罕见。色觉的变化不影响单一颜色，而是影响整个可见光谱。

[![Types of colorblindness](img/901bed37c0263208f7a59e17f6b9d37e.png "How a color-blind person sees")](https://res.cloudinary.com/practicaldev/image/fetch/s--K9tFFx11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/how-a-color-blind-person-sees-the-world.png%3Fsfvrsn%3D25a31cd7_1)

你最初的想法可能是选择大多数色盲的人都能看到的颜色。这是不理想的，因为残疾的许多变化，但橙色和蓝色在大多数情况下工作正常。这也是互联网如此钟爱蓝色的原因之一。

有一些工具可以模拟色盲者看到你的网站时的样子。您可以使用它们来检测是否存在问题，然后为有问题的条件类型设计和添加可选主题。这仍然需要你的用户能够找到并切换到相应的主题。

你最有效的解决方案是不要仅仅依靠颜色来传达关键信息。你可以通过使用形状、符号、动画和其他创造性的手段来围绕这个问题进行设计。

#### 运动障碍

快速和/或重复的动作，需要按住按钮的动作，有时间限制的动作-所有这些对有运动障碍的人来说都很难，会导致身体疼痛。你需要避开他们，但事情没那么简单。下面的例子说明了原因:假设您有一个滑块，需要您按住按钮才能移动。您的解决方案可能是通过敲击一个键来允许滑块移动，但是如果步长太小，结果将是一个重复的动作，没有太大的改进。一般的规则是，你需要设计一个网站，让用户能够方便地使用键盘和鼠标。

#### 与晕动病和感觉超负荷有关的认知障碍(例如癫痫)

有几种模式会导致晕动病或感觉超负荷。通常这些都是快速的效果，如摇晃，明亮的灯光，快速闪烁(每秒三次或更快)。重复的运动模式，无论是否快速，都会导致同样的问题。页面上重复但缓慢的移动的一个很好的例子是雪花飘落的动画，这是我们在寒假期间经常看到的。在页面内容中使用华丽的过渡进行急剧的改变也是有问题的；我们需要使用平滑过渡来代替。一个好的做法是避免有问题的效果，但是如果你想使用它们，作为一种妥协，允许用户禁用它们。

#### 认知障碍——学习困难

简单是关键。让你的场景简单，让你的界面简单，没有混乱。使用简单的语言，避免花哨的词语。总是用简洁的信息提供清晰的说明。信息量应该遵循金发女孩原则——太少是不够的，但是添加太多会让一些用户分心。避免给用户带来不必要压力的时间限制。

#### 认知障碍——阅读障碍

诵读困难是一种残疾，它使一些人阅读困难:诵读困难的人可能会混淆字母，或者看到它们旋转或拥挤在一起。在下面的段落中，我们将列出可读性的准则。他们强烈适用于解决阅读障碍的挑战。

[![Accessibility - Dyslexia](img/7b3a443e3cafe2bdbedd163b5d7f4485.png "Cognitive disabilities - Dyslexia ")](https://res.cloudinary.com/practicaldev/image/fetch/s--pyftypcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/dyslexia.png%3Fsfvrsn%3D914c6482_1)

#### 可读性提示

良好的可读性可以确保你的网站对许多残疾人来说是可访问的:可读的字幕和文字记录对有听力问题的人来说是有帮助的，一般来说可读的文本对视力低下或有阅读障碍的人来说是有帮助的。一个经验法则是在大字体中使用简单干净的无衬线字体。

空间很重要。例如，很长的行很难阅读，所以应用每行 70 个字符的限制。对于字幕，建议限制为 35 个字符。提供足够的空间让人物呼吸——1.5x 行距就可以了。关于空间的话题，所有大写字母的文本很难阅读，所以使用混合大小写。阅读速度也很重要，所以不要让文本自动前进，或者在有字幕的情况下，让它们在屏幕上停留至少每个单词 0.3 秒。

这个难题的一个关键部分是对比度。背景图像通常会遮住文字。好的字体在字母周围有边框以增强对比度，但最好是完全避免背景图像，并提供与文本形成良好对比的纯色背景。

IT 行业已经创造了令人惊叹的免费专业字体，并针对可读性进行了优化。你可以考虑其中的一些。开放性阅读障碍和[因特](https://rsms.me/inter/)就是很好的例子。

[![OpenDyslexic specialized font](img/596812b83fbd4476b490f768ee61cc05.png "OpenDyslexic specialized font - dyslexia")](https://res.cloudinary.com/practicaldev/image/fetch/s--pKKrMgxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/opendyslexic.png%3Fsfvrsn%3D83a9d45a_1)

## 辅助技术简介

辅助技术是一个行业术语，包括所有旨在帮助残疾人的软件和硬件。输入设备包括嘴棒、头棒、大轨迹球、专用键盘、语音识别软件。输出设备包括屏幕放大器、屏幕阅读器、盲文显示器、助听器、带有自然语言界面的软件等等。其中一些增强了现有技术，另一些提供了与计算机交互的另一种方式。

[![Assistive devices - keyboard](img/8d71f6ec013d9546eb672d73833d75ff.png "Assistive devices - keyboard")](https://res.cloudinary.com/practicaldev/image/fetch/s--JFgGLj-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/devices4e4730fb07714f0892b9b8b95e227d78.png%3Fsfvrsn%3D2022fd2d_1)

大多数辅助技术都是在操作系统层面上工作的，web 开发人员不需要做任何额外的工作就能使它们正常工作。然而，对于屏幕阅读器来说，事情要稍微复杂一些。本质上，屏幕阅读器所做的就是解析 HTML，然后用自然语言描述和解释它。语音描述质量直接取决于编码的质量。因此，自然地，屏幕阅读器是致力于使网站更易访问的 web 开发人员的主要关注点。在接下来的段落中，我们将会看到一些为屏幕阅读器优化 web 资源的最佳实践。

### 为屏幕阅读器优化

#### 编写语义 HTML

帮助屏幕阅读器正确工作的最佳实践是编写语义 HTML——也就是说，编写有效的 HTML，遵循最佳实践并根据预期目的使用元素。例如，如果某个东西的外观和行为像一个按钮，就把它做成一个按钮，而不是

. If it is a heading, use the tags and not some inline CSS.

html 元素语义的正式定义可以在[HTML 的生活标准](https://html.spec.whatwg.org/multipage/)中找到。

在现实生活中，这当然没那么简单。这将我们带到下一节。

#### 遵循规范

与任何基础技术一样，互联网有多个标准化机构。万维网联盟(W3C)是其中之一，[网络无障碍倡议(WAI)是其中的一部分。作为开发者，我们需要遵循由 WAI 开发的](https://www.w3.org/WAI/)[网页内容可访问性指南(WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/) ，这是网页可访问性的通用标准。

我们之前在讨论不同类型的残疾时讨论的设计实践在 WCAG 有更详细的描述。值得注意的是，WCAG 的生活水平正在积极提高。在撰写本文时，最新的版本是 2.1。

WAI 开发了[Web Accessibility Initiative-Accessible Rich Internet Applications Suite(WAI-ARIA)，](https://www.w3.org/WAI/standards-guidelines/aria/))这是编写代码的技术标准。作为开发人员，我们需要遵循这个规范来让屏幕阅读器正常工作。为简洁起见，在接下来的段落中，我将把 WCAG 和 WAI-ARIA 称为“spec”

#### 自动化测试

有多种扫描仪可以自动检查我们需要遵守的许多合规性规则。例如，大多数自动化软件可以轻松地扫描缺失的属性和元素，检查颜色对比或验证 HTML。一个好的做法是至少每季度对你的网站进行一次扫描。如果你真的很专注，你可以在你的 CI 和 CD 流程中包含这一步。以下是高质量扫描仪的列表，排名不分先后:

*   [谷歌灯塔](https://developers.google.com/web/tools/lighthouse/)
*   轴
*   [波](https://wave.webaim.org/)
*   [电源映射器](https://www.powermapper.com/)
*   [Dynomapper](https://dynomapper.com/)
*   [Monsido](https://monsido.com/features/web-accessibility)
*   [W3 列出的各种其他工具](https://www.w3.org/WAI/ER/tools/)

#### 手动测试

不幸的是，自动化只能占据全局的一小部分。如果你想获得有意义的结果，你必须手动测试你的站点。进行这种测试最实用的方法是闭上眼睛，只用键盘和屏幕阅读器在你浏览的网站上执行各种任务。

旁注:就个人而言，这是我发现易访问性测试有多难的地方。

[![Accessibility - Blind Testing](img/49f1d5cbc04e56d740ee05ba13d2893a.png "Accessibility Blind Testing")](https://res.cloudinary.com/practicaldev/image/fetch/s--XAbnGdZJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/blind-testing.png%3Fsfvrsn%3D3f056418_1)

#### 导航

闭上眼睛，你不能使用鼠标。黑暗中的键盘导航比视觉输入困难得多。一旦你不再看屏幕，你的很多解决方案可能不会像你希望的那样有效。你可能会发现一些你没有考虑到的情况。简而言之，提供好的、有效的键盘导航是非常困难的。

#### 听觉复杂度

市场提供了多种屏幕阅读器，它们通常很难理解。你可能很难理解你所听到的。原因是屏幕阅读器不只是使用文本到语音来阅读屏幕。他们的任务更困难:他们需要足够详细地描述 UI，这样你才能理解它的结构。只有在简单的场景中为屏幕阅读器提供简单的构造，才能很好地理解它们。所以你需要非常努力地简化你网站的信息架构。

#### 不一致

每个屏幕阅读器对规范都有自己微妙的解释，在每个浏览器上的表现也略有不同。您将会遇到许多灰色地带，在这些地方，仅仅遵循规范是不足以让所有的屏幕阅读器都提供有意义的输出的。在这些情况下，您的实现需要做出妥协，在大多数阅读器和浏览器的组合中都能正常工作。

[![NVDA JAWS ChromeVox readers](img/af986f42cb5d37dbbcd2d18052665b35.png "NVDA JAWS ChromeVox readers")](https://res.cloudinary.com/practicaldev/image/fetch/s--rtRqT1Li--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/nvda-jaws-chromevox.png%3Fsfvrsn%3Da62aa04c_1)

在我们的实践中，我们发现了一些适合测试目的的组合:

##### 颌

Jaws 是市场上最古老的屏幕阅读器之一。这意味着它是最受欢迎的工具之一。它有许多用户，所以您需要确保您的应用程序支持它。但是它的年龄也意味着 Jaws 需要支持许多遗留用例。因此，它通常不太符合规范，并且很难使用。在我们的实践中，我们发现 Jaws 与 IE 配合使用效果最好。

##### ChromeVox

ChromeVox 是最新的阅读器(在撰写本文时),因此最符合规范。它的年轻意味着它仍然不是很受欢迎。在 Chrome 上效果最好。

##### NVDA

[NVDA](https://www.nvaccess.org/) 是另一款符合规范的新阅读器。它非常受欢迎，在 Firefox 上效果最好。

#### 手工测试结论

当一个阅读器与一个浏览器配合良好时，这给你一些信心，它的用户将主要在那个浏览器上使用它，尽管没有规则和可能的情况很多。然而，鉴于我们通常使用有限的资源，一个好的做法是只关注上面流行的组合并经常测试，而不是覆盖所有可能的阅读器和浏览器的组合，但不要经常这样做。

为了用数据来支持我们的说法，这里有一个链接指向一个著名的[屏幕阅读器用户调查](https://webaim.org/projects/screenreadersurvey7/)，它揭示了用户对屏幕阅读器的采用情况。

### 最后是第三方测试

建议您与残疾人一起测试，或者从客户那里获得可访问性反馈。最佳实践是，只有在您完成了内部手工和自动化测试的功课之后，才这样做。我们有责任首先确保他们的用户体验没有被完全破坏。只有这样，你才能从用户那里得到有意义的反馈。

## 您组织中的最佳工作实践

### 教育

解决任何问题的第一步是首先意识到它。这就是为什么我们建议您投资对您的团队进行这方面的培训。不管我们做正确事情的动机是什么，除非我们知道需要做什么来使网站更容易访问，否则我们不会在这方面取得进展。

此外，可访问性不是一个人的责任——从事 web 应用的每个人，从工程师、设计师到管理人员，都需要考虑可访问性。教育和与工程师同事分享知识也是这篇文章背后的主要动机。

### 文档

正如前面已经讨论过的，可访问性不是一门精确的科学。你会经常发现自己处于灰色地带，看不到清晰的解决方案。在这些情况下，最好的做法是记录你的方法。在该文档中，您可以包括您当前实施背后的理由，并引用您选择遵循的 WCAG 规则。这些文档将帮助你的团队分享知识，提高网站的一致性，减少灰色区域的数量。如果你需要在法庭上为你的决定辩护，拥有文件可以帮助你辩护。

KendoReact 是一套叫做[剑道 UI](https://www.telerik.com/kendo-ui) 的 JavaScript UI 库中的一个。在进展中，我们在团队之间共享代码和知识，以确保当一个团队在某方面表现出色时，其他团队会很快达到相同的水平。当谈到可访问性时，文档是我们如何在团队间共享知识的一个非常重要的部分。

#### 可用性和可访问性不一样

可用性和可访问性有很多共同点。本文中讨论的大多数可访问性实践将使所有用户受益。但是可用性和可访问性并不相同。您可能已经在可用性上投入了大量的资金，但是这并不意味着您已经自动涵盖了可访问性。请注意，可访问性需要单独关注。

这是我们推荐的关于可用性的读物:

*   美国政府提供基于研究的[网页设计和可用性指南](https://webstandards.hhs.gov/guidelines)
*   杰夫·拉斯金的《人性化界面》被认为是这方面的基础作品
*   史蒂夫·克鲁格的《不要让我思考》是一本精彩的短篇小说

正如我们之前所讨论的，可访问性有多个灰色区域。有时，易访问性解决方案可能与可用性解决方案相矛盾。在这些情况下，最佳实践是不牺牲可用性，因为它通常面向大量用户。相反，我们需要创造性地解决这个问题。

#### 使用无障碍工具

网页可访问性很难。获得好结果的一个关键方法是使用可访问的工具。例如，如果你想要一个简单的博客或网站，WordPress 会为你考虑可访问性。我们在 KendoReact 组件库上的工作，旨在以同样的方式帮助您。我们的 UI 组件是从零开始设计和构建的，考虑到了可访问性，所以你不必做大部分繁重的工作。

### 推荐资源

下面，你可以找到我推荐的相关和权威的资源，供你进一步阅读

*   [Mozilla 对 WCAG 的解释](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Understanding_WCAG)
*   [a11y 项目](https://a11yproject.com/)-一个主要的社区驱动的中心，拥有资源和工具
*   [谷歌免费无障碍课程](https://eu.udacity.com/course/web-accessibility--ud891)
*   [韦建议的测试工具](https://www.w3.org/WAI/ER/tools/)
*   AccessU 是一个著名的关注可访问性的峰会
*   [web aim(web accessibility in mind)](https://webaim.org/)–一个提供无障碍服务(如培训、评估和认证)的组织

此外，Progress 有一份关于可访问性的白皮书，非常详细地探讨了这个领域，名为[Accessibility for Web Developers](https://www.telerik.com/campaigns/kendo-ui/wp-accessibility)，可以免费下载。

### 结论

这是我们关于 web 可访问性的文章的最后一篇，总结了 [KendoReact](https://www.telerik.com/kendo-react-ui/) 团队在为 React 开发 UI 组件库的可访问性时的经验。我们使用该资源的主要目的是帮助建立对该主题的认识。我们希望我们已经传达了可访问性是多么重要，并且给了你有用和实用的想法来有效地解决在建立一个可访问的网站中的一些挑战。请在下面的评论中告诉我们你对这个话题的体验。

愿无障碍的力量与你同在。