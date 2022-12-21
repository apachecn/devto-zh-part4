# 提高表单和 AJAX 安全性

> 原文：<https://dev.to/lawrencejohnson/improving-form-and-ajax-security-4g29>

这篇文章首先是关于适用于任何 Web 应用程序平台的表单安全性和基本考虑事项。第一部分将涵盖这些概念，第二部分将使用 PHP 来演示和解释应用。从中受益最大的开发人员需要在服务器端处理 HTML 表单方面拥有经验或知识。

**注意:**我想在一开始就说明，无论使用何种过滤方法(包括本文中的方法)，对于 SQL 数据库等外部资源，数据都应该进行转义。**输入过滤不能替代参数化或转义的 SQL 查询！**

# 表单安全的原则

在开发生涯的某个阶段，所需的技能需要从效率、性能、可用性、可读性和可重用性的基础发展到包括安全性在内的一系列技能。有些人可能认为这是入门技能的一部分，对于那些在编写第一行代码之前经历了 4 年大学生活的人来说，这可能是真的，但在大多数情况下，人们不仅会逐渐了解开发是如何工作的，还会逐渐了解 Web 是如何工作的，恶意用户是如何进行攻击的，以及 Web 应用程序扫描器(WAS)会根据什么类型的东西对应用程序进行评分。

在其他开发人员的代码中，我最常遇到的一点是缺乏对恶意用户开始攻击网站的最基本和标准方式的考虑。这些是脚本和机器人级别的方法，甚至可以使非常小的 web 应用程序成为目标，因为进入的门槛(工作量级别)非常低:HTTP 请求。

## HTTP 请求的性质

这一切都始于对 HTTP 协议的中等水平的理解。向 Web 服务器发送请求有多种方式；大多数请求使用 GET 命令，该命令最常用于请求对网站上的特定 URL 的响应，并且可能经常包括来自查询字符串的 URL 参数，以向它所请求的资源提供一些附加指令。Web 浏览器会自动为用户构建这个请求。示例:

```
GET / HTTP/1.1
Host: dev.to 
```

这可以通过在端口 80 上连接到 dev.to，在原始模式下手动使用 telnet 客户端或 PuTTY 之类的东西来实现。两个换行符向服务器表明请求已经完成，可以处理了(第一行之后的所有内容都是为了添加 HTTP 头)。

来自服务器的响应看起来像:

```
HTTP/1.1 301 Moved Permanently
Server: Varnish
Retry-After: 0
Location: https://dev.to/
Content-Length: 0
Accept-Ranges: bytes
Date: Sat, 17 Aug 2019 17:46:15 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-lax8646-LAX
X-Cache: HIT
X-Cache-Hits: 0
X-Timer: S1566063976.885350,VS0,VE0 
```

使用`GET`方法的表单或 AJAX 请求会自动将一个查询字符串填充到 HTTP 请求中的 URL 路径。`POST`方法将数据填充到 HTTP 头中，但最终的工作方式是一样的(除了一些这里没有提到的例外，比如提交文件)。

我简要解释这一点的原因是，重要的是要理解，发布到 Web 服务器的内容并不像不理解这一层协议的开发人员所看到的那样复杂。因此，当涉及到像表单数据这样的东西时，恶意用户可以做的不仅仅是在网页上提供的表单中输入有趣的值。可以在这里找到对该协议的更深入的研究:[HTTP 基础知识介绍](https://www.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Basics.html)。

## 客户端验证功效

即使是基本的 Web 开发人员也很可能理解，任何适当的客户端验证都不能为表单处理程序提供安全性和数据完整性，对于那些不理解这一点的人来说，上一节的简要说明应该清楚地表明，客户端验证只对使 Web 应用程序更加用户友好有用(或者减少服务器负载，稍后将简要介绍)。

这意味着，无论何时从查询字符串或表单 post 数据中提取数据，开发人员都必须小心谨慎地验证这些数据。这包括普通用户看不见的 AJAX 请求。

## 服务器端处理用户生成的内容

这是问题的关键。从根本上说，开发人员在从`GET`(查询字符串)或`POST`(表单数据)中检索数据时应该始终考虑到这一点。以下是任何变量的一些初始考虑因素:

*   我检查过变量的缺失了吗？
*   我是否确认了该变量是否是必需的？
*   我是否确认了变量是正确的数据类型？
*   我是否确认了变量在最小或最大长度要求之内？
*   我是否确认恶意内容已被删除？

最后一点是我最喜欢的，我经常听到这样的话:

“我正在正确地对 SQL(或*空白*外部资源)参数进行转义，所以我不必担心这一点。”

这是一个被误导的假设。

这是被误导的原因有很多；第一个(也是最明显的)问题是，这只是假设需要保护数据库(或其他外部资源)。以下是一个更完整的列表，说明为什么在开始采集数据时应该过滤`GET`和`POST`参数:

*   进入我的数据库(或其他外部资源)的数据可能有另一个目的，例如在另一个区域检索和显示。未能过滤 HTML 标签、服务器端代码转义、HTTP 或电子邮件头等内容，最终可能会让恶意用户在以后完成攻击。
*   进入我的数据库的数据将来可能会以不同于现在的方式使用。在初始构建时，捕获的数据只存储在数据库中，但是可能以后添加的功能会以一种使数据不安全的方式使用数据。
*   我的代码捕获的数据将来可能会被另一个开发人员扩展，在这种情况下，不能保证开发人员会正确地对参数进行转义。在初始构建时，我的代码没有保存到数据库中，但是开发人员可能会在以后决定存储这些数据，而且不能保证开发人员会正确地避开这些数据。
*   我的代码可能会被另一个开发人员重用，作为将来新功能的复制/粘贴基础。这在与初级开发人员一起工作时很常见，这些开发人员可能不具备保护外部资源所需的理解水平。给低年级学生提供例子是让他们尽早步入正轨的好方法。
*   我在一个团队中工作，其中多个开发人员处理不同的任务，因此另一个开发人员处理我的代码捕获的数据的集成，可能会错误地认为它是安全的。
*   零日漏洞变得越来越常见，甚至在服务器端框架或 Web 服务器中，这些漏洞也会在许多不同的级别上弹出。

上述考虑的大部分应该是有意义的，但是最后一点是另一个大的假设，即代码的其余部分是安全的，不受任何数据的影响。当然，这可能是预期的工作方式，但是如果服务器端框架甚至 Web 服务器本身(apache、nginx、iis 等)中的漏洞被暴露，简单地在内存中处理捕获的数据*可能会*带来不可预见的风险。

**提醒:**不管之前做了什么过滤，开发者都应该避开外部资源的内容。**输入过滤不能替代参数化的 SQL 查询！**

## 关于 Web 应用防火墙(WAF)的说明

如果一个 Web 应用程序位于一个适当的 WAF 之后，可以假设大量的恶意内容永远不会使它成为真正的 Web 应用程序，所以我承认，在 100%确信 Web 应用程序将永远位于一个适当的 WAF 之后的情况下，过滤恶意内容变得不太可能是必要的努力。然而，开发人员在做出这个假设之前，应该确保他们了解 WAF 技术。例如，如果一个开发者认为一个合适的 WAF 是在需要保护的 Web 应用程序中运行的(例如:他们所在平台的插件)，那么这个开发者需要做更多的研究。就像传统的防火墙一样，让一个实体负责保护自己从来都不是理想的。

这就总结了处理表单数据的概念性本质，因为实际处理表单数据的应用方法可能会因平台而有很大差异。例如，一些框架甚至会在到达开发人员的代码之前预先验证`GET`和`POST`数据，其他框架可能会为安全/过滤检索提供方便的包装器或函数，还有一些框架可能需要完全定制的过滤机制。下一节将介绍用 PHP 处理这个问题的例子。

# 过滤 PHP 中的输入

我选择 PHP 作为示例，因为它涵盖了各种适用的过滤需求和方法:

*   需要:PHP 是应用最广泛的服务器端编程语言。来源:[当前](https://w3techs.com/technologies/overview/programming_language/all)，[历史](https://w3techs.com/technologies/history_overview/programming_language)
*   需要:检索`GET`和`POST`数据的标准方法不提供基本过滤或保护。
*   需要:一些基于 PHP 的 CMS 平台要么不提供，要么允许绕过平台提供的输入过滤机制。
*   方法:PHP 提供了带有大量过滤机制的`filter_input`函数，使得这个过程变得简单和一致，即使是普通的 PHP。

## 来源形式

对于所有即将出现的服务器端示例，我将假设数据源来自下面的 HTML 表单。这些例子的目的纯粹是集中在过滤服务器端的数据，所以任何与 UX、客户端验证等相关的东西。，被完全删除。

```
<form>
    <input type="text" name="name">
    <input type="text" name="email">
    <input type="text" name="age">
    <input type="text" name="postalcode">
    <select name="subject">
        <option value="general">General</option>
        <option value="other">Other</option>
    </select>
    <input type="submit" value="Submit">
</form> 
```

## 检索/捕获表单数据(繁体)

对于那些不熟悉 PHP 但仍然对后面的例子感兴趣的人，我已经快速回顾了检索在 HTTP 请求中发送给服务器的内容的传统方法。这是通过在任何开发人员代码执行之前访问 PHP 准备的`$_GET`和`$_POST`全局变量来完成的。使用数组语法查询这些变量。

对于表单或 AJAX 方法`GET` :

```
$name = $_GET['name'];
$email = $_GET['email';
$age = $_GET['age'];
$postalcode = $_GET['postalcode'];
$subject = $_GET['subject']; 
```

对于表单或 AJAX 方法' POST':

```
$name = $_POST['name'];
$email = $_POST['email';
$age = $_POST['age'];
$postalcode = $_POST['postalcode'];
$subject = $_POST['subject']; 
```

在这两种情况下，上面的代码都没有为数据的安全和实际使用做任何准备。没有空检查(不发送输入)，没有验证，也没有过滤恶意代码。这些代码甚至不需要对这些变量做任何事情，只需提交没有任何变量的表单，就可以抛出警告或异常，这可能会暴露表单的其他缺点，具体取决于 Web 应用程序的配置。

## 输入`filter_input`

简单回顾一下`filter_input`、【https://www.php.net/manual/en/function.filter-input.php】和的 PHP 手册条目就能学到很多东西。它是另一个有用的 PHP 函数`filter_var`的扩展。它至少接受两个参数，但是如果使用得当，它应该至少有三个参数。

该函数允许开发者指出变量被检索的位置(`INPUT_POST`、`INPUT_GET`、`INPUT_COOKIE`——这里没有涉及，但不用于会话或服务器，首先参见`$_SESSION`和`getenv()`)，以及他们想要检索值的变量名。最后一个属性允许他们指定自己的基本过滤机制。`INPUT_COOKIE`非常重要，而且经常被忽视，但我不会在这篇文章中讨论它(在大多数情况下，所有相同的规则都适用于适用于`GET`和`POST`的 cookies)。这里有一些基本的例子来开始使用我们的表单(如上)和`POST`方法。

```
$name = filter_input(INPUT_POST, 'name', FILTER_SANITIZE_STRING);
$email = filter_input(INPUT_POST, 'email', FILTER_VALIDATE_EMAIL);
$age = filter_input(INPUT_POST, 'age', FILTER_SANITIZE_NUMBER_INT);
$postalcode = filter_input(INPUT_POST, 'postalcode', FILTER_SANITIZE_STRING);
$subject = filter_input(INPUT_POST, 'subject', FILTER_SANITIZE_STRING); 
```

这些是非常基本的过滤机制；有很好的过滤器来处理像所需条件和复选框输入这样的事情，并自动将它们转储到一个数组中，但这有点超出了我在这里要讨论的范围，因为它们在应用中都有复杂性。

这些变量中的每一个都将返回三种不同情况中的一种:

*   NULL:如果输入没有发送到处理程序，则返回 NULL。
*   false:如果输入不符合提供的过滤器的要求，则返回 FALSE(无效)。
*   MIXED:如果值满足过滤器要求，它将返回过滤器提供的值。PHP 是一个松散类型的系统，但是，在某些情况下，需要确保所提供的类型就是所需要的类型。因此，在`FILTER_SANITIZE_NUMBER_INT`的情况下，将返回一个整数值，因此如果该值为`0`，我可以预计$var === 0 为真，但$var === false 为假——这对于测试 false 和 NULL 条件非常重要。

## 如何处理恶意数据

根据所使用的过滤器，恶意数据会得到不同的处理。在某种程度上，过滤的并不是真正的恶意数据，而是过滤器不想要的过滤内容。换句话说，`FILTER_SANITIZE_STRING`并不一定要过滤恶意代码，但它预期用户会在类似消息的内容中输入一个字符串。用于代码或 SQL 的特殊字符被去掉了，所以在这种情况下，结果不是值`FALSE`，而是提供的字符串减去任何危险字符。对于捕获像密码这样很可能包含特殊字符的东西来说，这是一个重要的考虑因素。

在类似于`FILTER_SANITIZE_NUMBER_INT`的情况下，恶意代码将返回`FALSE`，因为除了整数之外的任何值都是无效的。

## 处理过滤后的结果并进一步处理

过滤后如何处理数据实际上取决于开发人员，但是这里有一个关于如何正确处理数据的基本想法:

```
$name = filter_input(INPUT_POST, 'name', FILTER_SANITIZE_STRING);
$isValid = false;
$errMessage = '';
if ($name === null) {
    $errMessage = 'A value for Name was not found.';
} else if ($name === false) {
    $errMessage = 'An invalid value for Name was detected.';
} else {
    if (strlen($name) === 0) {
        $errMessage = 'Name is required.';
    } else {
        $isValid = true;
    }
} 
```

在上面的例子中，`FALSE`的情况不太可能发生。即使只找到恶意符号，它也会返回一个空字符串。

```
$email = filter_input(INPUT_POST, 'email', FILTER_VALIDATE_EMAIL);
$isValid = false;
$errMessage = '';
if ($email === null) {
    $errMessage = 'A value for Email was not found.';
} else if ($email === false) {
    $errMessage = 'Email is invalid.';
} else {
    $isValid = true;
} 
```

在上面的示例中，如果为`email`传递的值不是有效的电子邮件地址，则返回`FALSE`。

```
$age = filter_input(INPUT_POST, 'age', FILTER_SANITIZE_NUMBER_INT);
$isValid = false;
$errMessage = '';
if ($age === null) {
    $errMessage = 'A value for Age was not found.';
} else if ($age === false) {
    $errMessage = 'Age must be a number between 1 and 100.';
} else {
    if ($age > 0 && $age <= 100) {
        $isValid = true;
    } else {
        $errMessage = 'Age must be a number between 1 and 100.';
    }
} 
```

在最终的 else 中，已经确定`$age`是整数。剩下要做的就是确保它落在要求的范围内。

```
$postalcode = filter_input(INPUT_POST, 'postalcode', FILTER_SANITIZE_STRING);
$isValid = false;
$errMessage = '';
if ($postalcode === null) {
    $errMessage = 'A value for Postal Code was not found.';
} else if ($postalcode === false) {
    $errMessage = 'An invalid value for Postal Code was detected.';
} else {
    if (!preg_match('/^[0-9]{5,5}$/', $postalCode)) {
        $errMessage = 'Postal code must be a 5-digit value.';
    } else {
        $isValid = true;
    }
} 
```

在上面的例子中，使用`FILTER_SANITIZE_NUMBER_INT`似乎是个好主意，因为我只期待数字，但这不是真的，因为一些邮政编码可能以开头的`0`开始。

```
$subject = filter_input(INPUT_POST, 'subject', FILTER_SANITIZE_STRING);
$isValid = false;
$errMessage = '';
switch ($subject) {
    case 'general':
    case 'other':
        $isValid = true;
        break;
    default:
        $errMessage = 'An invalid value for Subject was detected.';
        break;
} 
```

在上面的例子中，我只给了表单两个主题选项。这意味着我知道如果它不是这两个选项中的一个，那么它就是错误的，这会使代码更少，因为检查`null`或`false`是为了处理表单处理程序的恶意使用。

## 关于`filter_input`的一些期末笔记

关于过滤系统有一些事情需要考虑。例如，在许多情况下，当处理字符串内容时，会自动从值的开头和结尾删除空白。一般来说，这是一件好事，但是如果代码期望有空格(或者如果它需要能够检测这些场景，也许是为了用户消息传递)，这也可能是一个问题。

所提供的一些基本过滤器可能会在确定某个值的问题类型时带来挑战。可能需要一些实际使用的经验来了解可能遇到的情况以及如何应对这些情况(参数`options`可以增加相当多的额外控制)。

`filter_input`的最后一个(第四个)参数接受标志的按位析取。这可以大大提高过滤器的能力，但需要一些重要的额外读数才能充分利用。

注意类型比较的使用，特别是对于`false`。在没有类型比较(`===`)的情况下，当使用没有类型比较的`==`操作符时，空字符串、空数组、字符串值`false`或`0`等值也将为真。

`NULL`仅在参数不存在时从`filter_input`返回。一个空的表单域将传递一个空字符串，不会触发`NULL`值，尽管在处理复选框时会遇到这种情况(使用 ajax 时，一个简单的方法是即使复选框没有被选中，也总是发送一个值)。撇开复选框不谈，这个`NULL`条件只是为了验证 HTML 表单包含该字段，并且恶意用户不会试图在没有该参数的情况下发布到表单。在大多数情况下，为`NULL`添加一个特定的条件并不是真正需要的，只要它停止使用那个变量。更快的方法是使用条件`$value === null || $value === false`返回一个“无效”响应，这是完全可以接受的。

我没有在这里介绍它，但是我也建议开发人员研究一下 cookie 过滤。Cookie 数据发布在 HTTP 头中，因此恶意用户可以在其中填入他们想要的任何内容。重要的是，无论何时检索 cookie 数据，在使用之前都要通过过滤系统。

# 关于 AJAX 的注意事项

我想快速指出，上面例子中使用的处理`POST`和`GET`数据的所有规则也适用于 AJAX 请求，包括不需要任何用户交互的 AJAX 方法。AJAX 请求的处理程序可以像表单处理程序一样被操作。

# 关于 UX 的笔记

dev.to 上有很多关于客户端验证的帖子，所以我唯一要考虑的是，如果服务器端验证完成了，它可以用于客户端验证。这对于实际回发到服务器的传统表单来说不太理想，但是对于 AJAX 实现来说，有许多方法可以让错误消息返回到客户端代码，以输出表单处理程序错误消息的结果。这种方法应该在服务器的性能和表单的流量/使用之间取得平衡，但是对于低流量到中等流量的实现，拥有一个广泛的客户端验证层将导致开发和测试中的更多工作(取决于方法)。如果全部在一个地方完成，确保验证的一致性也容易得多。

# 快速汇总/TL；博士；医生

开发人员应该始终认为表单处理程序是恶意用户容易进入的地方，永远不要仅仅依靠客户端脚本来保护应用程序的安全性或数据的完整性。

**感谢您阅读**