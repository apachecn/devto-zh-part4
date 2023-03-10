# 使用 Laravel 和 Vue.js 构建富搜索

> 原文：<https://dev.to/epse/building-a-rich-search-using-laravel-and-vue-js-3732>

在最长的一段时间里，我工作的内部发票和 CRM 包对所有搜索框使用 [Select2](https://select2.org/) 。主“客户搜索”框添加了一些额外的代码，当您在 Select2 中选择一个客户时，这些代码将加载客户的资料。这是可行的，但是有一些问题:

1.  所有可搜索的数据都必须在 Select2 的`data`字符串中。
2.  我们只能为每个客户显示这个`data`字符串，没有额外的控件、信息或快捷方式。

我着手通过构建一个定制的搜索解决方案来解决这个问题。如果你想继续下去，这不是一个教程，而是对那些熟悉 Laravel 的人的指导。例如，我不会告诉你如何添加路线。

# 后端

我们的管理软件是用 Laravel 编写的，只是在顶部的一些地方添加了一点点 Vue，使其更具交互性。为了搜索，我们已经使用了令人惊叹的口才库。它做了很多，但最重要的是它允许您非常容易地向您的模型添加多列搜索。

```
use Sofa\Eloquence\Eloquence;

class Customer extends Model
{
    use Eloquence;
    protected $searchableColumns = ['first_name', 'last_name', 'email', 'address', 'phone', 'mobile'];
    ...
} 
```

这就是我们需要对模型所做的一切！如果你愿意，你可以为我们的模型设置 JSON 中可见的字段，但是我会让你参考 Laravel 文档。

现在我们需要添加一个控制器方法来处理搜索，并弄清楚 api 将会是什么样子。我们旧的搜索 API 在相当多的地方还是会用，所以不能用`/customers/find?q=<querystring>`。而是和`/customers/findRich?q=<querystring>`一起去了。让我们添加该路由并将其指向一个新的控制器方法。这个方法简单得可笑:

```
 public function findRich(Request $request)
    {
        $term = trim($request->q);
        if (empty($term))
            return response()->json([]);

        $customers = Customer::search($term)->limit(self::SEARCH_LIMIT)->get();

        // This should do automatic and nice JSON
        return $customers;
    } 
```

就是这样！

# 网址

我希望我们的前端能够获得一个`Customer`的 URL，以及为该客户创建一个新的`Repair`或`Quote`以显示在前端的 URL。我决定将属性添加到`Customer`模型中，并将它们包含在 JSON 中，就像这样:

```
protected $appends = ['url'];
public function getUrlAttribute()
{
    return url()->route('customers.show', $this);
} 
```

`$appends`变量可用于向模型的序列化版本添加非列属性。如果使用的话，一定要将相同的属性添加到`$visible`中。否则，它们仍然不会出现在 JSON 中。

# 前端

在每个视图的边栏中，我简单地包含了一个文本输入:

```
<input type="text" id="main-customer-search" v-model="customerSearchTerm" placeholder="Search for customers..." /> 
```

如果你想知道的话，这个`id`是用于我们的侧面键盘快捷键系统的。

我将`customerSearchTerm`添加到主`Vue`实例的`data`对象中，但仅此而已。我们的主实例什么也不做，它只是注册一些外部组件，并在这些组件之间传递一些数据。它连一个方法都没有！

我所有的组件都是 vue 单文件组件，但是我的样式是在一个单独的`SCSS`文件中完成的，以挂钩到我们的引导变量和主题。模板变得非常简单

```
<template>
    <div v-if="visible" id="customer-search-popover">
        <div class="customer-search-modal-header modal-header">
            <h5 class="modal-title">Search results</h5>
            <button type="button" class="close" v-on:click="dismiss" aria-label="Close">
                <span aria-hidden="true">&times;</span>
            </button>
        </div>
        <table class="table table-sm table-hover">
            <thead>
                <th scope="col">Customer</th>
                <th scope="col">Phone</th>
                <th scope="col">Mobile</th>
                <th scope="col">E-mail</th>
                <th scope="col">Actions</th>
                <th scope="col"></th>
            </thead>
            <tbody>
                <tr v-for="customer in customers" v-bind:key="customer.id">
                    <th class="align-middle" scope="row">{{ customer.name }}</th>
                    <td class="align-middle">{{ customer.formatted_phone }}</td>
                    <td class="align-middle">{{ customer.formatted_mobile }}</td>
                    <td class="align-middle"><a :href="'mailto:' + customer.email">{{ customer.email }}</a></td>
                    <td class="align-middle">
                        <a class="btn btn-outline-primary" :href="customer.url">View</a>
                    </td>
                    <td class="align-middle">
                        <a class="btn btn-outline-secondary" :href="customer.quick_repair_url">Repair</a>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template> 
```

您可以看到我们正在使用之前添加的`url`属性。我在这里混合使用了 bootstrap 和自己的类，`SCSS`最终看起来像这样:

```
#customer-search-popover {
    position: absolute;
    width: 100%;
    min-height: 5rem;
    z-index: 1000;
    padding: 1rem;
    background-color: $white;
    box-shadow: $box-shadow-lg;
    border: 1px solid $orange;
    @include border-radius($modal-content-border-radius);
}

.customer-search-modal-header {
    background-color: $orange;
    color: $white;
    margin-top: -1rem;
    margin-left: -1rem;
    margin-right: -1rem;
    margin-bottom: $spacer;
    border-radius: 0;
} 
```

这一点也不花哨，只是设置了一个带有阴影和标题的模态窗口。

Javascript 代码也不花哨，我相信简单明了。如果我们有搜索结果并且用户没有点击关闭按钮，那么模态应该是可见的，如果传入搜索词的属性发生了变化，那么我们调用一个去抖函数从我们之前制作的 API 中获取一组新的搜索结果。最终看起来像这样:

```
 import { debounce, headers } from '../util.js';
 export default {
     props: {
         'searchTerm': {type: String},
         'searchUrl': {required: true, type: String},
         'csrf': {required: true, type: String},
     },
     data() {
         return {
             customers: [],
             hide: false,
         };
     },
     methods: {
         dismiss: function () {
             this.hide = true;
         },
     },
     computed: {
         visible: function() {
             return !this.hide && this.customers && this.customers.length;
         }
     },
     watch: {
         'searchTerm': debounce(function (val, old) {
             this.hide = false;
             fetch(this.searchUrl + '?q=' + encodeURI(this.searchTerm), {
                 headers: headers,
             }).then(res => {
                 if (!res.ok)
                     res.text().then(res => console.error(res));
                 else
                     res.json().then(res => this.customers = res);
             });
         }, 100, false),
     },
 }; 
```

我在这里引入的`debounce`功能不是我自己的发明，我~~剽窃了~~从其他博客中获得的灵感。它只需要一个函数，一个超时，第三个参数可以用来强制执行函数。`headers`只是一组我在整个前端使用的头，所以我把它分开了。必须保持干燥。

我希望这对任何人都有用！干杯！