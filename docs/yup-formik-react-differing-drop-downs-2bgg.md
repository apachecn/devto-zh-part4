# 是的，Formik，反应:不同的下拉列表

> 原文：<https://dev.to/bowlendev/yup-formik-react-differing-drop-downs-2bgg>

这需要熟悉 Formik、Yep 和 React。

今天我从耶那里学到了一些很酷的东西。假设您有两个下拉框或字段，并且您希望确保它们是不同的。有一个很好的方法可以让 Yup 和 Formik 做到这一点。

[![ice cream cones](img/38c89ec8b1037d3df08de3166492e4cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gxwZWnYf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/0%2AARQILtKA_G-Opnp8)

让我们来点简单的:

第一种味道:香草

第二种口味:巧克力

用户能够挑选口味列表并按顺序选择他们最喜欢的。我们希望这些有所不同。所以在我们的代码中可能会有这样的内容:

```
validationSchema: Yup.object({
  vanilla: Yup.string()
           .notOneOf([Yup.ref('chocolate'), null], 
           'Flavors must not match.'),
  chocolate: Yup.string()
             .notOneOf([Yup.ref('vanilla'), null], 
             'Flavors must not match.'),
}) 
```

Enter fullscreen mode Exit fullscreen mode

本质上，这里发生的事情是，Formik 和 Yup 确保在捕获数据之前，两个字段不是相同的值。“no of”方法是检查以确保香草不是巧克力，反之亦然。“Yup.ref”获取另一个下拉列表的值，以便您可以将它传递给“notOneOf”。

干杯！