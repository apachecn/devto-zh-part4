# 向角度应用程序添加下拉列表

> 原文：<https://dev.to/syncfusion/adding-a-drop-down-list-to-your-angular-application-4e1>

在这篇博文中，我们将看看 Essential JS 2 中的 Angular 下拉列表组件。您将学习如何从一个简单的下拉列表开始，然后通过简单的代码片段扩展示例来启用它的几个特性。

下拉列表是一个文本框组件，允许从预定义值列表中选择单个不可编辑的值。

基本的 JS 2 Angular 下拉列表组件通常用在有 5 到 10 个以上选项的应用程序中，并且希望允许用户从中选择一个。它已经实现了您的最终用户所期望的特性，比如搜索(包括对变音符号的支持)、项目分组、级联选择、可访问性、模板化。

## 使用下拉列表组件入门

以下步骤将指导您完成 Angular 应用程序中下拉列表组件的必要配置。

1.使用以下命令克隆基本角度样本。

```
git clone https://github.com/angular/quickstart.git quickstart
cd quickstart
npm install 
```

2.安装在角度环境中渲染组件所需的下拉列表依赖项。

```
npm install @syncfusion/ej2-ng-dropdowns --save 
```

3.在 **systemjs.config.js** 配置文件中映射 sync fusion**ej2-ng-drop downs**依赖包。

```
/**
 * System configuration for Angular samples.
 * Adjust as necessary for your application needs.
 */
(function (global) {
System.config({
    map: {
        // our app is within the app folder
        app: 'app',
        // angular bundles
        // syncfusion bundles
        '@syncfusion/ej2-base': 'syncfusion:ej2-base/dist/ej2-base.umd.min.js',
        '@syncfusion/ej2-data': 'syncfusion:ej2-data/dist/ej2-data.umd.min.js',
        '@syncfusion/ej2-inputs': 'syncfusion:ej2-inputs/dist/ej2-inputs.umd.min.js',
        '@syncfusion/ej2-popups': 'syncfusion:ej2-popups/dist/ej2-popups.umd.min.js',
        '@syncfusion/ej2-lists': 'syncfusion:ej2-lists/dist/ej2-lists.umd.min.js',
        '@syncfusion/ej2-buttons': 'syncfusion:ej2-buttons/dist/ej2-buttons.umd.min.js',
        '@syncfusion/ej2-dropdowns': 'syncfusion:ej2-dropdowns/dist/ej2-dropdowns.umd.min.js',
        '@syncfusion/ej2-ng-base': 'syncfusion:ej2-ng-base/dist/ej2-ng-base.umd.min.js',
        '@syncfusion/ej2-ng-dropdowns': 'syncfusion:ej2-ng-dropdowns/dist/ej2-ng-dropdowns.umd.min.js'
    }
  });
})(this); 
```

4.将 **DropDownListModule** 从**@ sync fusion/ej2-ng-dropdowns**包导入到 Angular 应用( **app.module.ts** )中。

```
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent }  from './app.component';
import { DropDownListModule } from '@syncfusion/ej2-ng-dropdowns';

@NgModule({
  imports:      [ BrowserModule, DropDownListModule ],
  declarations: [ AppComponent ],
  bootstrap:    [ AppComponent ]
}) 
```

5.修改 **app.component.ts** 文件中的模板以呈现 **ejs-dropdownlist** 组件，并通过 [styleUrls](https://angular.io/guide/component-styles#component-styles) 包含来自相应基础包**@ sync fusion/ej2-dropdowns**组件的组件主题。

```
import { Component, ViewEncapsulation  } from '@angular/core';
@Component({
  selector: 'my-app',
   // specifies the template string for the drop-down list component
  template: ``,
})
export class AppComponent  { name = 'Angular'; } 
```

6.从 Syncfusion 包根文件夹导入下拉列表组合 CSS 文件。这可以在 src/styles/styles.css 文件中引用。

```
@import '../../node_modules/@syncfusion/ej2-dropdowns/styles/material.css'; 
```

## 填充数据

初始化后，使用**数据源**属性填充下拉列表中的数据。这里，从远程数据服务中检索数据是在数据管理器组件的帮助下执行的，因此我们需要从 **ej2-data** 包中导入**查询**、**数据管理器**和 **ODataAdaptor** 。

```
import { Query, DataManager, ODataAdaptor } from '@syncfusion/ej2-data'
@Component({
  selector: 'my-app',
   // specifies the template string for the drop-down list component
  template: `<ejs-dropdownlist id='ddlelement' #samples [dataSource]='data' [fields]='fields' [placeholder]='text' [query]='query' [sortOrder]='sorting'></ejs-dropdownlist>`,

})
export class AppComponent  { 
name = 'Angular'; 
     //bind the DataManager instance to dataSource property
    public data: DataManager = new DataManager({
        url: 'https://js.syncfusion.com/demos/ejServices/Wcf/Northwind.svc/',
        adaptor: new ODataAdaptor,
        crossDomain: true
    });
    // maps the appropriate column to fields property
    public fields: Object = { text: 'ContactName', value: 'CustomerID' };
    //bind the Query instance to query property
    public query: Query = new Query().from('Customers').select(['ContactName', 'CustomerID']).take(6);
    //set the placeholder text for drop-down list input
    public text: string = "Select a customer";
} 
```

**注意**:下拉列表使用 dataSource 属性从本地数据源或远程数据服务加载数据。

## 分组下拉列表项

我们可以在下拉列表中将逻辑上相关的项目排列成组。组的标题可以内嵌显示，也可以静态显示在项目组的上方。要显示分组标题，请映射数据绑定上的 [groupBy](https://ej2.syncfusion.com/16.1.32/angular/documentation/drop-down-list/grouping.html?_ga=2.128771404.1708073739.1562561788-1065100418.1555049162) 字段。

[![Drop-Down List with Grouping](img/0b8b7ab3927972344f7ade485fddc31d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0D2GnZCm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2018/08/image-326.png) 

<figure>

<figcaption>下拉列表带分组</figcaption>

</figure>

## 过滤数据

当您希望在下拉列表中显示更多选项时，用户可能会在查找和选择所需选项时遇到一些困难。下拉列表有内置的过滤来解决这个问题。通过设置**允许过滤**为**真**来启用。

```
@Component({
  selector: 'my-app',
   // specifies the template string for the drop-down list component
  template: `<ejs-dropdownlist id='ddlelement' #samples [dataSource]='data' [fields]='fields' [allowFiltering]='true' [placeholder]='text' [query]='query' [sortOrder]='sorting'></ejs-dropdownlist>`,
}) 
```

**注意**:您也可以使用[过滤](https://ej2.syncfusion.com/16.1.32/angular/documentation/drop-down-list/filtering.html?_ga=2.233097694.1708073739.1562561788-1065100418.1555049162)事件应用自定义逻辑来过滤项目。

## 排序数据

排序允许用户通过按升序或降序排列项目，从选项列表中选择正确的项目。使用**排序顺序**设置和数值**下降**或**上升**启用排序。

```
@Component({
  selector: 'my-app',
   // specifies the template string for the DropDownList component
  template: `<ejs-dropdownlist id='ddlelement' #samples [dataSource]='data' [fields]='fields' sortOrder='Descending' [placeholder]='text' [query]='query'></ejs-dropdownlist>`
}) 
```

[![Drop-Down List Sorted in Descending Order](img/e1629207acef4de2c96e9f8a93ad9fd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aKUXcQT9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2018/08/image-327.png) 

<figure>

<figcaption>下拉列表按降序排序</figcaption>

</figure>

## 使用模板自定义外观

Essential JS 2 下拉列表中有模板选项，可以根据您的需要定制默认 UI。您可以自定义下拉列表中的所有元素，如项目、值、页眉和页脚。

### 项目模板

在 [itemTemplate](https://ej2.syncfusion.com/16.1.32/angular/documentation/drop-down-list/templates.html?_ga=2.196583916.1708073739.1562561788-1065100418.1555049162#item-template) 属性的帮助下，可以定制下拉列表中每个列表项的内容。

```
@Component({
  selector: 'my-app',
   // specifies the template string for the drop-down list component
  template: `<ejs-dropdownlist id='ddlelement' #samples [dataSource]='data' [fields]='fields' [allowFiltering]='true' [placeholder]='text' [query]='query'>
                <ng-template #itemTemplate="" let-data="">
                      <!--set the value to itemTemplate property-->
                      <span><span class='name'> {{data.ContactName}}</span>  <span class ='city'><i><sub> -- {{data.City}}</sub></i></span></span>
                </ng-template>
             </ejs-dropdownlist>`
}) 
```

[![Drop-Down List with Item Template](img/e40dcc6c7729bd3c0eebdd801c7c7e15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VFPgsiWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2018/08/image-328.png) 

<figure>

<figcaption>下拉列表带项目模板</figcaption>

</figure>

### 值模板

可以使用 [valueTemplate](https://ej2.syncfusion.com/16.1.32/angular/documentation/drop-down-list/templates.html?_ga=2.196583916.1708073739.1562561788-1065100418.1555049162#value-template) 属性自定义所选值的样式。

### 表头模板

header 元素静态显示在弹出窗口的顶部，任何自定义元素都可以使用 [headerTemplate](https://ej2.syncfusion.com/16.1.32/angular/documentation/drop-down-list/templates.html?_ga=2.196583916.1708073739.1562561788-1065100418.1555049162#header-template) 属性作为 header 元素放置。

```
@Component({
  selector: 'my-app',
   // specifies the template string for the drop-down list component
  template: `<ejs-dropdownlist id='ddlelement' #samples [dataSource]='data' [fields]='fields' [allowFiltering]='true' [placeholder]='text' [query]='query'>
                 <ng-template #headerTemplate="" let-data="">
                    <!--set the value to headerTemplate property-->
                    <span class='head'><span class='name'>Name</span><span class='city'>City</span></span>
                 </ng-template>
            </ejs-dropdownlist>`
}) 
```

[![Drop-Down List with Header Template](img/cde2f8f1a6e46a886e2b740660b7789a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9gocB7yy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2018/08/image-330.png) 

<figure>

<figcaption>下拉列表带表头模板</figcaption>

</figure>

### 页脚模板

通过使用 [footerTemplate](https://ej2.syncfusion.com/16.1.32/angular/documentation/drop-down-list/templates.html?_ga=2.140365299.1708073739.1562561788-1065100418.1555049162#footer-template) 属性，可以将任何自定义元素作为下拉列表弹出窗口的页脚元素。

[![Drop-Down List with Footer Template](img/90afd646c8d7a1785793d3b748875224.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--80PrdiyQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2018/08/image-331.png) 

<figure>

<figcaption>下拉列表带页脚模板</figcaption>

</figure>

**注**:下拉列表还提供了 **noRecordsTemplate** 、 **actionFailureTemplate** 和 **groupTemplate** 。有关更多信息，请参考我们的文档。

## 结论

我们希望这是对下拉列表及其重要特性的有用概述。您可以在我们的文档中探索其他高级功能。如果你想尝试下拉列表组件，你可以下载[免费试用版](https://www.syncfusion.com/downloads/essential-js2?_ga=2.163024380.1708073739.1562561788-1065100418.1555049162)或者玩我们的[在线演示版](https://ej2.syncfusion.com/angular/demos/?_ga=2.163024380.1708073739.1562561788-1065100418.1555049162#/material/dropdownlist/default)。你甚至可以在 [GitHub](https://github.com/syncfusion/ej2-angular-ui-components/tree/master/components/dropdowns) 上查看软件包。如果您有任何问题或反馈，请在下面的评论中告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums?_ga=2.159311994.1708073739.1562561788-1065100418.1555049162)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/) 联系我们。

向你的 Angular 应用程序添加下拉列表的帖子[最先出现在](https://blog.syncfusion.com/post/adding-a-drop-down-list-to-your-angular-application.aspx)[的 Syncfusion 博客](https://blog.syncfusion.com)上。