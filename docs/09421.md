# 5 个高级 npm package.json 配置技巧

> 原文：<https://dev.to/davidwells/5-advanced-npm-package-json-config-tips-haa>

> 最初发布于 [davidwells.io](https://davidwells.io/blog/advanced-package-json-tips)

Node，npm，还有`package.json`超级牛逼。

它们一起给开发人员提供了丰富的开发环境&构建开箱即用的系统。

在我做节点开发的这些年里，我收集了一些高级模式，让`npm`脚本& `package.json`更加出色，所以我想我应该分享一下。

**希望这些能帮到丫**。还有，如果有就在下面留言吧！

## 1。使用 NPM 脚本`pre` & `post`生命周期

`npm`为您的包`scripts`内置了生命周期方法。当您将`pre`和`post`与命令一起使用时，它们将在给定脚本之前和之后运行。

**举个例子**

```
{  "name":  "npm-lifecycle-example",  "scripts":  {  "prefoo":  "echo prefoo",  "foo":  "echo foo",  "postfoo":  "echo postfoo"  }  } 
```

运行`npm run foo`将按顺序运行这三个命令

1.  `prefoo`，
2.  `foo`，
3.  然后`postfoo`

会导致:

```
echo prefoo
echo foo
echo postfoo 
```

将此用于`prebuild`和`postbuild`(以及其他任何东西🌈).这对于将 npm 命令链接在一起的部署过程&来说非常方便。

## 2。将参数传递给命令

您可以将值动态传递到 npm 脚本中。

为此，您需要在命令中使用`$npm_config_{key}`值。

**举例:**

```
{  "name":  "passing-flags-into-command",  "scripts":  {  "pass-arg":  "node scripts/lol.js --name=$npm_config_name",  }  } 
```

然后用:

```
npm run pass-arg --name woot 
```

在这个例子中，我们使用参数运行`lol.js`。

```
const argv = require('minimist')(process.argv.slice(2));
console.log(argv.name);
// woot 
```

这对于项目中的临时 CLI 工具来说非常方便。

## 3。使用 package.json `config`键中的值

为了避免在一堆不同的脚本中重复配置，您可以引用来自`package.json`文件`config`字段的值。

```
{  "config":  {  "ENDPOINT":  "my-value"  },  "scripts":  {  "set-env":  "REACT_APP_ENDPOINT=$npm_package_config_ENDPOINT react-scripts start"  }  } 
```

这在某些场合很方便

## 4。将`cross-env`用于跨平台脚本

在脚本中设置环境变量非常简单，但是它们不是跨平台兼容的(咳咳窗口)

npm 中的`cross-env` pkg 允许您设置命令使用的环境变量，这样任何运行您的脚本的人都会很高兴。

**举例:**

```
{  "name":  "using-cross-env",  "scripts":  {  "cross-env":  "cross-env NODE_ENV=prod OTHERFLAG=xyz webpack --config webpack.js",  }  } 
```

这将把`NODE_ENV=prod`和`OTHERFLAG=xyz`设置为`process.env`变量，供 webpack 使用(例如)。

感谢[肯特多德](https://twitter.com/kentcdodds)制作了这颗宝石。

## 5。向其他 npm 命令传递参数

有时我们需要向现有的 npm 脚本传递额外的参数。

我们可以利用`--`分隔符，而不是每次需要 arg 时就修改现有的脚本，或者一遍又一遍地复制脚本。

**例子 json**

```
{  "name":  "passing-flags-example",  "scripts":  {  "my-script":  "esw src/components",  "pass-flags-to-other-script":  "npm run my-script -- --watch",  }  } 
```

下面的例子将把`--watch`标志传递给`my-script`命令

```
npm run pass-flags-to-other-script 
```

将运行:

```
esw src/components --watch 
```

这对于用普通模式和`watch`模式设置`test`脚本非常方便。

## 把所有的东西放在一起

下面是所有组合成一个大型 json 的方法。

```
{  "name":  "advanced-package-json",  "version":  "0.1.0",  "private":  true,  "config":  {  "SESSION_ENDPOINT":  "my-value"  },  "dependencies":  {  "react":  "^15.6.1",  },  "scripts":  {  "pass-arg":  "node scripts/lol.js --name=$npm_config_name",  "set-env":  "REACT_APP_ENDPOINT=$npm_package_config_ENDPOINT react-scripts start",  "cross-env":  "cross-env NODE_ENV=prod OTHERFLAG=xyz webpack --config webpack.js",  "my-script":  "esw src/components",  "pass-flags-to-other-script":  "npm run my-script -- --watch",  },  "devDependencies":  {  "react-scripts":  "^1.1.4",  "cross-env":  "^1.1.4"  }  } 
```

## 你还知道其他的`package.json`小贴士吗？

请在下面的评论中告诉我。

往前走

```
npm init 
```