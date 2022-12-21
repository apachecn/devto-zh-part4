# 为 API 使用 Vue

> 原文：<https://dev.to/workingwebsites/using-vue-for-apis-k4j>

在某种程度上，大多数应用程序都使用数据，而且数据可能来自 API。在本教程中，我们将使用 Vue 获取数据并显示它。

# 这个例子

假设您需要显示北美国家的列表。该列表将显示国家、首都及其人口。

您找到了 API，其中包含名为 [REST Countries](https://restcountries.eu/) 的数据，它包含国家大小和人口——这正是您所需要的。

# 欢迎光临 API，我能为您点餐吗？

仔细想想，API 有点像免下车餐馆，你提出请求，然后得到响应。但是，您需要知道一些事情，例如:

**1。食物/数据在哪里？**

您需要餐馆的地址，就像您需要知道 API 的 URL 一样。

**2。菜单上有什么？**

你要“薯条”还是“炸薯条”？是的，它们是一样的，但是你需要使用正确的词/参数来得到你想要的。

**3。你得到了什么？**

当你拿到包裹里的食物/数据时，你需要知道里面是什么。包括番茄酱还是醋？同样，返回哪些字段和格式？

**4。你能提出特殊要求吗？**

你如何获得额外的餐巾纸/请求某些字段？

**阅读菜单**

在开始你的项目之前，最好对 API 有一个概念。看看菜单/文件，这会使工作更容易。

# 在 Vue 中访问 API

Vue 是一个 javascript 框架，所以它没有特定的方法来使用 API。Ajax 或者 Fetch 都可以。Axios 经常被推荐使用，因为它使用简单且有效，所以我们将使用它。

理论到此为止，让我们把手放在键盘上开始编码吧！

## 基本设置

为了简单起见，我们将在网页中构建应用程序。

为此，我们将使用两个文件，

1.  HTML 文件来显示数据和加载 Vue 文件。
2.  Vue 文件有代码。

**HTML 文件:**

```
<html lang="en">

<head>
    <!-- Load Vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <!-- Load script file.  'defer' loads after body is generated -->
    <script src="vue_lists.js" defer></script>

    <style>
        table,
        th,
        td {
            border: 1px solid #cccccc;
            border-collapse: collapse;
        }
    </style>
</head>

<body>
    <h1>North America</h1>
    <!-- Div where Vue runs -->
    <div id="app">
        <h2>Table</h2>
        <table>
            <tr>
                <th>Country</th>
                <th>Capital</th>
                <th>Population</th>
            </tr>
            <tr>
                <td></td>
                <td></td>
                <td></td>
            </tr>
        </table>
    </div>
</body>

</html>
```

vista . js 文件:

在这个文件中，我们使用“countries_list”作为包含数据的数组。暂时是空的。

```
var app = new Vue({
    el: '#app',
    data: {
        countries_list: [],
    },
})
```

## 加载 Axios

要在您的应用程序中使用 Axios，您需要加载它。我们保持简单，所以我们将使用 cdn，我们将把它添加到 HTML 头中。

```
<!-- Load Axios -->
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script> 
```

现在已经加载了，您可以在 Vue 中访问它。

## 获取数据

回到汽车餐厅的比喻。我们在车里(Axios)，准备出发。我们知道位置(url)，我们阅读菜单(文档)，所以我们知道要什么(url 字符串)。让我们下订单吧！

根据文档，请求字符串应该类似于:[https://restcountries.eu/rest/v2/region/americas](https://restcountries.eu/rest/v2/region/americas)

太好了！我们知道如何获得数据，现在让我们把它显示在屏幕上。数据应该在页面加载后立即显示，那么如何做到这一点呢？

## 把它挂在钩子上

Vue 有一系列的[生命周期挂钩](https://vuejs.org/v2/guide/instance.html)，它们在加载过程中的特定阶段触发。装载 Dom 时,**挂载的**钩子触发。那有效！我们将把 Axios 调用放在那里并进行设置，以便我们可以查看控制台日志中的原始数据。

```
mounted() {
        axios
            .get('https://restcountries.eu/rest/v2/region/americas')
            .then(response => (
                console.log(response)
                ))
    }
```

**细分:**

| 已安装(){...} | 当 dom 被加载时 |
| 阿克斯 | 告诉 Axios...
 |
| 。获取(....) | ...使用“get”转到此 url 并返回数据 |
| 。然后(...)
 | 一旦数据被返回...
 |
| response = >(
console . log(response)
) | ...将数据称为“响应”,并显示在控制台日志中。 |

## 把它放在一起

代码应该如下所示:

**HTML**

```
<html lang="en">

<head>
    <!-- Load Vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <!-- Load Axios -->
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

    <!-- Load script file.  'defer' loads after body is generated -->
    <script src="vue_lists.js" defer></script>

    <style>
        table,
        th,
        td {
            border: 1px solid #cccccc;
            border-collapse: collapse;
        }
    </style>
</head>

<body>
    <h1>North America</h1>
    <!-- Div where Vue runs -->
    <div id="app">
        <h2>Table</h2>
        <table>
            <tr>
                <th>Country</th>
                <th>Capital</th>
                <th>Population</th>
            </tr>
            <tr>
                <td></td>
                <td></td>
                <td></td>
            </tr>
        </table>
    </div>
</body>

</html>
```

vista . js 文件:

```
var app = new Vue({
    el: '#app',
    data: {
        countries_list: [],
    },

    mounted() {
        axios
            .get('https://restcountries.eu/rest/v2/region/americas')
            .then(response => (
                console.log(response)
                ))
    }

})
```

控制台日志中的数据如下所示:

```
{data: Array(57), status: 200, statusText: "", headers: {…}, config: {…}, …}
config: {adapter: ƒ, transformRequest: {…}, transformResponse: {…}, timeout: 0, xsrfCookieName: "XSRF-TOKEN", …}
data: (57) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
headers: {content-type: "application/json;charset=utf-8", cache-control: "public, max-age=86400"}
request: XMLHttpRequest {onreadystatechange: ƒ, readyState: 4, timeout: 0, withCredentials: false, upload: XMLHttpRequestUpload, …}
status: 200
statusText: ""
__proto__: Object
```

它拥有我们需要的一切，而且还多得多！

### 拿出你需要的东西

就像汽车餐厅一样，你需要打开包装才能拿到食物/数据。

我们在这里看到的是*完整的*响应、状态、标题和整个包。这就像在免下车窗口把袋子递给我们，我们不需要包装纸，只需要食物(数据)，所以我们需要打开它一点。您可以通过调整代码来返回响应。**数据**

```
.then(response => (
                console.log(response.data)
                ))
```

提示:因为*响应*返回一切，所以它是一个很好的调试工具。

控制台日志应该类似于:

```
(57) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
0: {name: "Anguilla", topLevelDomain: Array(1), alpha2Code: "AI", alpha3Code: "AIA", callingCodes: Array(1), …}
1: {name: "Antigua and Barbuda", topLevelDomain: Array(1), alpha2Code: "AG", alpha3Code: "ATG", callingCodes: Array(1), …}
2: {name: "Argentina", topLevelDomain: Array(1), alpha2Code: "AR", alpha3Code: "ARG", callingCodes: Array(1), …}
```

那更好！

### 让数据变得可行

下一步是将数据赋给一个我们可以在 HTML 文件中使用的变量。

```
.then(response => (
                this.countries_list = response.data
                ))
```

现在 countries_list 有了数据，我们将设置 HTML 页面来显示它。

## 显示数据

我们已经在 HTML 文件中设置了表格，我们只需要将数据放入其中。

目前，数据存储在一个名为“countries_list”的数组中。这里的技巧是循环遍历它并在表中显示它。这是通过使用' [v-for](https://vuejs.org/v2/guide/list.html) '来完成的，这是 Vue 的一种' for 循环'。您将希望数据库中的每条记录都重复的元素放入其中。

我们的例子看起来像:

```
<tr v-for="country in countries_list">
    <td>{{country.name}}</td>
    <td>{{country.capital}}</td>
    <td>{{country.population}}</td>
</tr>
```

**细分:**

|  |
| 为中的**每条记录创建一个**<tr>****'**country _ list**'，
每条记录将被称为' **country****** |
| 
<TD>{ { country . name } }</TD>
T7】TD>{ { country . capital } }</TD>
<TD>{ { country . population } }</TD> | 要在 Vue 中显示数据，可以使用双花括号。
因此，对于每条记录，创建<TD>T1】并包装数据(称为“**国家**”)
和字段(**名称**)、资本人口等。)中的**花括号** |
|  | 这一排的末尾。 |

HTML 页面应该看起来像这样:

```
<html lang="en">

<head>
    <!-- Load Vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <!-- Load Axios -->
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

    <!-- Load script file.  'defer' loads after body is generated -->
    <script src="vue_lists.js" defer></script>

    <style>
        table,
        th,
        td {
            border: 1px solid #cccccc;
            border-collapse: collapse;
        }
    </style>
</head>

<body>
    <h1>North America</h1>
    <!-- Div where Vue runs -->
    <div id="app">
        <h2>Table</h2>
        <table>
            <tr>
                <th>Country</th>
                <th>Capital</th>
                <th>Population</th>
            </tr>
            <tr v-for="country in countries_list">
                <td>{{country.name}}</td>
                <td>{{country.capital}}</td>
                <td>{{country.population}}</td>
            </tr>
        </table>
    </div>
</body>

</html>
```

该表应该如下所示:

| 国家 | 资本 | 人口 |
| 安圭拉 | 小巷 | Thirteen thousand four hundred and fifty-two |
| 安提瓜和巴布达 | 圣约翰学院 | Eighty-six thousand two hundred and ninety-five |
| 阿根廷 | 布宜诺斯艾利斯 | Forty-three million five hundred and ninety thousand four hundred |
| 阿鲁巴岛 | 奥兰耶斯塔德 | One hundred and seven thousand three hundred and ninety-four |
| 巴哈马 | 拿骚 | Three hundred and seventy-eight thousand and forty |
| 巴巴多斯 | 布里奇顿 | Two hundred and eighty-five thousand |

我们有进展了！

## 在元素
中使用 API(或者如何在< img 和其他标签中使用 Vue)

这个 API 有每个国家的国旗图像。这是一个很好的视觉提示，所以我们把它加在国家旁边。

虽然你在 Vue 中使用双花括号显示 API 数据，但是当你把它应用到一个 HTML 元素，比如一个图像标签时，它的工作方式是不同的。

在这里，您**使用 [v-bind](https://vuejs.org/v2/guide/syntax.html#Attributes) 将数据绑定到一个属性。**因此图像标签看起来会像这样:

```
<img v-bind:src="country.flag" alt="Flag" height="26" width="42">
```

让我们把它加到表中。

```
<tr v-for="country in countries_list">
  <td>
     <img v-bind:src="country.flag" alt="Flag" height="26" width="42"> 
      {{country.name}}
   </td>
   <td>{{country.capital}}</td>
   <td>{{country.population}}</td>
 </tr>
```

## 传递参数

使用这个 API，您可以向它传递一个参数，告诉它要返回哪些字段。我们只需要

*   国家
*   简易格式
*   大小
*   人口
*   旗

基于[文档](https://restcountries.eu/#filter-response)，URL 应该看起来像 https://restcountries.eu/rest/v2/region/americas?的
fields = name 资本；旗帜；人口

我们可以将 Axios 调用更改为:

```
mounted() {
        axios
            .get('https://restcountries.eu/rest/v2/region/americas?fields=name;capital;flag;population')
            .then(response => (
                this.countries_list = response.data,
                console.log(response.data)
                ))
    }
```

结果中没有任何变化，但是如果您在控制台中查看，您将只能看到您需要的数据。简单吧。

```
[{"flag":"https://restcountries.eu/data/aia.svg","name":"Anguilla","capital":"The Valley","population":13452},{"flag":"https://restcountries.eu/data/atg.svg","name":"Antigua and Barbuda","capital":"Saint John's","population":86295},{"flag":"https://restcountries.eu/data/arg.svg","name":"Argentina","capital":"Buenos Aires","population":43590400},{"flag":"https://restcountries.eu/data/abw.svg","name":"Aruba","capital":"Oranjestad","population":107394},
.....
```

在这种情况下传递参数很简单——一个“get”字符串。当然，一些 API 和应用程序更复杂，可能需要“后”变量等。Axios 在传递变量方面做得很好。更多信息请见:[https://github.com/axios/axios](https://github.com/axios/axios)

## 处理错误和缺失数据

如果有问题会怎样？数据不回，餐厅关门，然后呢？

你用接球。Catch 将捕获错误，这样您就可以优雅地处理它。

```
.catch(error =>(
                console.log(error)
                ));
```

通常你会让用户知道有问题。虽然您可以在 catch error 中设置一些代码，但在 HTML 文件中设置一个 **[v-if](https://vuejs.org/v2/api/#v-if)** 语句可能更容易。

在这种情况下，如果“countries_list”为空，那么让用户知道。这可以通过在 HTML 页面中添加另一行来完成，如果 countries_list.length 为 0，将显示该行。

```
<tr v-if="countries_list.length == 0">
    <td colspan="3">
        No records found.
    </td>
</tr>
```

**细分:**

|  |
| 创建一个 **< tr >**
**如果**数组国家 _ 列表的长度为零 |
| 
< td colspan="3" >
未找到记录。
< /td > | 只需要 1 个 td 来扩展应该在那里所有三列。我们还有 3 个标题列。 |
|  | 这一排的末尾。 |

**重要！**如果有列表，确保您的“错误行”在生成的行之外。它们是两码事！

您的表应该看起来像这样:

```
<table>
    <!-- Header -->
    <tr>
        <th>Country</th>
        <th>Capital</th>
        <th>Population</th>
    </tr>
    <!-- Country List -->
    <tr v-for="country in countries_list">
        <td>
            <img v-bind:src="country.flag" alt="Flag" height="26" width="42">
            {{country.name}}
        </td>
        <td>{{country.capital}}</td>
        <td>{{country.population}}</td>
    </tr>
    <!-- Error Row -->
    <tr v-if="countries_list.length == 0">
        <td colspan="3">
            No records found.
        </td>
    </tr>
</table>
```

## 最终产品

我们的 HTML 代码应该是这样的:

```
<html lang="en">

<head>
    <!-- Load Vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <!-- Load Axios -->
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

    <!-- Load script file.  'defer' loads after body is generated -->
    <script src="vue_lists.js" defer></script>

    <style>
        table,
        th,
        td {
            border: 1px solid #cccccc;
            border-collapse: collapse;
        }
    </style>
</head>

<body>
    <h1>North America</h1>
    <!-- Div where Vue runs -->
    <div id="app">
        <h2>Table</h2> 
        <table>
            <!-- Header -->
            <tr>
                <th>Country</th>
                <th>Capital</th>
                <th>Population</th>
            </tr>
            <!-- Country List -->
            <tr v-for="country in countries_list">
                <td>
                    <img v-bind:src="country.flag" alt="Flag" height="26" width="42">
                    {{country.name}}
                </td>
                <td>{{country.capital}}</td>
                <td>{{country.population}}</td>
            </tr>
            <!-- Error Row -->
            <tr v-if="countries_list.length == 0">
                <td colspan="3">
                    No records found.
                </td>
            </tr>
        </table>
    </div>
</body>

</html>
```

我们的 Vue 脚本应该是这样的:

```
var app = new Vue({
    el: '#app',
    data: {
        countries_list: [],
    },

    mounted() {
        axios
            .get('https://restcountries.eu/rest/v2/region/americas?fields=name;capital;flag;population')
            .then(response => (
                this.countries_list = response.data
                ))
            .catch(error =>(
                console.log(error)
                ));
    }

})
```

我们的列表应该是这样的:

| 国家 | 资本 | 人口 |
| ![Flag](img/be61bdaf6b2a7391b466ad2f93ab5b1e.png)安圭拉 | 小巷 | Thirteen thousand four hundred and fifty-two |
| ![Flag](img/215ca1d979668cae96631ddee8487aed.png)安提瓜和巴布达 | 圣约翰学院 | Eighty-six thousand two hundred and ninety-five |
| ![Flag](img/5c478f9ddc746b5541d735d1ef6d83f6.png)阿根廷 | 布宜诺斯艾利斯 | Forty-three million five hundred and ninety thousand four hundred |
| ![Flag](img/2d74af9f27db941ec5ac6f308eec5d46.png)阿鲁巴 | 奥兰耶斯塔德 | One hundred and seven thousand three hundred and ninety-four |
| ![Flag](img/b13dbe1fe2105b9ba7b0887837da70d6.png)巴哈马 | 拿骚 | Three hundred and seventy-eight thousand and forty |
| ![Flag](img/5f72a4933ed86f0112f07913806edd34.png)巴巴多斯 | 布里奇顿 | Two hundred and eighty-five thousand |
| ![Flag](img/9d8d36108eb112fb85ac45e03c20135c.png)伯利兹 | 贝尔莫潘 | Three hundred and seventy thousand three hundred |
| ![Flag](img/427cf3786dc8bd7f5975b3c2510c6c78.png)百慕大群岛 | 哈密尔顿 | Sixty-one thousand nine hundred and fifty-four |

## 综上

在 Vue 中使用 API 相当简单。确保您知道如何获取数据(驱车通过)，将它分配给一个数组变量，并在 HTML 中显示它。

## 资源

有关更多信息:

*   [本教程的 GitHub 代码](https://github.com/workingwebsites/vue-api)
*   [使用 Axios 消费 API](https://vuejs.org/v2/cookbook/using-axios-to-consume-apis.html)
*   [轴](https://github.com/axios/axios)
*   检视清单
*   [使用 Vue 显示列表](https://dev.to/workingwebsites/using-vue-to-display-lists-6dn)
*   在 WordPress 中使用 Vue