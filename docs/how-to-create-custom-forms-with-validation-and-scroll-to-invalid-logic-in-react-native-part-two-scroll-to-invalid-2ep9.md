# 如何在 React Native 中创建带有验证和滚动到无效逻辑的自定义表单(第二部分:滚动到无效)

> 原文：<https://dev.to/lfkwtz/how-to-create-custom-forms-with-validation-and-scroll-to-invalid-logic-in-react-native-part-two-scroll-to-invalid-2ep9>

想要保持最新状态？现在看看 React Native，双周刊 [React Native 简讯](https://reactnativenow.com/)

* * *

在本系列的第[部分](https://dev.to/lfkwtz/how-to-create-custom-forms-with-validation-and-scroll-to-invalid-logic-in-react-native-part-one-validation-269o)中，我们创建了一个简单的表单，其中包含一些帮助方法，使我们能够推出自己的验证逻辑。在这一部分，我们将介绍如何让表单自动向上滚动到第一个无效元素。

## 定位元素

实现这一点所需的第一步是确保本地状态的输入对象得到更新，以存储每个单独输入所在的 Y 值。为了存储它，我们将创建一个名为`setInputPosition`的助手，它将为我们的每个输入添加一个`yCoordinate`键。

```
function setInputPosition({ ids, value }) {
  const { inputs } = this.state;

  const updatedInputs = {
    ...inputs
  };

  ids.forEach(id => {
    updatedInputs[id].yCoordinate = value;
  });

  this.setState({
    inputs: updatedInputs
  });
} 
```

这个助手将获取一个数组`ids`,它与本地状态中的输入对象同步。在这里使用数组的优点是，我们可能在同一行上存在多个输入(就像我们在演示应用程序中已经用生日、月份和年份做的那样)。因为这两个输入将共享同一个`yCoordinate`值，我们可以调用这个帮助器方法一次并更新两个。

现在我们已经创建了我们的助手，像前面的许多助手方法一样将它绑定到构造函数——因为它将与我们的状态交互。

要使用它，我们需要使用许多 React 本地组件上公开的 [onLayout](https://facebook.github.io/react-native/docs/view#onlayout) 方法。onLayout 方法将在挂载时和任何布局更改后被调用，并接收一个对象，该对象包含关于元素位置**相对于其父视图**的详细信息(稍后将详细介绍)。

所以，让我们测试一下在表单的第一个输入- `first_name`上调用这个方法。

```
onLayout={({ nativeEvent }) => {
    this.setInputPosition({
        ids: ["first_name"],
        value: nativeEvent.layout.y
    });
}} 
```

现在，当表单被加载时，我们可以在调试器中查看本地状态，我们应该看到这个:

```
inputs: {
    first_name: {
        type: 'generic',
        value: '',
        yCoordinate: 17
    }
} 
```

我们的`yCoordinate`被成功地保存到我们的状态，我们的表单现在知道我们的输入在 ScrollView 中的确切位置。

接下来，我们将把助手方法添加到`last_name`输入和我们的`birthday_month` / `birthday_day`输入中。对于生日输入，我们将只在包含这两个元素的外层`View`上添加一次助手，并在`ids`数组中包含两个键。此时，我们的表单演示应用程序看起来像[这个](https://github.com/lawnstarter/react-native-form-helpers/blob/a903f529e8f9153969b1e090a461c336b8d686bd/demoApp/App.js)。

如果我们重新加载页面并再次检查我们的调试器，我们将看到我们的本地状态:

```
inputs: {
    first_name: {
        type: 'generic',
        value: '',
        yCoordinate: 17
    },
    last_name: {
        type: 'generic',
        value: '',
        yCoordinate: 17
    },
    birthday_day: {
        type: 'day',
        value: '',
        yCoordinate: 142
    },
    birthday_month: {
        type: 'month',
        value: '',
        yCoordinate: 142
    }
} 
```

等等，这里好像有点不对劲...我们的生日月份和日期应该有相同的值，但是为什么我们的名字和姓氏有相同的值呢？我们的`last_name`输入不应该有更高的`yCoordinate`值吗，因为它在屏幕上更低？

如果你看一看我们的演示应用程序中的第 75 行，你会看到如下内容:

```
<View style={styles.container}>
  <ScrollView>

    // first_name inputs are here

    <View> // <--- LINE 75 HERE
      <Text>Last Name</Text>
      <TextInput
        style={styles.input}
        onChangeText={value => {
          this.onInputChange({ id: "last_name", value });
        }}
        onLayout={({ nativeEvent }) => {
          this.setInputPosition({
            ids: ["last_name"],
            value: nativeEvent.layout.y
          });
        }}
      />
      {this.renderError("last_name")}
    </View> 
    // birthday_month and birthday_year inputs are here

  </ScrollView> </View> 
```

你能发现问题吗？记住，`onLayout`方法返回元素**相对于其父视图**的位置。所以我们的`last_name`输入有效地告诉我们`Text`元素的高度，而不是这个输入在屏幕上的位置。这也意味着我们的`first_name`输入正在犯同样的错误。

我们如何解决这个问题？两种方法之一。我们可以将`Text`和`TextInput`移出包装`View`，这样每个元素都是我们的父元素`ScrollView`的直接后代。或者，我们可以将我们的`onLayout`逻辑移到包装`View`中。让我们做后者。

现在，当我们重新加载并检查我们的本地状态时，我们应该将`first_name`的`yCoordinate`设为 0，将`last_name`的`yCoordinate`设为 71。听起来更准确。

## 确定第一个无效元素

我们所有的表单元素目前都适合屏幕，所以让我们添加一些额外的输入和间距，这样我们的表单实际上会滚动一点。

在这里你可以尽情发挥你的创造力，实践我们到目前为止所做的工作——包括测试新的验证类型。如果你想跳过，你可以把我的更新[复制到这里](https://github.com/lawnstarter/react-native-form-helpers/blob/19f07015491a58a683c54a4bb5fa2fb25aae6d8a/demoApp/App.js)和[复制到这里](https://github.com/lawnstarter/react-native-form-helpers/blob/19f07015491a58a683c54a4bb5fa2fb25aae6d8a/demoApp/validation/dictionary.js)。

[![](img/422f194b146effb07c1d043745a57d75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bi-Il9ZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fft072q99ckr7l482zzw.png) 
我们的形式，目前的形式。

此时，我们有一个长表单，它知道每个输入的位置，正确地验证所有输入，并标记无效的输入供用户修复。现在我们需要确定哪个无效元素是第一个——意味着既无效**又无效**的输入具有最低的`yCoordinate`值。

为了确定这一点，我们再写一个 helper。

```
function getFirstInvalidInput({ inputs }) {
  let firstInvalidCoordinate = Infinity;

  for (const [key, input] of Object.entries(inputs)) {
    if (input.errorLabel && input.yCoordinate < firstInvalidCoordinate) {
      firstInvalidCoordinate = input.yCoordinate;
    }
  }

  if (firstInvalidCoordinate === Infinity) {
    firstInvalidCoordinate = null;
  }

  return firstInvalidCoordinate;
} 
```

在之后，这个方法将获取我们的整个输入状态**,我们已经通过我们的验证服务运行了它，并迭代通过每个无效的输入，如果找到一个更小的值，则不断地用一个更小的值替换`firstInvalidCoordinate`值。**

我们还想更新我们的`getFormValidation`方法，通过添加下面的代码作为最后一行来返回这个助手的结果:

```
return getFirstInvalidInput({ inputs: updatedInputs }); 
```

现在在我们表单中的`submit`方法中，如果我们`console.log`输出调用`this.getFormValidation()`的结果，我们应该看到最低的`yCoordinate`值——代表屏幕上的第一个无效元素。

## 滚动到第一个无效输入

到目前为止，所有这些工作都是为我们准备本教程的真正目的，实际上是自动将用户的设备滚动到第一个无效元素。这样，他们知道他们需要纠正什么，并且当他们向下滚动屏幕时能够看到任何其他无效的输入。

为了以编程方式与我们的`ScrollView`交互——我们需要在构造函数上创建一个对元素的引用，并通过`ref`属性附加它。更多细节可以在[这里](https://reactjs.org/docs/refs-and-the-dom.html#creating-refs)找到。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.scrollView = React.createRef();
  }
  render() {
    return <ScrollView ref={this.scrollView} />;
  }
} 
```

现在我们有了一个对它的引用，如果我们的表单对于我们想要滚动到的精确坐标无效，我们可以调用`scrollTo`方法**。我们也可以利用`animated`标志使我们的滚动看起来更专业。** 

```
submit() {
const firstInvalidCoordinate = this.getFormValidation();

    if (firstInvalidCoordinate !== null) {
        this.scrollView.current.scrollTo({
            x: 0,
            y: firstInvalidCoordinate,
            animated: true
        });

        return;
    }

    // if we make it to this point, we can actually submit the form
} 
```

好了，让我们看看一切都连接起来后会是什么样子:

[![](img/6a6e1f470bc9091f63bd14600886bc3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MWWEskYW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jijv4euoosawf0sobhm.gif)

厉害！我们的表单有验证，并自动滚动到第一个无效输入。

如果你这边有些不太正常，请在[查看我们的演示应用程序的当前状态](https://github.com/lawnstarter/react-native-form-helpers/tree/53035b3e0ae6e2a3359760ddd6492e89a081e3d7)。

## 下一步

在本系列的第三部分，也是最后一部分，我们将回顾一些可以改善用户验证体验的方法，尝试另一种获取输入坐标的方法，并分享我们从在 React Native 中构建表单的经验中学到的一些见解。