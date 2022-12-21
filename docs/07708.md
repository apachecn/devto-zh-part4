# 揭秘@babel/preset-env

> 原文：<https://dev.to/jnielson94/demystifying-babel-preset-env-3h88>

*最初发表于[jnielson.com](https://jnielson.com/demystifying-babel-preset-env)。*

理想情况下，我的帖子[揭秘的构建工具](https://jnielson.com/build-tools-demystified-my-thoughts)有助于澄清使用 babel 和 webpack 时的一些基本概念。如果没有，请让我知道可以澄清或进一步补充的事情！我希望这个系列能对我基于它的演讲之外的人有所帮助。我知道对我来说，阅读比听演讲更容易消化，所以我希望这种形式能帮助到一些人。

当与人们谈论巴别塔时，我发现一个非常有趣的话题是他们提供的`preset-env`。因为巴别塔是一个开源项目，你可以在这里找到`@babel/preset-env`的代码，但是在这篇文章中，我们将会探究一下为什么它对于那些使用巴别塔的人来说是如此受欢迎的预置。作为参考，[有专门针对该预置](https://babeljs.io/docs/en/babel-preset-env)的文档，详细说明了其支持的选项。一般来说，你对`preset-env`(以下我称之为“预设”)的使用可以简单到有一个包含
的`babel config file`

```
{  "presets":  ["@babel/preset-env"]  } 
```

虽然这个简单的用法有效，但他们的文档中写道:

> “我们不建议以这种方式使用`preset-env`,因为它没有利用其针对特定浏览器的能力。”

我还要补充一点，因为这样使用它会导致将你的所有代码*转换成 ES5，这在很多情况下是不需要的——除非你能支持非常老的环境。在大多数情况下，至少在我工作的地方，我们通常只支持相对较新的浏览器，预置中最酷的特性之一是它与`Browserslist`集成，允许你使用其他工具使用的相同目标。例如，您可以使用一个`.browserslistrc`文件来指定您的目标，比如:* 

```
last 1 version
> 1%
maintained node versions
not dead 
```

使用这种方法的好处之一是，您不必手动维护浏览器的版本以及您的目标，让您可以专注于自己编写的代码。

### 使用调试选项

除了令人难以置信的`Browserslist`集成，还有许多其他选项可以传递给预置，以便定制甚至调试它。例如，如果你像这样使用它:

```
{  "presets":  [  [  "@babel/preset-env",  {  "debug":  true  }  ]  ]  } 
```

您将获得一系列有用的调试信息，帮助您了解正在应用的插件(以及需要它们的浏览器)，如下所示:

```
@babel/preset-env: `DEBUG` option
Using targets:
{
  "android": "67",
  "chrome": "74",
  "edge": "17",
  "firefox": "66",
  "ie": "10",
  "ios": "12",
  "node": "10.16",
  "opera": "12.1",
  "safari": "12",
  "samsung": "8.2"
}
Using modules transform: false Using plugins:
  transform-template-literals { "android":"67", "ie":"10", "ios":"12", "opera":"12.1", "safari":"12" }
  transform-literals { "android":"67", "ie":"10", "opera":"12.1" }
  transform-function-name { "android":"67", "edge":"17", "ie":"10", "opera":"12.1" }
  transform-arrow-functions { "android":"67", "ie":"10", "opera":"12.1" }
  transform-block-scoped-functions { "android":"67", "ie":"10", "opera":"12.1" }
  transform-classes { "android":"67", "ie":"10", "opera":"12.1" }
  transform-object-super { "android":"67", "ie":"10", "opera":"12.1" }
  transform-shorthand-properties { "android":"67", "ie":"10", "opera":"12.1" }
  transform-duplicate-keys { "android":"67", "ie":"10", "opera":"12.1" }
  transform-computed-properties { "android":"67", "ie":"10", "opera":"12.1" }
  transform-for-of { "android":"67", "ie":"10", "opera":"12.1" }
  transform-sticky-regex { "android":"67", "ie":"10", "opera":"12.1" }
  transform-dotall-regex { "android":"67", "edge":"17", "firefox":"66", "ie":"10", "opera":"12.1" }
  transform-unicode-regex { "android":"67", "ie":"10", "opera":"12.1" }
  transform-spread { "android":"67", "ie":"10", "opera":"12.1" }
  transform-parameters { "android":"67", "edge":"17", "ie":"10", "opera":"12.1" }
  transform-destructuring { "android":"67", "ie":"10", "opera":"12.1" }
  transform-block-scoping { "android":"67", "ie":"10", "opera":"12.1" }
  transform-typeof-symbol { "android":"67", "ie":"10", "opera":"12.1" }
  transform-new-target { "android":"67", "ie":"10", "opera":"12.1" }
  transform-regenerator { "android":"67", "ie":"10", "opera":"12.1" }
  transform-exponentiation-operator { "android":"67", "ie":"10", "opera":"12.1" }
  transform-async-to-generator { "android":"67", "ie":"10", "opera":"12.1" }
  proposal-async-generator-functions { "android":"67", "edge":"17", "ie":"10", "opera":"12.1" }
  proposal-object-rest-spread { "android":"67", "edge":"17", "ie":"10", "opera":"12.1" }
  proposal-unicode-property-regex { "android":"67", "edge":"17", "firefox":"66", "ie":"10", "opera":"12.1", "samsung":"8.2" }
  proposal-json-strings { "android":"67", "edge":"17", "ie":"10", "opera":"12.1", "samsung":"8.2" }
  proposal-optional-catch-binding { "android":"67", "edge":"17", "ie":"10", "opera":"12.1", "samsung":"8.2" }
  transform-named-capturing-groups-regex { "android":"67", "edge":"17", "firefox":"66", "ie":"10", "opera":"12.1", "samsung":"8.2" }
Using polyfills: No polyfills were added, since the `useBuiltIns` option was not set. 
```

上面的内容是使用下面的`Browserslist`查询生成的:`last 2 versions, current node`。正如您可能猜到的，在大多数应用程序中，这包括了比您实际需要支持的更多的内容，事实上，使用`npx browserslist 'last 2 versions, current node'`可以立即打印出下面的列表:

```
and_chr 75
and_ff 67
and_qq 1.2
and_uc 11.8
android 67
baidu 7.12
bb 10
bb 7
chrome 75
chrome 74
edge 18
edge 17
firefox 67
firefox 66
ie 11
ie 10
ie_mob 11
ie_mob 10
ios_saf 12.2
ios_saf 12.0-12.1
kaios 2.5
node 10.16.0
op_mini all
op_mob 46
op_mob 12.1
opera 58
opera 57
safari 12.1
safari 12
samsung 9.2
samsung 8.2 
```

## 为什么目标这么有用，这么重要？

如果您不需要支持 ie10 之类的东西，您可能应该调整您的查询，就像上面的`.browserslistrc`文件中使用的例子一样。运行该查询，`npx browserslist 'last 1 version, > 1%, maintained node versions, not dead'`给出以下输出:

```
and_chr 75
and_ff 67
and_qq 1.2
and_uc 11.8
android 67
baidu 7.12
chrome 75
chrome 74
chrome 73
edge 18
edge 17
firefox 67
firefox 66
ie 11
ie_mob 11
ios_saf 12.2
ios_saf 12.0-12.1
kaios 2.5
node 8.16.0
node 12.5.0
node 10.16.0
op_mini all
op_mob 46
opera 58
safari 12.1
samsung 9.2 
```

做这个改变时，我们放弃了对一些过时的东西的支持，比如 bb 10 和 bb 7(黑莓浏览器)，并增加了对更多节点版本(8 和 12)的支持。我们还获得了一个额外的 chrome 版本，可能是因为它目前的使用量。

这个列表的预置调试输出现在看起来是这样的:

```
@babel/preset-env: `DEBUG` option
Using targets:
{
  "android": "67",
  "chrome": "73",
  "edge": "17",
  "firefox": "66",
  "ie": "11",
  "ios": "12",
  "node": "8.16",
  "opera": "46",
  "safari": "12.1",
  "samsung": "9.2"
}
Using modules transform: false Using plugins:
  transform-template-literals { "android":"67", "ie":"11", "ios":"12", "safari":"12.1" }
  transform-literals { "android":"67", "ie":"11" }
  transform-function-name { "android":"67", "edge":"17", "ie":"11" }
  transform-arrow-functions { "android":"67", "ie":"11" }
  transform-block-scoped-functions { "android":"67" }
  transform-classes { "android":"67", "ie":"11" }
  transform-object-super { "android":"67", "ie":"11" }
  transform-shorthand-properties { "android":"67", "ie":"11" }
  transform-duplicate-keys { "android":"67", "ie":"11" }
  transform-computed-properties { "android":"67", "ie":"11" }
  transform-for-of { "android":"67", "ie":"11" }
  transform-sticky-regex { "android":"67", "ie":"11" }
  transform-dotall-regex { "android":"67", "edge":"17", "firefox":"66", "ie":"11", "opera":"46" }
  transform-unicode-regex { "android":"67", "ie":"11" }
  transform-spread { "android":"67", "ie":"11" }
  transform-parameters { "android":"67", "edge":"17", "ie":"11" }
  transform-destructuring { "android":"67", "ie":"11" }
  transform-block-scoping { "android":"67", "ie":"11" }
  transform-typeof-symbol { "android":"67", "ie":"11" }
  transform-new-target { "android":"67", "ie":"11" }
  transform-regenerator { "android":"67", "ie":"11" }
  transform-exponentiation-operator { "android":"67", "ie":"11" }
  transform-async-to-generator { "android":"67", "ie":"11" }
  proposal-async-generator-functions { "android":"67", "edge":"17", "ie":"11", "node":"8.16", "opera":"46" }
  proposal-object-rest-spread { "android":"67", "edge":"17", "ie":"11", "opera":"46" }
  proposal-unicode-property-regex { "android":"67", "edge":"17", "firefox":"66", "ie":"11", "node":"8.16", "opera":"46", "samsung":"9.2" }
  proposal-json-strings { "android":"67", "edge":"17", "ie":"11", "node":"8.16", "opera":"46", "samsung":"9.2" }
  proposal-optional-catch-binding { "android":"67", "edge":"17", "ie":"11", "node":"8.16", "opera":"46", "samsung":"9.2" }
  transform-named-capturing-groups-regex { "android":"67", "edge":"17", "firefox":"66", "ie":"11", "node":"8.16", "opera":"46", "samsung":"9.2" }
Using polyfills: No polyfills were added, since the `useBuiltIns` option was not set. 
```

如果您的组织已经决定完全放弃对 Internet Explorer 的支持，您可以在您的查询`not ie 11, not ie_mob 11`中添加一个排除项，并将其从列表中删除。如果你打算这样做，你甚至可以说服你的组织放弃 caniuse 中所谓的“Android 浏览器”(上面的“android 67”)，因为它的使用率为 0%，从而进一步减少你应用的转换量。因此，将`not android 67`添加到您的查询中。一旦你这样做了，预设的调试输出看起来更像这样:

```
@babel/preset-env: `DEBUG` option
Using targets:
{
  "chrome": "73",
  "edge": "17",
  "firefox": "66",
  "ios": "12",
  "node": "8.16",
  "opera": "46",
  "safari": "12.1",
  "samsung": "9.2"
}
Using modules transform: false Using plugins:
  transform-template-literals { "ios":"12", "safari":"12.1" }
  transform-function-name { "edge":"17" }
  transform-dotall-regex { "edge":"17", "firefox":"66", "opera":"46" }
  transform-parameters { "edge":"17" }
  proposal-async-generator-functions { "edge":"17", "node":"8.16", "opera":"46" }
  proposal-object-rest-spread { "edge":"17", "opera":"46" }
  proposal-unicode-property-regex { "edge":"17", "firefox":"66", "node":"8.16", "opera":"46", "samsung":"9.2" }
  proposal-json-strings { "edge":"17", "node":"8.16", "opera":"46", "samsung":"9.2" }
  proposal-optional-catch-binding { "edge":"17", "node":"8.16", "opera":"46", "samsung":"9.2" }
  transform-named-capturing-groups-regex { "edge":"17", "firefox":"66", "node":"8.16", "opera":"46", "samsung":"9.2" }
Using polyfills: No polyfills were added, since the `useBuiltIns` option was not set. 
```

这些调整很重要，原因有很多，但最大的原因是你在 Babel 中使用的每个插件都会影响这个过程的时间。虽然这在您的应用程序中可能看起来不算什么，但它可以累加起来。对于那些不喜欢数数的人来说，调整我们的目标将我们使用的转换列表从 28 个减少到 9 个。虽然这确实排除了一些可能的用户，但您需要通过分析来确定这是否真的重要。如果是这样的话，你可能会看到模块/非模块分裂成两个不同的包，【Jake Archibald 在上有一篇很好的文章。

预置支持的另一个特性是模块转换(您可能已经注意到在上面的调试日志中提到过)。这种转换有多种模式，默认为“自动”(通常以 commonjs 结束)。对于那些使用 webpack 捆绑你的代码的人来说，你会想要设置`modules: false`以允许 webpack 的酷特性，比如树抖动，工作。如果不设置`modules: false`，babel 会将导入/导出语句转换成 require/ `module.exports`语句(又名非 ES6 模块)，webpack 无法对其进行静态分析。在库代码的情况下，您可能想要生成一个 ES6 模块版本和一个 commonjs 版本，但可能不是。

总而言之，`@babel/preset-env`是一个智能预设——一个插件的集合，这些插件根据你给它的目标来启用或禁用，以将你的代码转换成与你的目标兼容的东西。希望你能从中学到一些东西，我在写的时候当然也是这样！

*封面图片由 undraw.co 提供*