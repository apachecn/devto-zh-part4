# 我试过 Vue.js，你试过吗...？

> 原文：<https://dev.to/saiprasaddevare/i-tried-vue-js-have-you-4j6e>

## 快速背景

大家好，这是赛普拉萨德。我是一名全栈 web 开发人员，使用 Ruby on Rails、Python、AngularJS。

## 开始

我最近开始学习 Vue.js。我仍然是一个新手，但我认为解释我如何使用 Vue.js 了解 AngularJS 是有益的。

两周前，我遇到了一个关于分页的问题。我必须用普通的 Javascript 来解决这个问题(因为这个项目没有任何前端框架)。因此，通过普通的 Javascript，这个问题将得到解决，但我们尝试了一些新的东西。我们决定使用 Vue.js.
,因为用例只针对一个简单的组件(分页), Vue.js 非常适合。

我们使用了 Vue.js 独立模式(不需要任何 webpack 构建系统或 vue-cli)。后端框架是 Django。

从安古拉的背景来看。把 Vue.js 看做 AngularJS。(这对我帮助很大)。

## 视图生命周期

Vue.js 提供的最漂亮的东西就是命名约定。生命周期挂钩的命名约定更容易理解。

创建(初始化)

*   创建前
*   创造

Mounting (DOM Insertion)

*   安装前
*   安装好的

Updating (Diff & Re-render)

*   更新前
*   更新

毁灭(拆卸)

*   销毁前
*   破坏

生命周期钩子的详细信息请查看 [Vue.js 生命周期](https://alligator.io/vuejs/component-lifecycle/)。(帮了我大忙)

## 面临的问题

**1。插值**

正如我所说的，我们使用 Django 作为后端框架，我们在 Vue.js 中遇到了一些插值问题。Vue.js 使用与 Django 模板相同的语法来表示表达式。所以为了解决这个问题，我们使用分隔符作为 Vue.js 的一部分

[![Creating Delimiter](img/efbca8f66b0466a734c3b86f46647db4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q0XbPCL9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o7cy6qfyrq3swgmelc4p.png) 截图 1.1 创建分隔符

分隔符与 Ruby 语言相同(" #{} ")。).因此，要在 Vue.js 的 HTML 中打印值，请像这样使用

[![Using new Delimiter](img/02a3b108ed75d950bb037b1fa407554c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4oeRQajy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0sfve85bilg1wnve7efc.png) 截图 1.2 在 HTML 中使用分隔符

**2。数组和对象变化检测**

Vue.js 中对数组和对象的检测是有注意事项的，正如官方文档所述“由于现代 JavaScript 的限制(以及 Object.observe 的废弃)，Vue 无法检测属性的添加或删除。”

[https://vuejs . org/v2/guide/reactivity/# Change-Detection-declarations](https://vuejs.org/v2/guide/reactivity/#Change-Detection-Caveats)

因此，要使对象具有反应性，我们应该事先声明它。如在**截图 1.1 创建分隔符**中，可以看到前面声明了“pendingUsersInvitation”、“pageNo”、“pageCount”、“recordCount”。因此，这些对象是反应性的。
所以改变对象就是更新用户界面。你可以看到下面的演示例子

[![sample_example](img/2c1030257366de7a2d67e917a63064c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WxR20NKT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9o4k85zlwgxq2ye5qagg.png) 截图 1.3 演示示例

下面的屏幕截图是向用户对象添加一个新的键-值对。“Vue instance.object”即 element.users 可在控制台中访问，向用户添加新用户会更改 UI。

[![adding_new_element](img/75592a4ce74e59f94fbd03b470735604.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v9ywSrDB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pvq5synwhj2ln2oov7az.png) 截图 1.4 Vue 中的反应对象

这是 Vue.js 中的反应性。

Vue 如何改变用户界面是一件有趣的事情。我简单解释一下。
当我们将一个普通对象的数据属性添加到 Vue 实例中时。Vue 获取所有对象，并为数据中的每个对象创建 getter 和 setter 方法(在上面的例子中是 users 对象)。

**I]Object.defineProperty**
Vue 使用 object . define property 作为对象上的 setter 和 getter 方法。观察者模式用于更新 Vue 中对象的值。

[![define_property](img/c795d90f815bcfdce69b129107c3987b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nry7UkE3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1lsozf2zedivd73hcz3j.png) 截图 1.5 向用户对象添加 Getter 和 Setter

不要担心下面使用的术语。请查看此视频，[反应性系统](https://www.vuemastery.com/courses/advanced-components/build-a-reactivity-system)解释了反应性的详细信息。

**注意:-如果你看过上面的视频，那么下面的内容将开始有意义。**

这个 getter 和 setter 方法分别有 **depends()** 和 **notify()** 方法。
**depends()**方法将目标推送到存储器(存储器不过是一个空数组)。
**notify()**方法运行所有的目标。
当你调用一个对象时，然后 **dep.depends()** 方法被调用并推送你的函数(目标)。
当你设置一个对象时，那么 **dep.notify()** 方法得到调用并运行目标。这使得系统被动反应。

因此，**我们面临的问题是将布尔值设置为名为“active”的对象键并不能更新 UI。**数据的结构如下，

[![data_structure](img/765f78d5c42cace16aac6fd07222f29f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hlCpZ0t4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hshs3lpr10636bwtticg.png) 截图 1.6 对象的数据结构。

**注意:pagination . pendingussinvitation 是 Vue 实例。你可以在声明 Vue 实例的截图 1.1 中看到。**
该数据没有“活动”键。我们正在明确地增加数据。如下

```
 pagination.pendingUsersInvitation[0][active] = True; 
```

Enter fullscreen mode Exit fullscreen mode

但是在这里，Vue 不理解数据(pagination . pendingussinvitation)已经被修改，这将不会更新对 UI 的更改。

因此，要让 Vue 知道数据已经更改，以便 Vue 可以更新 UI。我们将代码改为

```
 Vue.set(pagination.pendingUsersInvitation[0], 'active', true); 
```

Enter fullscreen mode Exit fullscreen mode

这使得 UI 发生了变化，因为 Vue 知道数据已经发生了变化。

在实际中我们已经做过这样的事情，
[![vue_set](img/071c3ded700ac0232748ac33191cee4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Rm5JFrw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwitxrmifnc9jlyz51h9.png) 截图 1.7 使用 Vue.set

在这里，我们通过比较数据中的 item . PK = = id(pagination . pendingussinvitation)来设置活动键。因此，如果条件得到满足，活动键就会被添加到数据中。代码的这一变化更新了 UI。

最后，解决了“插值”和“数组和对象变化检测”问题。

### 分页组件

分页组件准备如下。

[![pagination_component](img/1be6f7e30933f3dd13c6e9204ff6abe6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mqZz5HBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b8yefya43lsmlf6eqd3c.jpg)1.8 分页组件截图

## 结论

Vue.js 中熟悉的东西

*   垂直模型与(ng-angular js 中的模型)相同
*   v-if 与(ng-if in AngularJS)相同
*   v-与 as 相同(ng-以角度重复)
*   v-bind:类与(在 AngularJS 中视为 ng 类)相同

由于来自 AngularJS 的背景，学习 Vue.js 既有趣又容易。试试 Vue.js，让我们知道你使用 Vue.js 的感受。