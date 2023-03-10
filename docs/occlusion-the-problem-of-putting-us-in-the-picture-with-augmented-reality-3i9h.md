# 遮挡:将我们放入增强现实画面的问题

> 原文：<https://dev.to/iflexion/occlusion-the-problem-of-putting-us-in-the-picture-with-augmented-reality-3i9h>

2016 年，Pokémon Go 的现象让增强现实(AR)有了第一个“杀手级应用”——这是虚拟现实(VR)徒劳地寻找了 25 年的东西。尽管围棋具有异想天开的性质，但这是公众第一次能够在现实世界中与计算机生成的元素实时互动，这些元素融入了现实生活的体验。
Pokémon Go 能够在普通移动设备的技术限制下实现这一点，而不是用 YouTube 视频演示来吸引消费者，这些令人印象深刻的壮举注定[仍然是蒸汽软件](https://www.theregister.co.uk/2016/12/09/magic_leap_neither_magic_nor_leaping/)——至少就实际的 RTM 产品而言。最终，pole place 去了实际出货的软件。
不幸的是，从商业角度来看，围棋的自由漫游特性为混合现实(MR)设定了如此雄心勃勃的目标，以至于这门艺术很快就遇到了难以逾越的障碍。

## **VR 和 AR 融合**

从历史上看，在过去的三十年里，虚拟现实从未能够摆脱投币式购物中心机器、专业的城市体验，或者一系列价格过高、束缚过多或功能不足的家庭设备。全视图、高分辨率交互环境的计算和图形需求是如此令人疲惫，以至于没有任何技术飞跃能够显著缩小他们经常需要的笨重耳机或锚定支持系统，如 PC。它也无法消除这样一种感觉，即这是一种孤立、昂贵、反社会的技术，与一个被轻便的可穿戴设备所迷惑、痴迷于数字社交互动的时代格格不入。增强现实似乎提供了一种更现代的方法。1990 年，波音公司的研究人员汤姆·考德尔(Tom Caudell)首次为一项拟议的工业应用定义了 AR，它用人工元素覆盖现实世界——这一概念可以追溯到二战，并于 20 世纪 80 年代末首次冲击汽车市场。
当前对增强现实的商业兴趣受到了可穿戴护目镜的发展的推动，这种护目镜比虚拟现实耳机更谨慎，可以以这种方式叠加交互式数字元素，而不是像虚拟现实那样用数字环境完全取代我们的视野。目前的市场产品包括微软的 Hololens、Magic Leap、谷歌眼镜企业版、Vuzix Blade、(苦苦挣扎的)Meta 2、Optinvent 的 ORA 2 和 Varia Vision 等。
现阶段，AR 和 VR 的区分正在学术化。由于 AR 可以完全模糊用户的视觉需求，并且 VR 系统越来越多地提供真实世界的视图，开发被一致认为是朝着[“混合现实”体验的方向发展](https://www.iflexion.com/blog/mixed-reality-examples)。
pokémon Go 现象激发了消费者对发生在非受控环境中的灵活 AR 体验的热情:街道、公园和开放空间。广告商想要令人眼花缭乱、惊叹不已的可发现的创意；游戏公司希望虚拟世界覆盖在公共世界之上。如果我们打算呆在家里，或者只在特殊活动或固定景点的指定空间中享受 AR，我们还不如回到 20 世纪 90 年代，看着最后一个虚拟现实泡沫在其技术和地理限制下缩小。所有这些意味着 AR 中的虚拟元素需要表现得像真实的物体一样——能够藏在桌子下面，隐藏在真实的结构后面，如柱子、门口、墙壁、汽车、灯柱、人；甚至摩天大楼，如果情况需要的话。在计算机视觉中，这被称为遮挡，这对于真正沉浸式增强现实系统的未来至关重要。然而，这可能会有点问题。

## **AR 映射的三种常用方法**

ar 系统需要了解它所处环境的几何形状:桌子、椅子、墙壁、壁龛和任何其他可能需要在 AR 会话期间出现在虚拟元素前面的对象。

[![Furniture mapped by the Microsoft HoloLens](img/6596aeaa7183d1b1878d17243064a318.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FcD27Bbk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.microsoft.com/en-us/windows/mixed-realiimg/surfacereconstruction.jpg) 

<figcaption>微软 HoloLens 绘制的家具</figcaption>

它还需要能够识别和提取可能与你一起在 AR 空间中的真人，因为他们可能最终站在虚拟物体或结构的前面。如果系统不能做到这一点，它就不能掩盖逻辑上不应该可见的虚拟元素的部分，例如站在你面前真实桌子后面的虚拟机器人的小腿。在真实参与者的情况下，它也不能把他们从他们可能站在前面的任何虚拟物体中“切掉”。
[https://www.youtube.com/embed/nRywAihJ2jM](https://www.youtube.com/embed/nRywAihJ2jM)

<figcaption>一个真实的人夹在两个非真实的元素之间:多彩的背景和前景物体。</figcaption>

如果系统可以提取这些“遮罩”，但不能足够快地完成或达到可接受的质量，要么虚拟元素将落后于真实元素，要么遮罩边界将模糊或不可信。
有三种主要方法，AR 系统通过这些方法构建不可见的 3D 环境模型，这些模型对于真实的遮挡是必要的。有些人比其他人需要更多的准备；有些比其他的更适合某些情况，没有一个是真正无拘无束和自发的混合现实体验的理想选择。

### 飞行时间

飞行时间传感器向环境快速发射红外光，并记录这些发射被反射回传感器的时间。较远的物体会比较近的物体更晚返回光线，使传感器能够建立房间空间的 3D 图像。微软 Kinect 传感器的第二版使用 TOF，自动驾驶汽车研究中常见的激光雷达系统也是如此。这些传感器也被广泛应用于工业应用中。
在室外，TOF 具有严重的局限性，因为自然光会扭曲其结果，并且多个 TOF 传感器(这将有助于避免 AR 绘图中的底切)容易干扰彼此的有效性。
一个适用于便携式 AR 设备(专用耳机或手机)的 TOF 传感器也有大约四米的最大可用范围，所以它肯定不能帮助我们在帝国大厦后面放一个虚拟哥斯拉。

### 立体摄像机

立体相机可以通过比较两幅图像之间的差异，从其固有的 2D 深度图中生成 3D 几何图形。这种技术广泛应用于 AR 应用和硬件中。从好的方面来看，如果有必要，这种方法可以实时工作，允许更多的自发和“实时”环境映射。
不利的是，它在光线不好的情况下工作不佳，无法解决底切(即区分远处墙上的凹室和门口)或其他几何异常，这些异常可能是初步映射会话已经识别的，并且在比实际相机输出低得多的分辨率下工作，通常会导致粗糙和不可信的遮罩。【T2[https://www.youtube.com/embed/JemHaOV_Zpo](https://www.youtube.com/embed/JemHaOV_Zpo)

<figcaption>In this video from Google's Project Tango, where live occlusion maps are generated from RGB-D data, we see the notable difference in quality between the video image and the available depth map.</figcaption>

更糟糕的是，当系统被要求绘制空白或无特色的区域时，这种方法可能会完全被破坏。
由于立体相机测绘系统的工作限制与人眼相同，它无法分辨 4 米范围以外的有意义的 3D 信息。所以，哥斯拉又一次不走运了。

### 结构光 3D 传感器

在这里，一个条纹状的红外光图案被投射到环境的真实世界形状上，通过计算线条扭曲的方式，从解释相机的角度来重建其轮廓。
[![no](img/3aa0f4c982427badd32eb4c8379b8342.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--SDljQuVL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://emeralddental.com/uploads/Medit%2520Blog.jpg)

<figcaption>By observing and comparing how straight lines are distorted when projected onto objects, SLM can compare the differences between the two viewpoints at its disposal and deduce the 3D geometry of the objects and the environment.</figcaption>

SLS 用于 iPhone X 的前置深度传感器(后置传感器使用立体摄像头)，是许多新兴 ar 技术和播放器的焦点，也是为苹果有影响力的 ARKit 考虑的首要硬件。明亮的环境光会破坏 SLS，因此对于绘制发生在户外的 AR 场景来说，SLS 不是一个合理的解决方案。像 TOF(见上文)一样，SLS 使用红外反射返回，在可行的 AR 场景中，不太可能将其传感器放置在比人眼更远的位置。这限制了这项技术的有效范围——你猜对了:4 米。

## **网络解决方案？**

即将到来的 5G 网络的低延迟和高数据吞吐量可能会对 AR 开发人员产生吸引力，他们在内心深处可能更喜欢在更强大的基站节点上解决这些问题，并将 AR 耳机变成一个通过网络连接的相对愚笨的播放设备。
但是，将这种反应灵敏的网络模型构建到核心增强现实框架中，似乎可能会将成熟的增强现实系统限制在城市环境中，那里 5G 连接广泛可用，价格合理。实际上，这是另一个潜在的地理锚，阻碍了即时 AR 体验的梦想。

## **AR 映射中的多余工作**

在当前任何流行的生成遮挡的 3D 模型的方法背后，都没有真正的智能或持久性。当一个 AR 系统在你的虚拟会议中“遮蔽”了另一个人，使他们看起来像是在一座 8 个月内不会开工的建筑前，那个人只是遮挡系统的另一个“像素包”。如果她走出视野又回到视野，系统必须重新开始对她进行分析。
[![no](img/4ae214ac325766ebdb511e721c2cdec3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WrSPuxI2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.researchgate.net/profile/Max_Mignotte/publication/283967510/figure/fig10/AS:296589928222723%401447723957352/Setup-and-pre-processing-steps-a-Original-depth-map-b-After-clipping-c-After-treadmill.png)

<figcaption>Source photo credit: ResearchGate</figcaption>

就静态对象而言，这种工作冗余甚至更糟:无论对象可能有多容易识别，AR 遮挡系统每次都必须从头开始创建遮挡网格。没有捷径，没有模板，并且分析的笨重和持续性质使得滞后或低质量的问题几乎不可避免。对于真实的增强现实体验来说，这两种情况都是致命的。

## **一个用于增强现实几何的“谷歌地图”？**

在公共环境中实现 AR 遮挡的最佳解决方案是下载已经存在的由科技巨头创建和索引的几何图形。但是，生成如此复杂和高分辨率的 3D“世界地图”是一项艰巨的任务，即使对苹果或谷歌这样的公司来说也是如此，因此它可能需要通过众包来实现。目前在这个方向上的努力要么局限于个别技术生态系统的围墙花园，如苹果的 ARKit 和 HoloLens 保存用户地图的能力——要么以无数初创公司为代表，这些公司显然希望如果 Polly(见下文)成为面向公众的 AR 领域的主要参与者，谷歌就会收购它们。其中包括 YouAR、Jido、Placenote 和 Sturfee 等。随着这样一个数据库的增长，它将变得更加精确，逐渐学会丢弃短暂的结构，如汽车、建筑设备、链条自行车和季节性圣诞装饰品。最终，它将学会表示该区域的几何图形和遮挡贴图的一致且持久的档案。
然而，首先，收集信息的分辨率必须能为大众所接受，速度不能阻碍志愿者的贡献，质量也不需要特殊的设备或复杂的方法。机器学习似乎可以提供答案。

## **用机器学习组装 AR 映射**

绘制汽车的复杂轮廓目前对最先进的 ar 来说是一个相当大的挑战。曲线是复杂的，表面是反光的，对象的某些部分是透明的。另一方面，机器学习已经[能够](https://towardsdatascience.com/machine-learning-for-vehicle-detection-fd0f968995cf)潜在地识别任何车型。识别出车辆后，ML 驱动的环境识别系统可以从 3D 汽车网格的公共数据库中下载简化的低分辨率几何图形，并将其准确定位在现实生活中识别出的汽车所在的位置，以几千字节的数据和几秒钟的场景设置为代价，实现车辆的遮挡，完全透明。
这样一个系统还可以利用 [Google Polly](https://poly.google.com/) 不断增长的 AR/VR 网格数量——这似乎最终会成为 AR 的 Google Maps，或者作为一项专注于 AR 的子服务并入地图。类似地，支持 ML 的 AR 扫描系统将能够对场景中的个人进行分类，并了解他们是什么(或许是谁)，如果他们从视野中消失然后再次出现的话。
此外，局部移动神经网络可以通过语义识别对象来解决四米扫描限制。他们甚至能够区分非永久性的物体(如停放的汽车和旁观者)、更可能需要遮挡的物体(灯柱)和远处的物体(如摩天大楼)。
有了对规模和深度的这种全面理解，目前在没有专业预映射的情况下 AR 无法实现，最终将有可能创建智能地使用遮挡并且没有尺寸限制的增强现实体验。

## **用神经网络生成遮挡的 3D 模型**

并非场景中的每个项目都有网络中可用的相应低多边形网格。有时，该装置需要以困难的方式重建几何形状，这是目前的标准做法。幸运的是，从 RGB-D 图像的深度图创建 3D 网格是计算机视觉研究的核心目标之一。在这个时期，一些公司正在谨慎地发布基于 ML 的系统，用于通过低影响的设备上神经网络生成几何图形。
其中一个是 Ubiquity6，它声称使用深度学习、3D 绘图和摄影测量的混合技术，以令人印象深刻的 30 秒的[速度重建一个观察到的环境。](https://www.wired.com/story/this-startup-makes-augmented-reality-socialand-ubiquitous/) [https://www.youtube.com/embed/hZrveuAURxo](https://www.youtube.com/embed/hZrveuAURxo)
然而，该公司并未公布其方法论的具体细节，似乎也在回避闭塞的话题。
从牛津主动视觉实验室分离出来，6D.ai 对遮挡的棘手问题不那么害羞:
[https://www.youtube.com/embed/q9PFFqAABBM](https://www.youtube.com/embed/q9PFFqAABBM)
像标题中的绝大多数开创性 AR 遮挡系统一样，物体隐藏的证据通常是“眨眼，你就会错过它”的变化。在这个 6d.ai Twitter 视频中，一个球落在一个国内表面上:
[【https://twitter.com/wintermoot/status/1020133483144810496】](https://twitter.com/wintermoot/status/1020133483144810496)
几何图形的简要一瞥(绿色，如下)，暗示了由低分辨率深度贴图层生成的标准多边形形状的粗糙边缘:
[![no](img/a7e0ac9dc1e86e1131ea35828a738db3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rmfRAVmv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sun9-1.userapi.com/c851036/v851036402/19a76e/H6lXNX3IEQ4.jpg)

<figcaption>Source photo credit: Twitter</figcaption>

在这个视频中，该公司展示了其系统的遮挡潜力:
[https://www.youtube.com/embed/NZwt75eii4Q](https://www.youtube.com/embed/NZwt75eii4Q)
遮挡贴图非常近似，即使在这个展柜中，甚至在对象是简单长方体的地方。
这可能表明了针对移动设备的增强现实系统中的一个固有问题，即很可能需要将最干净的多边形网格转换为相对像素化的 RGB 近似，这些近似具有扭曲遮挡的锯齿状边缘。尽管 6D.ai 声称其系统没有使用深度相机，但它可能会在实践中遇到这一瓶颈。没有实现的细节，很难说。
6d . ai 系统创建的网格实际上比遮挡所需的网格更复杂、资源更密集，有许多冗余的细节，例如沙发贴图中靠垫的缩进:
[![no](img/47c0bb2e4ebacbbad87c6b42a4e2ffad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aIUqFU2v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sun9-56.userapi.com/c851036/v851036402/19a776/rlqyy-ZdnhM.jpg)

<figcaption>Source photo credit: artillry.co</figcaption>

以这种方式生成的点(通常由 RGB-D 深度图生成)中只有一小部分需要用来创建有用的遮挡。然而，构建这样一个节俭且轻量级的网格可能需要更智能的方法来创建几何体，并且可能需要更深入和更具创造性的局部神经网络应用。
我们还可以从上面的视频(1:30)中注意到，实时绘制真实世界地图的“实时网格”从不试图绘制超过 4 米的任何东西，或者冒着将相机指向超过该距离的风险。这就是未来基于机器学习的 AR 映射系统似乎要伸出援手的地方，通过将 AR 系统从前面提到的三种有限的几何映射方法中解放出来。

## **机器学习解决方案走向移动**

2014 年，来自波恩大学的研究人员[提出了](https://web.archive.org/web/20180812015223/https:/www.ais.uni-bonn.de/papers/KI_2014_Hoeft_RGB-D_Semantic_Segmentation.pdf)一个由卷积神经网络(CNN)驱动的能够产生遮挡的“活”分割系统。该技术包括根据 RGB-D 图像的深度图数据对视频帧中的每个像素进行分类。
这是许多基于机器学习的提取和几何图形生成技术中的一种，最终可能[转移到移动设备上](https://pdfs.semanticscholar.org/d789/6d6be118386a1f76f389210ca4e3a87b0d4a.pdf)。所有这些方法都依赖于流行的便携式设备上的本地机器学习硬件和软件实现的发展。
幸运的是，在可能的情况下，ML 研究朝着优化和迁移到本地处理的方向发展，这是一场由物联网和大数据需求以及消费硬件制造商在其设备中利用机器学习的热情推动的运动。精简的机器学习框架和工作流正在被主要制造商中途满足，这些制造商将面向人工智能的硬件纳入他们的产品线。

## **有用的限制**

解决遮挡仍然留下一些其他问题需要清理，如有效的手的存在，匹配的照明，阴影和重影重叠图像。然而，没有什么比将虚拟世界融入我们的世界的潜力更影响 AR 的“现实”了。当前资源如此匮乏，利润如此关键，这可能有利于增强现实制图系统的最终发展，因为这些条件通常会刺激发明。问题是，这些限制是否会及时产生巧妙的突破，以抵御令人恐惧的寒冬。
笨重的虚拟现实模型在三十多年里的失败，加上(迄今为止)Pokémon Go 的独特现象，表明 AR 必须瞄准比有线家庭视频游戏更高的目标，或大多数消费者住得太远的特殊城市活动。它不能成为一种城市或专有技术，因为支持它的密集努力和基础设施需要与智能手机行业本身相同的规模经济。