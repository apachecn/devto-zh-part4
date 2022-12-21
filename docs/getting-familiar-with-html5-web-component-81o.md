# 熟悉 HTML5 WEB 组件

> 原文：<https://dev.to/seopriya/getting-familiar-with-html5-web-component-81o>

Web 组件是被 W3C 纳入 HTML5 DOM 规范的一个特性。

web 组件是一组 API，允许你创建新的定制的、可重用的、封装的 HTML 标签，以便在 Web 应用中使用。定制组件和小部件建立在 web 组件标准的基础上，可以跨现代浏览器工作，并且可以与任何支持 HTML 的 JavaScript 库或框架一起使用。

web 组件基于现有的 Web 标准。支持 web 组件的特性目前正被添加到 HTML 和 DOM 规范中，让 web 开发人员可以轻松地用封装样式和自定义行为 的新元素扩展 HTML。

**Web 组件构建块:**

1。自定义元素:

自定义元素 API 为开发者提供了创建具有自己行为和 CSS 样式的全功能独立 DOM 元素的能力。下面就是一个这样的例子。在这里我们创建了一个显示电影及其描述的元素

1.  〔t0〕〔T6〕！doctype htmlT2、>T3、t5
2.  **< html** 郎=【en】**>**
3.  **<头>**T3】
4.  **<meta**charset=【UTF-8】**>**
5.  **<meta**name=【视口】 内容=【width = device-width，initial-scale = 1.0】**>**
6.  **<meta**http-equip=【X-UA 兼容】 内容=【ie = edge】**>**
7.  **<标题>** 模板演示**</标题>**
8.  **</头>**
9.  **<正文>**T3】
10.  你好世界

12.  **<模板**id=【tmpTopMovies】**>**
13.  **< h3 >** 电影名称**</H3>**
14.  **<p>**desc**T10】/p>**
15.  **</模板>**

17.  <div【id】=【tmpplaceholder】></div

19.  **<剧本>**

21.  让 电影=
22.  {姓名:《魔戒》，详细:“魔戒的一些信息”}，
23.  {姓名:“星球大战”，详细:“关于星球大战的一些信息”}，
24.  】；

26.  让 t = 文档 。query selector(" # tmpTopMovies ")；
27.  让 pholder = 文档 。query selector(" # tmpPlaceholder ")；

29.  为(电影中的我)
30.  {
31.  t . content . query selector(' H3 ')。innerText = 电影 【我】。姓名；
32.  t . content . query selector(' p ')。innerText = 电影 【我】。细节；
33.  让 克隆 = 文档 。importNode(t.content，true)；
34.  pholder.appendChild(克隆)；
35.  }
36.  **</剧本>**
37.  **</正文>**
38.  **< /html >**

在上面的例子中，有一个包含电影名称和电影描述的模板。它是由 JavaScript 处理的。在 JS 中，有一个包含名称和细节的数组。并将这些数组元素添加到模板中。

2.  暗影 DOM。

影子树是根为影子根的节点树。影子 DOM 提供了样式和标记封装。要创建阴影 DOM，请选择一个元素并调用它的 createShadowRoot 方法，该方法返回一个片段，您可以将内容追加到该片段中。返回的这个片段被称为影子根。阴影根及其子元素对用户是不可见的，但是浏览器在遇到我们的标签时会呈现它们。它允许隔离组件和作用域的 DOM，并简化 CSS。

1.  **<风格>**T3】
2.  /*文档样式不适用于#elem (1) */ 内的阴影树
3.  p {颜色:红色；}
4.  **</风格>**

6.  **<div**id=【elem】**></div>**

8.  **<剧本>**
9.  elem . attach shadow({ mode:' open ' })；
10.  //影树有自己的风格(2)
11.  elem . shadow root . intrahtml=
12.  **<风格>**p { font-weight:bold；}**</风格>**
13.  **< p >** 喂，约翰！**</p>**T8
14.  `；

16.  //**<p>**仅从影子树内部查询可见(3)
17.  alert(document . query select all(' p ')。长度)；// 0
18.  alert(elem . shadow root . query selectorall(' p ')。长度)；// 1
19.  **</剧本>**

描述:

影子 DOM 是一种创建组件本地 DOM 的方法。

shadow root = elem . attach shadow({ mode:open | closed })–为 elem 创建阴影 DOM。如果 mode="open "，那么它可以作为 elem.shadowRoot 属性访问。

我们可以使用 innerHTML 或其他 DOM 方法来填充 shadowRoot。

3.  HTML 模板:

它定义了一个新的<模板>元素，它创建了大量的 HTML。模板允许声明标记片段，这些片段被浏览器解析为 HTML，在页面加载时不被解释，但可以在以后的阶段使用。这些模板可以被实例化、克隆和重用。一个<模板>标签中的所有东西都被浏览器认为是惰性的。

1.  **<模板>**
2.  **< p >** 你好。**</p>**T8
3.  **<p>**I m a 模板 **< /p >**
4.  **</模板>**

4.  HTML 导入:

使用 HTML 模板，你可以定义模板。但是如果 HTML 标记相当大，你的 HTML 文件看起来会很破旧。如果我们可以将模板保存在一个单独的文件中，这不是很好吗？HTML 导入是来拯救我们的。HTML 导入允许从不同的 HTML 文件导入模板。您可以将您的模板组织在不同的文件中，然后按如下方式导入:-

1.  **<链接**rel=【导入】href=【my document . html】**>**

举例:

下面是一个图像滑块 Web 组件的例子，它每隔 1500 毫秒改变图像并更新图像标题。

HTML:

1.  〔t0〕〔T6〕！doctype htmlT2、>T3、t5
2.  **< html** 郎=【en】**>**
3.  **<头>**T3】
4.  **<meta**charset=【UTF-8】**>**
5.  **<meta**name=【视口】 内容=【width = device-width，initial-scale = 1.0】**>**
6.  **<meta**http-equip=【X-UA 兼容】 内容=【ie = edge】**>**
7.  **<标题>** 模板演示**</标题>**
8.  **</头>**
9.  **<正文>**T3】
10.  图像幻灯片

12.  **<模板**id=“图像-滑块-模板” **>**
13.  **<div**class=【图像容器】 **>**
14.  **< h4** 类 = 【图像-标题】**></H4>**
15.  **</div>**T3】
16.  **</模板>**

18.  **<**
19.  资料影像=【https://plural sight . img IX . net/paths/path-icons/nodejs-6061628 d09 d . png，https://camo . githubuser content . com/EB 464 a60a 4a 4 a47 F8 b600 aa 71 bfbc 6 af3fe 5c 5392/687470773 a2 F2 f7261
20.  **</图像滑块>**
21.  **</正文>**
22.  **< /html >**

JavaScript:

1.  <剧本>
2.  **类** ImageSlider **扩展** HTMLElement {
3.  构造函数(){
4.  //如果你定义了一个构造函数，总是先调用 super()，因为这是 CE 规范所要求的。
5.  **超级**()；
6.  }

8.  connectedCallback() {
9.  **调试器**；
10.  **const**shadowRoot =**this**。attachShadow({ mode: 【打开】})；
11.  **const**template = document . query selector(" # image-slider-template ")；
12.  **常量** 实例= template . content . clone node(**真**)；
13.  shadowRoot.appendChild(实例)；

15.  **本** 。images = **本** 。getAttribute( 【数据-图像】 )。拆分( ，)；

17.  let imageContainer = **本**. shadowroot . query selector()。image-container ")；
18.  image container . style . height =【200px】；
19.  image container . style . width =【200px】；
20.  image container . style . background size =【包含】
21.  image container . style . background image =

23.  ”网址( + **本** 。图像【0】++)；

25.  **【这】** 。暗影根.查询选择器( 。图像标题“ 。intrahtml =【影像】+1；
26.  **本** 。toggle image()；

28.  }

30.  toggleImage() {

32.  设 current image = 1；
33.  让图像= **本** 。图像；
34.  容影根= **本** 。shadowRoot

36.  setInterval( **功能** () {

38.  **如果**(current image>images . length)
39.  {
40.  current image = 1；
41.  }

43.  **调试器**；

45.  shadow root . query selector()。image-container ")style . background image =

47.  ' URL(+images[current image-1]+')'；

49.  影根.查询选择器(T2】。图像标题“ 。intrahtml =

51.  【影像】+current Image；

53.  current image++；

55.  }，1500)；
56.  }
57.  }

59.  custom elements . define(【image-slider】，image slider)；
60.  </剧本>

现在我们知道了 web 组件利用了哪些规范，让我们来看看定制元素的生命周期。我知道，我知道，我们很快就会找到密码的！

一个组件的生命周期:

1.  **类** MyElement **扩展** HTMLElement {
2.  构造函数(){
3.  //总是先叫超()
4.  **超级**()；
5.  console.log( '构造！')；
6.  }

8.  connectedCallback() {
9.  console . log(‘已连接！’)；
10.  }

12.  disconnectedCallback() {  
13.  console . log(‘断开连接！’)；
14.  }

16.  属性变更回呼(name、oldVal、newVal) {
17.  console . log(` attribute:$ { name }已更改！`);
18.  }

20.  adoptedCallback() {
21.  console . log()‘通过！’)；
22.  }
23.  }

以下是组件生命周期的方法:

*   构造函数():

每当创建一个元素时，构造函数就运行，但是在元素被附加到文档之前。我们将使用构造函数来设置一些初始状态、事件监听器和创建影子 DOM。

*   connectedCallback():

当元素被插入到 DOM 时，connectedCallback 被调用。这是运行设置代码的好地方，比如获取数据，或者设置默认属性。

*   disconnectedCallback() :

每当从 DOM 中删除元素时，就会调用 disconnectedCallback。清理时间！我们可以使用 disconnectedCallback 删除任何事件侦听器或取消间隔。

*   attributeChangedCallback(name，oldValue，newValue) :

每当你的元素的观察属性改变时，attributeChangedCallback 被调用。我们可以通过实现静态 observedAttributes getter 来观察元素的属性。

*   adoptedCallback():

每次将自定义元素移动到新文档时，都会调用 adoptedCallback。只有当您的页面中有< iframe >元素时，您才会遇到这种用例。

 T3】