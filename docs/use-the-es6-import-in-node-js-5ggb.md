# 使用 Node.js 中的 ES6 导入

> 原文：<https://dev.to/arbaoui_mehdi/use-the-es6-import-in-node-js-5ggb>

如果你决定使用 ES6 `import`而不是`require`，这是一个关于如何使用`babel`实现的简单演示:

–首先安装核心依赖项:

```
npm install babel-register babel-preset-env --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

–创建一个 starter.js 文件，其中包含您的代码必须如何被编译，以及包含您的导入的文件:

```
require("babel-register")({
  presets: ["env"],
});

// Import the rest of our application.
module.exports = require("./test.js"); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以在你的`test.js`文件中使用`import`来代替 require:

```
import validator from "validator";

const email = "yep";

if (!validator.isEmail(email)) {
  console.log("the email is not valid");
} 
```

Enter fullscreen mode Exit fullscreen mode

测试输出:

```
node starters.js 
```

Enter fullscreen mode Exit fullscreen mode