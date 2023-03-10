# 解析 nestjs 项目中的路径别名

> 原文：<https://dev.to/rubiin/resolving-path-alias-in-nestjs-projects-11o1>

在 typescript 中构建一个 nestjs 项目确实很棒，但是随着项目开始增长，您的目录也会增长，从而使您的导入变得冗长，如下所示:

```
import { User } from '../../user/model';
import { Article } from '../../article/model';

import { Cache } from '../../../../cache';
import { MongoDB } from '../../../../mongodb'; 
```

Enter fullscreen mode Exit fullscreen mode

# 路径映射到救援！

TypeScript 允许使用允许任意模块路径(不以“/”或“.”开头)的路径映射)在 tsconfig 文件的编译器选项中指定并映射到文件系统中的物理路径，如下:

```
{
  "compilerOptions": {
    ...
    "baseUrl": "./src",
    "paths": {
      "@datorama/utils/*": ["app/utils/*"],
      "@datorama/pipes/*": ["app/pipes/*"]
    }
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们必须添加的第一个属性是 baseUrl 属性。请注意，路径是相对于 baseUrl 解析的。第二个属性是 paths 属性。这告诉编译器匹配模式“@datorama/utils/*”的任何模块导入都要在以下位置查找:

# 问题

当您以内联方式(在执行期间)运行代码时，它会按预期工作，但是当您为生产构建它并尝试运行它时，您会得到以下错误:

```
Error: Cannot find module '@datorama/utils' 
```

Enter fullscreen mode Exit fullscreen mode

提到的问题是节点的执行。实际上，问题发生在使用节点 dist/main.js 执行构建的文件时，而不是在使用 tsc 的构建过程中。

# 解

在对这个问题做了一些搜索之后，我发现了多种解决方案。

*   [使用 webpack](https://github.com/dividab/tsconfig-paths-webpack-plugin)
*   [使用模块别名包](https://medium.com/@caludio/how-to-use-module-path-aliases-in-visual-studio-typescript-and-javascript-e7851df8eeaa)
*   使用显式参数引导 tsc

虽然上面有很多解决方法，但是第一个和第二个对我来说并不适用。所以我选择了第三种方法，实际上在 [tsconfig-paths](https://github.com/dividab/tsconfig-paths) 的文档中提到过，nest 使用它在运行时解析这些路径。

要应用该解决方案，创建一个名为 tsconfig-paths-bootstrap.js 的文件(名称由您决定),并复制/粘贴下面的代码片段

```
// tsconfig-paths-bootstrap.js

const tsConfig = require('./tsconfig.json');
const tsConfigPaths = require('tsconfig-paths');

const baseUrl = './dist'; // Either absolute or relative path. If relative it's resolved to current working directory.
tsConfigPaths.register({
  baseUrl,
  paths: tsConfig.compilerOptions.paths,
}); 
```

Enter fullscreen mode Exit fullscreen mode

像使用 tsc 一样正常构建 dist 文件。之后就可以用
运行了

```
node -r ./tsconfig-paths-bootstrap.js dist/main.js 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:用命令中的文件名检查文件名，然后就可以开始了

大声喊出来，感谢杰伊·麦克丹尼尔的帮助。

在 Github 上关注我:[www.github.com/rubiin](http://www.github.com/rubiin)