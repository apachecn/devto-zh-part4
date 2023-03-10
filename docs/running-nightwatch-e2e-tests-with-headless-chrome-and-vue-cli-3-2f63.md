# 使用无头 Chrome 和 Vue CLI 3 运行 Nightwatch E2E 测试

> 原文：<https://dev.to/anthonygore/running-nightwatch-e2e-tests-with-headless-chrome-and-vue-cli-3-2f63>

如果您正在使用一个虚拟开发环境，比如 vagger 或 Docker，或者可能使用一个远程 CI 服务器，那么您可能希望在一个没有专用 GUI 的环境中运行您的 Nightwatch E2E 测试。

在这种情况下，你需要在“无头”模式下使用 Chrome。然而，如果你使用的是 Vue CLI 3 的 Nightwatch 插件，默认设置将是普通 Chrome，而不是 headless。

在这个简短的教程中，我将向您展示如何为 headless Chrome 设置 Ubuntu 服务器，以及如何为 headless 模式配置 Vue CLI 3 Nightwatch 插件。

> 使用浮动跳转到[工作版本。](https://github.com/anthonygore/vue-vagrant-headless-chrome)

## 环境设置

你的 Ubuntu 服务器需要安装 Node、NPM、Vue CLI 3 和 Chrome。您可以在一个浮动文件中使用下面的代码片段，或者从终端手动运行它。

*provision.sh*

```
apt-get update -y && \

## Install Node and NPM

cd /opt && \
wget https://nodejs.org/dist/v10.15.3/node-v10.15.3-linux-x64.tar.xz && \
tar xf node-v10.15.3-linux-x64.tar.xz && \
ln -s -f /opt/node-v10.15.3-linux-x64/bin/node /usr/local/bin/node && \
ln -s -f /opt/node-v10.15.3-linux-x64/bin/node-waf /usr/local/bin/node-waf && \
ln -s -f /opt/node-v10.15.3-linux-x64/bin/npm /usr/local/bin/npm && \

## Install Vue CLI 3

npm install -g @vue/cli@3.5.0 && \
echo "export PATH=\"$PATH:/opt/node-v10.15.3-linux-x64/bin\"" >> /home/vagrant/.bashrc && \
source /home/vagrant/.bashrc && \

## Install Chrome

apt install -y openjdk-11-jdk && \
apt-get install -y libdbusmenu-gtk3-4 libappindicator3-1 libgtk-3-0 libxss1 xdg-utils fonts-liberation && \
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb -P /tmp/ && \
dpkg -i /tmp/google-chrome*.deb 
```

## CLI 视图 3 夜监视插件配置

Vue CLI 3 的默认设置是普通 Chrome 的，而不是 headless 的，所以我们需要改变它。

要让 Chrome 在 headless 模式下运行，你可以在从 CLI 启动时简单地传递一些标志。如果你使用的是 Nightwatch，你可以在配置中将这些标志作为`args`添加到 Chrome 选项中。

默认情况下，Vue CLI 3 Nigthwatch 插件没有配置文件，所以您需要在 Vue 项目的根目录下创建一个:

```
$ touch nightwatch.json 
```

*nightwatch.json*

```
{  "test_settings":  {  "chrome":  {  "desiredCapabilities":  {  "chromeOptions":  {  "args":  [  "--headless",  "--no-sandbox",  "--disable-gpu"  ]  }  }  }  }  } 
```

*注意:我们在这里添加的任何设置都将与[默认配置](https://github.com/vuejs/vue-cli/blob/dev/packages/%40vue/cli-plugin-e2e-nightwatch/nightwatch.config.js)合并。*

## 运行测试

如果您完成了上述测试，您的设置现在就完成了。从安装了该软件的服务器上，运行:

```
$ npm run test:e2e 
```

手指交叉你会看到一些绿色的扁虱！

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *