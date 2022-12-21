# 如何在 componentDidMount 中使用 js 文件函数？？？？

> 原文：<https://dev.to/ooo2003003v2/how-do-i-use-js-file-function-in-componentdidmount-jjb>

你好。我有一个问题。我在 js 文件中有一些函数，我想在我的 react js 项目中使用它。我在 head 中引用了它，但是当我在 componentDidMount 中调用 js 文件中的函数 a 时，它显示“' InitInput '未定义 no-undef”
有人有我的问题的解决方案吗？？？
Thx

input . jsx

```
class Input extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();

  }
  componentDidMount() {($(this.myRef ))
  }
} 
```

initInput.js

```
function InitInput(form){

alert()

} 
```

Index.html

```
<html lang="en">
  <head>
 <script src="/jQuery/jquery-3.4.1.min.js" ></script>
    <script  src="/jQuery/initInput.js" ></script>
</head>
...
</html> 
```