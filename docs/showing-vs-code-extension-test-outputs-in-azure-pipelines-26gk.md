# 在 Azure 管道中显示 VS 代码扩展测试输出

> 原文：<https://dev.to/azure/showing-vs-code-extension-test-outputs-in-azure-pipelines-26gk>

我一直在做我的 [VS 代码](https://code.visualstudio.com?WT.mc_id=devto-blog-aapowell) [概要文件切换扩展](https://marketplace.visualstudio.com/items?itemName=aaronpowell.vscode-profile-switcher&WT.mc_id=devto-blog-aapowell)，我想确保我正在做的一件事是编写测试。如果你是扩展作者，我推荐你阅读 VS 代码团队的[关于编写测试的好指南](https://code.visualstudio.com/api/working-with-extensions/testing-extension?WT.mc_id=devto-blog-aapowell)。

在这篇文章中，我想看看我们如何在一个持续集成管道中组合我们的测试运行的输出，为此我将使用 Azure 管道(这对开源项目是免费的！).

## 为 Azure 管道生成测试输出

Azure Pipelines 支持在[发布测试结果任务](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/test/publish-test-results?view=azure-devops&tabs=yaml&WT.mc_id=devto-blog-aapowell)中我们需要使用的许多不同的测试结果格式，其中之一是 Xunit [，Mocha 支持开箱即用](https://mochajs.org/#xunit)。

太好了，我们可以通过更新[测试运行程序脚本](https://code.visualstudio.com/api/working-with-extensions/testing-extension?WT.mc_id=aaronpowell-blog-aapowell#the-test-runner-script) :
将记者设置为`xunit`

```
import * as path from "path";
import * as Mocha from "mocha";
import * as glob from "glob";

export function run(): Promise<void> {
    // Create the mocha test
    const mocha = new Mocha({
        ui: "tdd",
        reporter: "xunit" //change the reporter to xunit
    });
    mocha.useColors(true);

    const testsRoot = path.resolve(__dirname, "..");

    return new Promise((c, e) => {
        glob(" **/**.test.js", { cwd: testsRoot }, (err, files) => {
            if (err) {
                return e(err);
            }

            // Add files to the test suite
            files.forEach(f => mocha.addFile(path.resolve(testsRoot, f)));

            try {
                // Run the mocha test
                mocha.run(failures => {
                    if (failures > 0) {
                        e(new Error(`${failures} tests failed.`));
                    } else {
                        c();
                    }
                });
            } catch (err) {
                e(err);
            }
        });
    });
} 
```

这工作得很好……除了`xunit`输出是一个 XML 文件，可以被计算机读取，但是对于本地测试来说并不理想，我更喜欢使用 [Spec](https://mochajs.org/#spec) (或者 [Nyan](https://mochajs.org/#nyan) ！)，但是 Mocha 只支持单个记者作为输出。

## 使用多个报表

值得庆幸的是，社区中有人为 Mocha 创建了一个报告器，这是一个允许您向多个报告器输出内容的传递工具！

开始安装 [`mocha-multi-reporters`](https://www.npmjs.com/package/mocha-multi-reporters) :

```
npm install --save-dev mocha-multi-reporters 
```

现在我们可以改变配置 Mocha 的方式，使用任意多的输出报告器:

```
import * as path from "path";
import * as Mocha from "mocha";
import * as glob from "glob";
import { createReport } from "../coverage";

export function run(): Promise<void> {
    const mocha = new Mocha({
        ui: "tdd",
        timeout: 7500,
        reporter: "mocha-multi-reporters",
        reporterOptions: {
            reporterEnabled: "spec, xunit",
            xunitReporterOptions: {
                output: path.join(__dirname, "..", "..", "test-results.xml")
            }
        }
    });

    mocha.useColors(true);

    // snip
} 
```

一旦与发布测试结果的 Azure Pipeline 任务结合，我们现在将在 Azure Pipeline 中看到输出！

[![Test Results in Azure Pipelines](img/598c963b1ee6c31017e12beba55fc955.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8JgxcodQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/vscode-testing-reports-azure-pipelines.png)

你可以看看我的扩展 [Azure Pipelines](https://dev.azure.com/aaronpowell/VS%20Code%20Profile%20Switcher/_build/results?buildId=724&view=ms.vss-test-web.build-test-results-tab) 。