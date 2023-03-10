# 通过 Transloco 使用 MessageFormat

> 原文：<https://dev.to/coly010/using-messageformat-with-transloco-30em>

# 🎸通过 Transloco 使用 MessageFormat

最近(2019-08-16)发布了一个角度国际化库 Transloco ，口号是“角度国际化做对了”。在使用该库之后，可以清楚地看到它极大地简化了 Angular 应用程序中的 i18n，提供了在模板中使用它的多种方法:

*   结构指令
*   属性指令
*   管

上述方法中性能最好的是结构指令方法，因为它只为你的模板打开一个订阅。你可以在他们的[文档](https://github.com/ngneat/transloco/blob/master/README.md)中找到更多关于合并 Transloco 的信息。

然而，很快就发现这个库不支持开箱即用的消息格式。这对我们来说可能是一个真正的交易破坏者，对其他使用`messageformat`来处理应用程序中多元化和性别的公司来说也是如此。如果这些短语对您来说是新的，您是否遇到过以下情况:

```
return mins + mins === 1 ? ' minute' : ' minutes' + ' ago';

let pronoun = 'They';
if (user.gender === 'male') {
    pronoun = 'He';
} else if (user.gender === 'female') {
    pronoun = 'She';
}

return `${pronoun} went to the shop.`; 
```

Enter fullscreen mode Exit fullscreen mode

每次我们需要处理复数和性别时都这样做可能会很乏味，以至于我们可能会将它们放入它们自己的方法或服务中以重用它们。但是，如果我们可以将这种逻辑从我们的代码库中完全删除呢？如果我们需要支持多种语言，我们需要添加额外的逻辑来返回活动语言的正确代词吗？

不。相反，我们应该让我们的翻译文件为我们处理这些！我最近给 [Transloco](https://github.com/ngneat/transloco) 库做了一个 [PR](https://github.com/ngneat/transloco/pull/15) 来增加对`messageformat`的支持。目前，它将是 Transloco > = 1.1.1 的一部分，但是有计划将这一功能转移到一个插件中，因为并非所有用户都需要它。

下面我将向你展示如何设置和使用 Transloco 的`messageformat`。

## 🔧先决条件

确保您在全球范围内安装了 Angular CLI。如果没有，请运行以下命令:

`npm install -g @angular/cli`

如果您当前没有正在开发的应用程序，请使用以下命令创建一个新的应用程序:

`ng new <application-name>`

## 🔨设置

如果您还没有安装 Transloco，首先，使用`ng add @ngneat/transloco`命令添加它。这将安装所需的包，并为`Transloco`的运行搭建所需的文件。

你可以在这里找到更多关于安装 Transloco 的信息。

要使用`messageformat`，我们需要告诉 Transloco 使用`MessageFormatTranspiler`而不是`DefaultTranspiler`。为此，打开你的`app.module.ts`和下面的:

```
import { TRANSLOCO_TRANSPILER, MessageFormatTranspiler } from '@ngneat/transloco'; 
```

Enter fullscreen mode Exit fullscreen mode

在`@NgModule`的
的提供者数组中

```
@NgModule({
    providers: [
        ...,
        { provide: TRANSLOCO_PROVIDER, useClass: MessageFormatTranspiler }
    ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

这将允许 Transloco 正确解释翻译文件中的`messageformat`格式字符串。

## 🚀使用

设置完成后，使用 messageformat 非常简单。现在，您可以在翻译文件中执行以下操作:

`en.json`

```
{
  "noParams": "Hello - english",
  "params": "The following {{ value }} was replaced by Transloco - english",
  "plural": "{count, plural, =0 {no results} one {one result} other {# results}} - english",
  "gender": "{gender, select, male {he} female {she} other {they}} went to the shop - english"
} 
```

Enter fullscreen mode Exit fullscreen mode

`es.json`

```
{
  "noParams": "Hello - spanish",
  "params": "The following {{ value }} was replaced by Transloco - spanish",
  "plural": "{count, plural, =0 {no results} one {one result} other {# results}} - spanish",
  "gender": "{gender, select, male {he} female {she} other {they}} went to the shop - spanish"
} 
```

Enter fullscreen mode Exit fullscreen mode

在您的模板中:

结构指导方法

```
<ng-container *transloco="let t">
    <ul>
        <li>{{ t.noParams }}</li>
        <li>{{ t.params | translocoParams: {value: "word" } }}</li>
        <li>{{ t.plural | translocoParams: {count: 2} }}</li>
        <li>{{ t.plural | translocoParams: {count: 1} }}</li>
        <li>{{ t.gender | translocoParams: {gender: "male"} }}</li>
        <li>{{ t.gender | translocoParams: {gender: ""} }}</li>
    </ul>
</ng-container> 
```

Enter fullscreen mode Exit fullscreen mode

属性指令方法

```
<ul>
    <li><span transloco="noParams"></span> }}</li>
    <li><span transloco="params" [translocoParams]="{value: 'word'}"></span></li>
    <li><span transloco="plural" [translocoParams]="{count: 2}"></span></li>
    <li><span transloco="plural" [translocoParams]="{count: 1}"></span></li>
    <li><span transloco="gender" [translocoParams]="{gender: 'male'}"></span></li>
    <li><span transloco="gender" [translocoParams]="{gender: ''}"></span></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

管道进场

```
<ul>
    <li>{{ 'noParams' | transloco }}</li>
    <li>{{ 'params' | transloco: {value: "word" } }}</li>
    <li>{{ 'plural' | transloco: {count: 2} }}</li>
    <li>{{ 'plural' | transloco: {count: 1} }}</li>
    <li>{{ 'gender' | transloco: {gender: "male"} }}</li>
    <li>{{ 'gender' | transloco: {gender: ""} }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

## 🎉结果

现在，无论您选择哪种方法，当您运行应用程序时，输出将是:

当语言为`en` :

```
Hello - english
The following word was replaced by Transloco - english
2 results - english
1 result - english
He went to the shop - english
They went to the shop - english 
```

Enter fullscreen mode Exit fullscreen mode

当语言为`es` :

```
Hello - spanish
The following word was replaced by Transloco - spanish
2 results - spanish
1 result - spanish
He went to the shop - spanish
They went to the shop - spanish 
```

Enter fullscreen mode Exit fullscreen mode

## 🍻结论

从上面可以看出，使用 MessageFormatTranspiler 和 Transloco 可以大大减少代码库中与复数和性别相关的语法的逻辑数量。这使得 i18n 更容易实现，因为您可以简单地将您的`en.json`文件交给您的翻译团队，他们可以为所有标识符提供翻译。可以添加、删除和维护这些翻译，而不会影响应用程序的业务逻辑！

如果你还没有看过 trans loco,你一定要看看。这很简单！

感谢阅读！