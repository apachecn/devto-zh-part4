# React 中的高阶组件(HOC)。什么&为什么？

> 原文：<https://dev.to/vinodchauhan7/high-order-component-hoc-in-react-what-why-ml6>

无论你是有经验的开发人员还是初学者，你都必须知道编程的首要规则，即 D.R.A(不要重复自己),这意味着你必须避免重复代码。考虑到这个 ***ReactJs*** 开发者使用术语 HOC(高阶组件)来减少冗余代码的工作。然而这个术语与 **Javascript** 的高阶函数直接或间接相关。

> 注意:HOC 或 HOF 只是设计模式。这些术语用于定义减少公共代码的工作量和需求的方法。

为了理解整个概念，让我们首先理解什么是高阶函数？我们都知道 ***javascript*** 中函数是对象，那就是说 ***函数可以像其他对象一样赋值给一个变量或者作为参数传递给另一个函数，对吧？如果这是真的，这意味着我们也可以将函数作为另一个函数*** 的结果返回，对吗？

以上陈述非常正确，这一信息使得 javascript 成为一种非常特殊的语言。我们可以这样做函数式编程。高阶函数的经典例子有:闭包、映射、过滤、归约等等..如果你从未使用或听说过这个 HOF，不要担心。

```
var company = function(employeeName,companyName){
    console.log(employeeName+" is working in "+companyName);
}

var employee1 = function(employeeName,companyReference){
    return companyReference(employeeName,"Altran");
}

var employee2 = function(employeeName,companyReference){
    return companyReference(employeeName,"Altran");
}

employee1("Vinod",company);

employee2("Nikhil",company);

output : 
1) Vinod is working in Altran.
2) Nikhil is working in Altran. 
```

上面的可执行代码是高阶函数的一个简单例子。这里，“公司”是一个回调函数，它作为参数传入“雇员”函数，并作为“雇员”函数的结果返回。“员工”是高阶函数，因为它满足成为一个 HOF 的所有要求。
比如 map 函数，把一个函数作为自变量。

```
[1,2,3,4,5].map((i) => (i*2));
output : (5) [2, 4, 6, 8, 10] 
```

然而，让我们假设如果员工‘Nikhil’已经改变了他的组织，那么这种职能回归的策略对我们来说就是失败的。但是等等， ***JS*** 有解决方案，因为我们可以从一个返回函数传递另一个函数，这意味着嵌套函数。让我们看看这个场景的例子。

```
var company = function(employeeName,companyName){
    console.log(employeeName+" is working in "+companyName);
}

var employeeDetails = function(employeeName, companyRef){
    return function(companyName) {
        return companyRef(employeeName,companyName);
    }
}

var employee1 = employeeDetails("vinod",company);

var employee2 = employeeDetails("nikhil",company);

employee1("Altran"); //Vinod is working in Altran.

employee2("Sapient"); //Nikhil is working in Sapient. 
```

如果我们将这种模式用于合成，还会有更多有趣的例子。我希望到目前为止我们都已经理解了什么是高阶函数，所以现在让我们跳到高阶组件。

## 高阶分量

如果你搜索 HOC，定义说 HOC 是一个组件，可以把其他组件作为参数并返回一个新组件。最重要的是，它返回的组件可以呈现传入的原始组件。这意味着 HOC 与 HOF 具有相同的属性。

让我们构建一个例子来理解 HOC。假设我们有三个组成部分'第一'，'第二'，'第三'。这些组件都有一个按钮，点击它可以添加一个新的 css 类。

```
App.js

function App() {
  return (
    <div className="App">
      <First />
      <Second></Second>
      <Third></Third>
    </div>
  );
}

//First.js
const First = () => {
  var [buttonPrimary, setButtonClass] = React.useState(false);

  const handleClick = event => {
    buttonPrimary = buttonPrimary ? false : true;
    setButtonClass(buttonPrimary);
  };

  return (
    <div>
      <button
        className={`btn ${buttonPrimary ? "btn-primary" : ""}`}
        onClick={handleClick}
      >
        First
      </button>
    </div>
  );
};

//Second
const Second = () => {
  var [buttonPrimary, setButtonClass] = React.useState(false);

  const handleClick = event => {
    buttonPrimary = buttonPrimary ? false : true;
    setButtonClass(buttonPrimary);
  };

  return (
    <div>
      <button
        className={`btn ${buttonPrimary ? "btn-primary" : ""}`}
        onClick={handleClick}
      >
        Second
      </button>
    </div>
  );
};

//Third.js
const Third = () => {
  var [buttonPrimary, setButtonClass] = React.useState(false);

  const handleClick = event => {
    buttonPrimary = buttonPrimary ? false : true;
    setButtonClass(buttonPrimary);
  };

  return (
    <div>
      <button
        className={`btn ${buttonPrimary ? "btn-primary" : ""}`}
        onClick={handleClick}
      >
        Third
      </button>
    </div>
  );
}; 
```

在上面的例子中，如果你可以观察到我们在每个组件中有一个共同的点击功能代码，这违反了 D.R.Y .的规则。在点击按钮时，我们只是通过检查“buttonPrimary”变量的值在按钮中添加“btn-primary”类。在这种情况下，我们可以使用“withClick”高阶组件来减少这 3 个组件的代码。

```
withClick.js

function withClick(Component) {
  return () => {
    var [buttonPrimary, setButtonClass] = React.useState(false);

    const handleClick = () => {
      buttonPrimary = buttonPrimary ? false : true;
      setButtonClass(buttonPrimary);
    };

    return (
      <div onClick={handleClick}>
        <Component buttonPrimary={buttonPrimary}></Component>
      </div>
    );
  };
}

export default withClick;

//First.js
import withClick from "./withClick";

const First = ({ buttonPrimary }) => {
  return (
    <div>
      <button className={`btn ${buttonPrimary ? "btn-primary" : ""}`}>
        First
      </button>
    </div>
  );
};

export default withClick(First);

//Second.js
import withClick from "./withClick";

const Second = ({ buttonPrimary }) => {
  return (
    <div>
      <button className={`btn ${buttonPrimary ? "btn-primary" : ""}`}>
        Second
      </button>
    </div>
  );
};

export default withClick(Second);

//Third.js
import withClick from "./withClick";

const Third = ({ buttonPrimary }) => {
  return (
    <div>
      <button className={`btn ${buttonPrimary ? "btn-primary" : ""}`}>
        Third
      </button>
    </div>
  );
};

export default withClick(Third); 
```

> 关键是，我们通过将所有三个组件包装在' withClick' HOC 中来导出它们，以便这些组件可以在其中获得点击功能。

在 React Docs 里面是这样写的我也喜欢***是一个组件把 props 转换成 UI，一个高阶组件把一个组件转换成另一个组件。***
希望你从这篇文章中了解一些东西，并随时澄清任何问题。

[https://codesandbox.io/embed/22nhj](https://codesandbox.io/embed/22nhj)