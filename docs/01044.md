# 在 GitHub 动作上运行跨浏览器 e2e 测试

> 原文：<https://dev.to/mizchi/run-crossbrowser-e2e-testing-on-github-actions-671>

GitHub Actions 可以使用 windows 和 mac 的 ci 容器，所以我想，“IE11
和 Safari 可以在 CI 上工作？”。从结论上来说，有效！

我在 GitHub Actions 上成功运行了 windows/ie11 MAC/safari windows/chrome windows/Firefox。

我的工作例子在这里。[https://github.com/mizchi/frontend-gh-action-playground](https://github.com/mizchi/frontend-gh-action-playground)

## 设置

我使用 node.js selenium-webdriver 和 jest(-ts)。

```
yarn add jest ts-jest selenium-webdriver chromedriver geckodriver -D 
```

（...设置您自己的`jest.config.js`。[例子](https://github.com/mizchi/frontend-gh-action-playground/blob/master/jest.config.js)

## 浏览器的简单 e2e 场景

```
// e2e/runWithSelenium.ts
import webdriver from "selenium-webdriver";
import assert from "assert";
import path from "path";

let driver: webdriver.WebDriver;

beforeAll(async () => {
  let capabilities: webdriver.Capabilities;
  switch (process.env.BROWSER || "chrome") {
    case "ie": {
      // HACK: include IEDriver path by nuget
      const driverPath = path.join(
        __dirname,
        "../Selenium.WebDriver.IEDriver.3.150.0/driver/"
      );
      process.env.PATH = `${process.env.PATH};${driverPath};`;
      capabilities = webdriver.Capabilities.ie();
      capabilities.set("ignoreProtectedModeSettings", true);
      capabilities.set("ignoreZoomSetting", true);
      break;
    }
    case "safari": {
      capabilities = webdriver.Capabilities.safari();
      break;
    }
    case "firefox": {
      require("geckodriver");
      capabilities = webdriver.Capabilities.firefox();
      break;
    }
    case "chrome": {
      require("chromedriver");
      capabilities = webdriver.Capabilities.chrome();
      capabilities.set("chromeOptions", {
        args: [
          "--headless",
          "--no-sandbox",
          "--disable-gpu",
          "--window-size=1980,1200"
        ]
      });
      break;
    }
  }
  driver = await new webdriver.Builder()
    .withCapabilities(capabilities)
    .build();
});

afterAll(async () => {
  await driver.quit()
});

it("Google", async () => {
  await driver.get('http://google.com');
  assert.equal(await driver.getTitle(), "Google");
}); 
```

您可以使用`BROWSER=chrome jest e2e/runWithSelenium.ts`运行这个脚本。

## 设置工作流程

我会在 windows 和 mac 上的 github 动作上使用它。

```
# .github/workflows/xbrowser.yaml
name: xbrowser

on: [push]

jobs:
  e2e-ie:
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v1
      - uses: warrenbuckley/Setup-Nuget@v1
      - uses: actions/setup-node@v1
        with:
          node-version: 12
      - run: yarn install
      - run: nuget install Selenium.WebDriver.IEDriver -Version 3.150.0
      - run: yarn jest e2e/runWithSelenium.test.ts
        env:
          BROWSER: ie
  e2e-safari:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v1
      - uses: actions/setup-node@v1
        with:
          node-version: 12
      - run: yarn install
      - run: |
          sudo safaridriver --enable
          safaridriver -p 0 &
      - run: yarn jest e2e/runWithSelenium.test.ts
        env:
          BROWSER: safari
  e2e-firefox:
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v1
      - uses: actions/setup-node@v1
        with:
          node-version: 12
      - run: yarn install
      - run: yarn jest e2e/runWithSelenium.test.ts
        env:
          BROWSER: firefox
  e2e-chrome:
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v1
      - uses: actions/setup-node@v1
        with:
          node-version: 12
      - run: yarn install
      - run: yarn jest e2e/runWithSelenium.test.ts
        env:
          BROWSER: chrome 
```

它是这样工作的[https://github . com/miz chi/frontend-GH-action-playground/pull/5/checks](https://github.com/mizchi/frontend-gh-action-playground/pull/5/checks)