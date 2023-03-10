# JSP(JSTL)和 Javascript 的混乱菜单

> 原文：<https://dev.to/composite/jsp-jstl-javascript-4l2m>

这篇文章是为刚开始的 Java Web 开发人员写的。如果用其他语言进行 web 开发，这篇文章是劣质的，希望以 T0 子句关闭。

> 我想把 javascript 变量放在“T0”值里，怎么办？
> 将 vo 数组导入到 javascript 中，出现奇怪的字符，`<c:forEach>`迭代语句中不能引用 javascript 变量。我该怎么办？

阿西瓦尔...有点...拜托，要想成为 web 开发人员，首先要学习服务器和客户端的基本知识。我提问什么都不说，问这个问题的窝囊废为什么这么多，马上就去 OKKY 也能看得很清楚。
只要能忘记，就又上来了。太疯狂了。我来整理。
如果整理后不喜欢，就放弃开发人员吧。拜托。即使是为了其他开发者。

## 用`<c:set>`标签设置 JavaScript 变量

首先基本上问了这样的问题，才回来的答案是 100%**[不可能]**。
JSP(JSTL)是服务器端，JS 亲切地解释为客户端端的概率为 99%。
如果用`<c:set>`标签设置 JavaScript 变量的目的，从我的经验来看，应该相当于下面。

*   JSTL 处理的便利性(例如，用于`<c:if>`条件子句标记)
*   不想写 JavaScript

 **从问这个问题的人类来看，他们不学 JavaScript，也不打算学，或者是两者之一。是啊
..什么...如果只用 Java 组成网站该有多好。很舒服吧。因为只用一种语言受苦就行了。我会让这样认为的人打一拳。

> Java 是 Oracle 的注册商标。

你明白吗？Java 再开源，也不能随便骑 Java，在应用程序中拥有一切。例如，如果 chrome 想让 Java 像 JavaScript 一样使用，谷歌必须向 Oracle 支付巨额专利费。当然，Google 在 Android 上用 Java 的时候被 Oracle 打了后脑勺，现在换成 Kortlin，Java 无论如何都想踢掉。

现在。那么该怎么办呢？答案只有一个。如果开始写客户端团 JavaScript，服务器团 JSP(JSTL)的角色就已经完全结束了。希望在制定脚本的过程中忘记 JSP 和 Java，进行开发。

也就是说，用脚本写 if 语句，用脚本重复。我会按事例给你攻略。

### `if`

有这样的 JSP 代码吧。

```
<c:set var="isRight" value="false"/>

<script>
// condition에 따라 동적으로 서버단 조건 변경
if(condition == true) <c:set var="isRight" value="true"/>
</script>

<c:if test="${isRight}">
<p>내가 옳다.</p>
</c:if>
<c:if test="${not isRight}">
<p>내가 틀리다.</p>
</c:if> 
```

Enter fullscreen mode Exit fullscreen mode

也许新手们会因为这样挤而发痒。从结论来说...你们不会如愿的。JSP 渲染的源结果如下。

```
<script>
// condition에 따라 동적으로 서버단 조건 변경
if(condition == true)
</script>

<p>내가 옳다.</p> 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式渲染，脚本错误将突然出现。

> 未捕获的语法错误:意外的输入结束

那该怎么修改呢？

首先记住我说的话。开始 script 时，JSP 已经结束了。相反，可以取值，请取值。

```
<%-- 어쩌면 model로부터 가져올 수도 있을 테니 남겨 두겠다. --%>
<c:set var="isRight" value="false"/>
<script>
var text = document.getElementById('text');
// condition에 따라 동적으로 텍스트 변경
if(${isRight} && condition == true) {
  text.innerHTML = '내가 옳다.';
}
</script>

<p id="text">내가 틀리다.</p> 
```

Enter fullscreen mode Exit fullscreen mode

现在。这样，将服务器端变量“T0”和客户端端变量“T1”调整为 true 或 false，根据需要放到浏览器中。那么就会得到想要的结果。

### `for` `while`等反复语句

首先简单地列出就可以了。例如 T0 或 T1 的话，叫什么都不会有问题。

如果是 POJO 对象，必须立即调用才能输出被称为“T0”方法的字符串。所以如果想输出 POJO 对象，可以像往常一样调用 POJO 对象的“T1”方法。

添加到脚本中也能载入的只有一件事。

到此为止是只读的，从现在开始提到的就是“T0”变化“T1”。总而言之，就是不行。现在让我解释一下。

你认识阿贾克斯吗？如果你知道 ajax，你就可以不看这篇文章了。
但是如果不知道，希望在谷歌上打弹簧 ajax 学习。只有鱼子皮妮们试试，弹簧 jsp 等等。

首先，你可能已经知道了，Java 的“T0”接口基础上的所有类，以及 Java 中用方括号(“T1”)括起来的数组角色的抽象类“T2”，负责 Javascript 数组的“T3”函数，从一开始就是不能兼容。

而且，Java 严格讲究类型，JavaScript 则不然。
当然，也有因为这一点而厌恶 JavaScript 的资深开发者存在。
对于那种资深开发者来说，严格考虑类型，推荐一款热腾腾的[类型脚本](https://dev.to/t/typescript)，足以让人误以为 JavaScript 是可以以对象为导向使用的 Java。

不管怎么样，一般载入列表的时候会写“T0”，如果搜索的话，一般会进行表单传输，再次用“T1”标签执行迭代语句进行渲染。

但是，如果要在没有刷新的情况下调出部分列表，或者要实现动态以树形结构方式选择的选择栏等，最终只能考虑脚本打糕。
对。要打糕。讨厌也没办法。让我们接受吧。

如果想这样动态地修改 POJO 对象，

```
<script>

<c:forEach items="${voList}" var="vo">

  if("${vo.status}" == "R") {
    vo.statusText = "읽는 중"; // ???
  }

</c:forEach>

</script> 
```

Enter fullscreen mode Exit fullscreen mode

最好趁早放弃。首先，没有办法调用例程来处理编辑过的 POJO 列表。
而且，这种习惯绝对是不好的习惯。不如按语法输出。
如果 vo 想动态改变并重新传达？更不行。
刚才也说过，但是写脚本的瞬间，服务器端语法是百度。

如果想有效地管理这一点，请使用 JSON。如果写 Spring，就会带上杰克逊。使用方法我会用例子说明知道学习、用 JSON 字符串存储、加工这些方法。

准备物

*   控制器的两个方法(一个 View 渲染和一个 POST API)
*   查看 jsp 하나
*   JS 技能

```
 <script>

  var voList = (${voListByJson});

  for(var i = 0; i < voList.length; i++) {
    var vo = voList[i];
    vo.someProperty = '아오지';
    //...
  }

  // jQuery 예를 들면,
  jQuery.post('/path/to/edit', voList, function(){
    alert('편집 완료!');
  });

</script> 
```

Enter fullscreen mode Exit fullscreen mode

相信在一定程度上掌握 API 和脚本技术，就能充分制作。
如果不能，可以去[【生活编码】](https://www.opentutorials.org/course/3281)学习。

### 从JS 调用 JSTL(Java)函数

看到刚进来的热乎乎的问题，一边回答一边写。
当然不能在 Java 端调用 JavaScript 函数，反之也不能在脚本端调用 Java 函数。当然，脚本端也不能调用 JSTL 函数(`${fn:something()}`)。

```
<script>

var a = 'Hey Apple!';
var b = '${fn:substring(a, 4)}';

window.load = function(){
    document.getElementById('some').innerHTML = b;
};

</script>

<p id='some'></p> 
```

Enter fullscreen mode Exit fullscreen mode

只有这样做，别说渲染了，汤姆凯特控制台上的异常堆栈跟踪才会欢迎你。

解决办法可能很简单，也可能很复杂。
主要有两种情况，内部可以解决的和需要借用服务器一个。

首先，变形或装饰字符串等行为(如`substring``replace`等)的情况下，在数字中加入 3 位数逗号等功能在 Java 中也可以实现，在脚本中也可以实现。只是互相分为服务器和客户端这两个“T2”铁策“T3”，所以要各自实施。虽然会很麻烦，但请直接实施，如果没有的话，请进行 Stack Overflow 或英语不好的 naver 搜索。

如果不可避免地需要服务器端功能(例如调用 DB、利用处理文件的外部资源)，那么脚本端当然不可能。在这种情况下，就利用剧本中没有两个人的朋友 T0 吧。

准备物如下，准备的方法是各自留为作业。

*   `ajax`技术和请求、响应回调
*   服务器端`API`的实现

这些都是需要学习的东西。

现在累了。
现在，希望你不要在脚本中看到调用 Java 或设置值的问题。
希望如此。

我为了写这篇文章挤了一周的头发也累了。

..瞥了一眼**