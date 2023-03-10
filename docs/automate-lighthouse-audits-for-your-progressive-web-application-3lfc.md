# 为您的渐进式 Web 应用程序自动化灯塔审计

> 原文：<https://dev.to/rishikc/automate-lighthouse-audits-for-your-progressive-web-application-3lfc>

我们都知道，当我们开发 web 应用程序时，来自 lighthouse 审计的见解是多么重要和有用。但我们大多数人检查的方式是通过 Chrome devtools 或 lighthouse 扩展手动进行，在我看来，这不是很有成效。

对于我们这些不知道的人来说，使用 lighthouse 审计我们的 web 应用程序主要有四种方式:

*   Chrome 开发工具

*   命令行

*   NPM 模块(我们一会儿会用到)

*   [页面速度洞察](https://developers.google.com/speed/pagespeed/insights/)

出于以编程方式执行 lighthouse 审计的目的，我们将使用 [lighthouse npm 包](https://www.npmjs.com/package/lighthouse)、 [mocha](https://mochajs.org/) 和 [chai](https://www.chaijs.com) 来编写我们的测试，使用 [chrome-launcher](https://www.npmjs.com/package/chrome-launcher) 来运行我们的 lighthouse 测试。

首先，让我们将上面的包作为开发依赖项安装到我们的项目中:

```
npm install lighthouse chrome-launcher chai mocha --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们在我们的`tests`目录中创建一个名为`lighthouse.tests.js`的文件。我们将通过这个文件运行我们的灯塔审计。
在这里，我们将导入 lighthouse 模块和 chrome 启动器——这将使我们能够从本地开发服务器打开我们的网页，并运行审计来测试我们希望 lighthouse 得分达到的最低阈值。

虽然这听起来有很多事情要做，但其实并不多。下面是它在实际代码中的样子:

```
const lighthouse = require("lighthouse");
const chromeLauncher = require("chrome-launcher");

function launchChromeAndRunLighthouse(url, opts, conf = null) {
  return chromeLauncher
    .launch({ chromeFlags: opts.chromeFlags })
    .then(chrome => {
      opts.port = chrome.port;
      return lighthouse(url, opts, conf).then(res =>
        chrome.kill().then(() => res.lhr)
      );
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。我们用`chromeLauncher.launch`方法启动 chrome 浏览器实例，然后用站点 url 和审计配置运行 lighthouse 测试。完成之后，我们关闭/终止 chrome 实例并返回结果。这是它在使用时的样子:

```
launchChromeAndRunLighthouse(testUrl, opts, config).then(res => {
  // Results are available in `res`
}); 
```

Enter fullscreen mode Exit fullscreen mode

所以现在，我们可以将这个调用放在我们的`before`钩子中进行测试，然后对每个指标进行测试，就像这样:

```
describe("Lighthouse Audits", function() {
  // Timeout doesn't need to be same. It can be more or less depending on your project.
  this.timeout(50000);
  let results;
  before("run test", done => {
    launchChromeAndRunLighthouse(testUrl, opts, config).then(res => {
      // Extract the results you need for your assertions.
      done();
    });
  });
  it("performance test", done => {
    // test your performance score against the threshold
    done();
  });
  // Some more tests..
}); 
```

Enter fullscreen mode Exit fullscreen mode

看起来还是怪怪的？放心吧！检查这个存储库，例如用 mocha 设置 [lighthouse 测试，并在您的 web 应用程序中进行尝试！](https://github.com/rishichawda/lighthouse-mocha-example)

*(可以通过[这个链接](https://github.com/GoogleChrome/lighthouse#related-projects)找到相关项目列表，包括一个带有 jest 的例子)*

这种方法可以应用于持续集成/部署环境中的自动化测试，因此您不必担心手动审计您的 web 应用程序并检查它是否满足最低的满意水平。

所以你走吧。这就是我们为渐进式 web 应用程序自动审计灯塔所要做的一切，以确保它们始终值得互联网和用户的数据包使用！

[![worthy](img/9ce94423d84d32463d55181a3fd02bc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zPpPWJI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/bTB3UGn.gif)

*感谢您的阅读！*😄

你也可以通过 [Twitter](https://twitter.com/rishiikc) 和我联系。

黑客快乐！干杯！🎉