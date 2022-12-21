# Vue 的数据列表动态组件的魔力

> 原文：<https://dev.to/drewtownchi/the-magic-of-vue-s-dynamic-components-for-lists-of-data-5j8>

在 [drewtown.dev](https://www.drewtown.dev) 上查看这篇文章和其他类似的文章

Vue 的动态组件是 Vue 最未被充分利用的超能力之一。它们允许开发人员通过变量或计算属性指定运行时要加载的子组件来减少表示逻辑和冗长。

```
<component :is="activeTabComponent" /> 
```

经常给出的例子是一个[选项卡组件](https://jsfiddle.net/chrisvfritz/Lp20op9o/)，它根据所选的活动选项卡动态引入正确的子组件。这是该模式的一个很好的用法，但是我想看看有些不同的东西。

当您有一个包含各种项目和数据类型的对象时，我希望使用浏览器。对于这些项目，您希望在列表中显示它们，显示格式良好的输出，可以是日期、项目数组、很长的文本，甚至只是一个名称。

让我们看看这个对象，看看我们正在处理哪种数据。

```
const person = {  
  firstName: "John",  
  lastName: "Doe",  
  birthdate: "1986-06-22T00:00:00Z",  
  anniversary: "2005-10-09T00:00:00Z",  
  activities: ["Skiing", "Hiking", "Cycling", "Drinking Beer"],  
  about: "John talking about himself. It goes on and on forever...[snip]",      
  metadata: {
    lastUpdateUTC: "2019-06-29T15:14:00Z",
    lastUpdatedBy: "admin"
  }
} 
```

这只是一个小例子，但是您可以想象一个场景，其中有 20、30 甚至 40 个字段，包含 5-10 种不同类型的数据。

## 使用 HTML 模板

你可以把所有你需要的东西放进 HTML 模板。很简单，很管用。

```
<li><span class="item-title">Birthday</span> {{formatDate(person.birthday)}}</li> 
```

不过，这也有一些缺点。

首先，如果您的数据集非常大，并且您的表示元素足够复杂，那么您在处理数据集时可能会遇到大量的 HTML。我们拥有 Vue 的力量，但我们却像使用一种愚蠢的模板语言一样有效地使用它。拥有大量的 HTML 会使组件很难查找，甚至更难更新。更改类名变成了查找和替换的练习，而更改实际的 HTML 结构则是噩梦的来源。

第二，现在 HTML 模板中必须有格式化逻辑。在`formatDate`方法周围散布，可能附带参数，不是好的做法。

对于“关于”字段，我们可能希望在“显示更多/更少”开关后隐藏超过一定长度的文本。这涉及到由子组件更好地处理该组件中的状态。

也许我们应该将所有这些逻辑转移到这些项目的子组件中。🤔

## 将所有的逻辑移动到子组件

既然我们已经确定我们对象的子对象包含足够复杂的逻辑，它可能需要自己的状态、格式或外部库，我们将把所有这些逻辑移到子组件，如`details-date`、`details-text`或`details-list`。

```
<li><span class="item-title">Birthday</span> <details-text :value="person.firstName + ' ' + person.lastName" /></li>
<li><span class="item-title">Birthday</span> <details-date :value="person.birthday" /></li>
<li><span class="item-title">About</span> <details-text-expander :value="person.about" /></li>
<li><span class="item-title">Activities</span> <details-list :value="person.activities" /></li> 
```

这样更好！至少现在我们没有关于每个条目的格式和显示属性泄露到父条目的细节。父节点不应该关心日期是如何实现的，也不应该关心真正长的文本字段是否扩展。这些是孩子的责任，应该被封装起来。

不过，我们可以做得更好。仍然存在重复 HTML 的问题。

## 引入动态成分

让我们将数据塑造成一种稍微新的格式

```
 // Make sure you import and register all of the components you specify here
  computed: {
    items() {
      return [
          { name: "Name", value: `${this.person.firstName}  ${this.person.lastName}`, component: "DetailsText" },
          { name: "Birthday", value: this.person.birthday, component: "DetailsDate" },
          { name: "About", value: this.person.about, component: "DetailsTextExpander" },
          { name: "Activities", value: this.person.activities, component: "DetailsList" },
      ];
    }
  } 
```

我们的计算属性将返回一个对象数组。每个对象将给出名称、要传递给子组件的值以及要使用哪个子组件。

不管列表中有多少项，我们完整的 HTML 现在看起来如下。

```
<li v-for="item in items" :key="item.name">
  <span class="item-title">{{item.name}}</span>
  <component :is="item.component" :value="item.value" />
</li> 
```

我们遍历 items 数组中的所有内容，设置标题，并使用对象中指定的组件创建动态子组件，同时传入指定的值。它很紧凑，使得更新表示变得很简单，并允许您将关于将要显示的内容的所有细节保存在一个易于理解的计算属性中。