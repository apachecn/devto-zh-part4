# 反应式表单的自定义验证？

> 原文：<https://dev.to/nishugoel/custom-validation-for-your-reactive-forms-39l7>

在 Angular 中处理表单时，我们可以选择。这种选择类似于选择茶还是咖啡。这个选择是关于根据我们的解决方案的需求选择我们应该使用哪种类型的表单。
我们有两个选择:

*   模板驱动的表单
*   模型驱动的表单也称为反应式表单

现在每一个都有自己的优势，这完全取决于开发者，他更喜欢使用哪一个来满足这一组需求。他是希望快速从用户那里得到输入，而对所有花哨的验证之类的东西不感兴趣，还是希望利用表单能够提供的所有可能性。有些人选择使用模板驱动的表单的原因之一是，在没有真正希望在组件中有表单模型的情况下完成工作，更不用说在 DOM 中很难完成的单元测试了。

有人选择使用模型驱动的表单(反应式表单)的原因之一是，可以从组件类中完成大部分任务，只将输入字段的实例放在模板上，这样更容易进行单元测试，并使用反应式表单的其他强大功能，例如，自定义验证。

在这篇博文中，我们将看到如何在表单中使用自定义验证，并充分利用反应式表单。现在验证器只不过是一个函数。要创建这个验证器函数，语法如下:

`function classValidator(control: AbstractControl) : {[key : string] : boolean} | null {
return null;
}`

现在让我们来打破这种理解。由于我们的自定义验证器是一个函数，我们使用 function 关键字，后跟我们想要创建的验证器的名称。作为一个参数，FormControl 或 FormGroup 使用基类，即 AbstractControl。它的下一部分意味着函数将返回什么类型的值。因此，如果输入字段有问题，它将以键-值对的形式返回一个对象，其中值是布尔类型，键是字符串类型。如果输入字段一切正常，它将返回 null。
所以让我们在这里为年龄创建一个简单的范围验证器:

`function ageValidator (control: AbstractControl):{[key: string]: boolean} | null {
if( control.value !==null && (isNaN(control.value) || control.value <20 || control.value> 70)){
return {'ageValidator': true}
}
return null;
};`

要将其应用到您的输入字段，请在表单组内的表单控件名称上使用您的自定义验证器的名称，

`this.customerForm = this.fb.group({
firstname: ['', [Validators.required, Validators.minLength(5)]],
email: ['', Validators.required],
age: [null, ageValidator]
})`

在模板上，

`<span *ngIf= “customerForm.get(‘age’).errors?.ageValidator”>Only for age group 20 to 70</span>`

现在让我们看看浏览器中的结果。

在这里阅读更多:[https://nishugoel . WordPress . com/2019/07/27/custom-validation-for-your-reactive-forms/](https://nishugoel.wordpress.com/2019/07/27/custom-validation-for-your-reactive-forms/)