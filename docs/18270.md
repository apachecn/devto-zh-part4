# GitHub 的镜像版本

> 原文：<https://dev.to/richard_klose/mirroring-releases-from-github-4an6>

*这篇文章最初发表在[我的博客](https://blog.klose.dev/mirroring-releases-from-github/)上。*

[![Mirroring Releases from GitHub](img/751632e4752869611ab148990f951ef2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--frYSep-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1495722281458-25edbdb08a1c%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在任何时候，开发人员都应该能够从他们的源代码中创建一个可重现的相同的输出。如果你的代码依赖于第三方包，这有时会很棘手。一个问题是一些包从网上下载预编译的版本，通常是从 GitHub 下载，以加速和简化包的安装。然而，如果存储库(或任何其他来源)被移除，构建就不再是可复制的了，它们甚至不能再被编译了。

我想分享一下我如何面对这个问题的方法。但在此之前，我想更详细地解释一下这个问题。如果你对细节不感兴趣，你可以放心地跳过下一段。如果你只是来寻求一个可行的解决方案，请查看我的 [GitHub 库](https://github.com/richardklose/github-release-mirror)。

内容:

1.  [详细问题:软件包如何从 GitHub 下载二进制文件](#chapter-1)
2.  [构建缓存服务器:将 GitHub 版本下载到本地服务器](#chapter-2)
3.  [使用缓存服务器:设置您的环境](#chapter-3)
4.  [选择性地将所有东西打包到 docker 容器中](#chapter-4)

在这篇文章中，我将使用[电子](https://github.com/electron/electron)和[节点-萨斯](https://github.com/sass/node-sass)作为例子，它们可以与 npm 一起安装。它们很受欢迎，也是最初引起我这个问题的包。

### 详细问题:软件包如何从 GitHub 下载二进制文件

从源代码编译 node-sass 需要一些时间。在大多数情况下，这比编译或打包使用它的项目本身花费的时间还要多。因为 node-sass 必须为每个平台和操作系统单独编译，所以它的安装脚本可以从头编译它。让我们更深入地看看 node-sass 是如何安装的。

运行`npm install node-sass`时，执行 package.json 中的安装脚本。(关于 npm 脚本的更多信息，请参见[https://docs.npmjs.com/misc/scripts](https://docs.npmjs.com/misc/scripts)。)

```
{
  ...
  "scripts": {
    "install": "node scripts/install.js",
  },
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>从 node-sass</figcaption>

<的 package.json 调用安装脚本！-kg-卡-end:代号- > <！-kg-卡-begin:代号- >

```
if (process.env.SKIP_SASS_BINARY_DOWNLOAD_FOR_CI) {
  console.log('Skipping downloading binaries on CI builds');
  return;
}
...
if (sass.hasBinary(binaryPath)) {
  console.log('node-sass build', 'Binary found at', binaryPath);
  return;
}
...
if (cachedBinary) {
  console.log('Cached binary found at', cachedBinary);
  ...
  return;
}

download(sass.getBinaryUrl(), binaryPath, function(err) { ... }
... 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>将要执行的 scripts/install.js 的第一行</figcaption>

<！-kg-卡-end:代码- >

从安装脚本的第一行可以看出，在实际的二进制文件下载开始之前，执行了三项检查。

1.  应该跳过下载吗？如果设置了环境变量`SKIP_SASS_BINARY_DOWNLOAD_FOR_CI`，脚本将立即退出。这将确保不下载任何内容，并且从头开始编译二进制文件。(请记住，这个[将来可能会改变](https://github.com/sass/node-sass/issues/1453)。)
2.  在编译目的地已经有二进制文件了吗？如果脚本找到一个二进制文件，它不需要重新编译它。
3.  某些缓存中已经有二进制文件了吗？我不会详细介绍这个缓存，但是可能已经有了一个二进制文件，脚本可以用它来代替下载或再次编译它。

如果三次检查都失败，将调用`download(sass.getBinaryUrl(), ...)`。这就是事情变得有趣的地方。

```
function getBinaryUrl() {
  var site = getArgument('--sass-binary-site') ||
             process.env.SASS_BINARY_SITE ||
             process.env.npm_config_sass_binary_site ||
             (pkg.nodeSassConfig && pkg.nodeSassConfig.binarySite) ||
             'https://github.com/sass/node-sass/releases/download';

  return [site, 'v' + pkg.version, getBinaryName()].join('/');
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>getBinaryUrl 是决定二进制文件从哪里下载的函数</figcaption>

<！-kg-卡-end:代码- >

`getBinaryUrl()`有几个来源，它可以从中获取下载 URL。它们按以下顺序使用:

1.  `npm install --sass-binary-site=<url>`
2.  环境变量`SASS_BINARY_SITE`
3.  来自`.npmrc`的变量`sass_binary_site`
4.  `pkg.nodeSassConfig.binarySite`，其中`pkg`是 node-sass 的`package.json`的内容
5.  默认网址:[https://github.com/sass/node-sass/releases/download](https://github.com/sass/node-sass/releases/download)

如您所见，从 GitHub 下载是获取 node-sass 二进制文件的默认方式，但是，您可以手动设置另一个下载源。这使得我们不再依赖 GitHub。我们可以使用任何其他服务器，通过改变下载 URL，使用这些设置之一。但是在我们这样做之前，我们需要这样一个服务器，它也有所有的文件，可以在 https://github.com/sass/node-sass/releases/download 得到。当然，我们可以手动下载它们，但是如果我们有一个服务器，它也可以自动更新 GitHub 的所有文件，那就更好了。

### 构建缓存服务器:下载 GitHub 版本到本地服务器

为了不再依赖 GitHub 来完成您自己的构建步骤，我们可以为此构建一个本地定制服务器。您将需要以下工具:

*   可以运行 bash 脚本的操作系统。在这篇文章中，我使用的是 Debian，但是任何其他的 Linux 发行版应该也能像 macOS 一样工作。
*   cron，用于安排镜像更新。
*   curl，用于从 GitHub API 获取发布的元数据。
*   jq，用于解析这些元数据。
*   wget，用于下载文件。(我们也可以用 curl 做到这一点，但是 wget 可以自动为我们创建子文件夹)
*   nginx，用于通过 http 传送下载的文件。(当然，任何其他网络服务器也可以)

对于下面的步骤，我假设该命令以 root 用户身份执行。如果您不能或不想使用 root，则应该在必要时使用 sudo。

**第一步:安装包**

从一个最小的 debian 系统开始，必须首先安装所需的软件包:

```
apt-get install -y cron curl jq wget nginx 
```

Enter fullscreen mode Exit fullscreen mode

**步骤 2:创建文件夹和镜像脚本**

首先，我们需要一个地方来存放下载的工件。我将在这里使用`/mirror`。使用以下内容创建目录:

```
mkdir /mirror 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本将只是一个简单的 bash 脚本。因此，在`/opt/mirror.sh`创建一个新文件，并使其可执行:

```
touch /opt/mirror.sh
chmod +x /opt/mirror.sh 
```

Enter fullscreen mode Exit fullscreen mode

**第三步:设置脚本**

在开始实现实际的脚本之前，应该进行一些设置，以便可以轻松地使用该脚本:

```
#!/usr/bin/env bash

REPO=${1}
MIRROR_DIR="/mirror"
SRC_URL="https://api.github.com/repos/${REPO}/releases"
DEST="${MIRROR_DIR}/${REPO}" 
```

Enter fullscreen mode Exit fullscreen mode

一些基本的变量，使得脚本更容易维护 <！-kg-卡-end:代码- >

*   用`REPO=${1}`可以用`/opt/mirror.sh <repo>`调用脚本，所以我们可以在任何 GitHub 仓库中使用它。(例如`/opt/mirror.sh sass/node-sass`或`/opt/mirror.sh electron/electron`)
*   如果由于某种原因，镜像目录必须改变，使用`MIRROR_DIR="/mirror"`可以帮助我们在一个地方完成这项工作，所以我们不必在脚本中改变目录的每一种用法。
*   GitHub API 有一个端点，用于 URL 中带有存储库名称的每个存储库的发布，所以我们将在下一步中针对 enpoint 执行 GET 请求。
*   当然，我们希望能够将这个脚本用于多个存储库，因此每个存储库在`/mirror`中都有自己的子目录。

**第四步:查询 GitHub API 并解析数据**

为了查询来自 GitHub 的数据，我们可以简单地使用`curl ${SRC_URL}`。默认情况下，GitHub API 返回 JSON 数据，如下所示:

```
GET https://api.github.com/repos/sass/node-sass/releases

Response:
[
  {
    url: "https://api.github.com/repos/sass/node-sass/releases/16997851",
    html_url: "https://github.com/sass/node-sass/releases/tag/v4.12.0",
    id: 16997851,
    tag_name: "v4.12.0",
    target_commitish: "master",
    name: "v4.12.0",
    draft: false,
    author: { ... },
    created_at: "2019-04-26T10:18:21Z",
    assets: [
      {
        url: "https://api.github.com/repos/sass/node-sass/releases/assets/12251871",
        id: 12251871,
        name: "win32-x64-11_binding.node",
        browser_download_url: "https://github.com/sass/node-sass/releases/download/v4.12.0/win32-x64-11_binding.node",
        ...
      },
      {
        url: "https://api.github.com/repos/sass/node-sass/releases/assets/12251872",
        id: 12251872,
        name: "win32-x64-11_binding.pdb",
        browser_download_url: "https://github.com/sass/node-sass/releases/download/v4.12.0/win32-x64-11_binding.pdb"
      }
    ],
    ...
  },
  ...
] 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>从 GitHub API</figcaption>

<中剥离出响应！-kg-卡-end:代码- >

如你所见，GitHub 已经给了我们很多关于这个例子中 node-sass 版本的信息。基本上，它是一个发布的数组，其中每个发布本身都有该特定发布的所有资产及其`browser_download_url`的数组。这正是我们想要下载并保存在发布缓存中的文件。此外，每个版本都有一个`tag_name`,在大多数情况下，它是该版本的版本号，来自于那个存储库的 git 标签。我们还应该确保，我们只是镜像实际版本，而不是草稿，所以我们应该看看`draft`属性。关于发布日期、作者等的所有其他信息。可能会很有趣，但现在与我们无关。

这意味着我们可以像这样剥离 JSON:

```
[
  {
    tag_name: "v4.12.0",
    draft: false,
    assets: [
      browser_download_url: "https://github.com/sass/node-sass/releases/download/v4.12.0/win32-x64-11_binding.node",
      browser_download_url: "https://github.com/sass/node-sass/releases/download/v4.12.0/win32-x64-11_binding.pdb"
    ],
    ...
  },
  ...
] 
```

Enter fullscreen mode Exit fullscreen mode

这可以很容易地通过将输出传递给 jq 来完成:

```
curl ${SRC_URL} | jq '[.[] | {tag_name: .tag_name, draft: .draft, assets: [.assets[].browser_download_url]}]' 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>从 GitHub 查询数据，传给 jq</figcaption>

<！-kg-卡-end:代码- >

让我详细解释一下 jq 滤波器:

`.[] | { ... }`:这意味着从数组(`.[]`)中取出所有对象，并将其传递(`|`)到一个新模板中。

`{tag_name: .tag_name, draft: .draft, assets: [.assets[].browser_download_url]}`:模板有三个属性:`tag_name`、`draft`和`assets`。`tag_name`和`draft`用来自源数据的原始值填充，而`assets`被定义为一个新数组，包含来自源数据`assets`中每个对象的`browser_download_url`的所有值。

为了更好地处理，我将把它保存在一个新的变量中。

```
RELEASES=$(curl ${SRC_URL} | jq '[.[] | {tag_name: .tag_name, draft: .draft, assets: [.assets[].browser_download_url]}]') 
```

Enter fullscreen mode Exit fullscreen mode

**第 5 步:准备每个版本的下载**

现在，我们有了一个 JSON 数组，只包含我们需要的数据，我们可以使用 for 循环对对象进行交互并准备资产的下载:

```
for RELEASE in $(echo ${RELEASES} | jq -r '.[] | @base64'); do
  DRAFT=$(echo ${RELEASE} | base64 --decode | jq -r '.draft')
  NAME=$(echo ${RELEASE} | base64 --decode | jq -r '.tag_name')
  URLS=$(echo ${RELEASE} | base64 --decode | jq -r '.assets | .[]')
  ...
done 
```

Enter fullscreen mode Exit fullscreen mode

在这个循环中，我们从 JSON 数据中创建了三个变量:

*   `DRAFT`:包含 JSON 数据中`draft`的值，为`true`或`false`。
*   `NAME`:包含 JSON 数据中`tag_name`的值。稍后我们将使用它为这个特定的版本创建一个子文件夹。在大多数情况下，这是发行版的版本号。
*   `URLS`:包含所有资产 URL，以空格分隔的字符串。Wget 可以轻松处理这个。

Ruben Koster 写了一篇非常棒的博文，解释了为什么我们必须在 bash 循环中对 JSON 进行 base64 编码和解码。

在开始下载之前，我们必须做两项检查。首先，我们应该确保跳过草稿:

```
if [["${DRAFT}" != "false"]]; then
  continue
fi 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们应该检查资产是否已经被下载。

```
RELEASE_DEST=${DEST}/${NAME}
if [[-d ${RELEASE_DEST}]]; then
  continue
fi 
```

Enter fullscreen mode Exit fullscreen mode

**第六步:下载所有文件**

现在，我们可以轻松地下载该特定版本的所有资产。

```
mkdir -p ${RELEASE_DEST}
wget -P ${RELEASE_DEST} --no-verbose ${URLS} 
```

Enter fullscreen mode Exit fullscreen mode

在下载开始之前，我们确保目标目录存在。另外，wget 非常健谈。通过使用`--no-verbose`,我们可以抑制它的输出，这样日志就不会被不必要的下载进度消息所污染。

**脚本完成！**

```
#!/usr/bin/env bash

REPO=${1}
MIRROR_DIR="/mirror"
SRC_URL="https://api.github.com/repos/${REPO}/releases"
DEST="${MIRROR_DIR}/${REPO}"

RELEASES=$(curl ${SRC_URL} | jq '[.[] | {tag_name: .tag_name, draft: .draft, assets: [.assets[].browser_download_url]}]')

for RELEASE in $(echo ${RELEASES} | jq -r '.[] | @base64'); do
  DRAFT=$(echo ${RELEASE} | base64 --decode | jq -r '.draft')
  NAME=$(echo ${RELEASE} | base64 --decode | jq -r '.tag_name')
  URLS=$(echo ${RELEASE} | base64 --decode | jq -r '.assets | .[]')

  if [["${DRAFT}" != "false"]]; then
    continue
  fi

  RELEASE_DEST=${DEST}/${NAME}
  if [[-d ${RELEASE_DEST}]]; then
    continue
  fi

  mkdir -p ${RELEASE_DEST}
  wget -P ${RELEASE_DEST} --no-verbose ${URLS}

done 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>完整镜像脚本</figcaption>

<！-kg-卡-end:代码- >

**步骤 7:自动更新缓存**

使用 cron，我们可以定期运行脚本，例如每天运行，以更新我们的缓存。对于每个应该被镜像的存储库，必须在 crontab ( `crontab -e`)中添加一个新行。

```
0 4 * * * /opt/mirror.sh sass/node-sass
0 5 * * * /opt/mirror.sh electron/electron 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>更新 node-sass 和电子缓存过夜(如凌晨 2:00 和 3:00)</figcaption>

<！-kg-卡-end:代码- >

您应该至少手动运行一次脚本来初始化缓存，并验证它是否工作。

**步骤 8:一个简单的静态 web 服务器，用于 http 访问缓存**

我们最不需要的是网络服务器，它通过 HTTP 传送下载的文件。(提示:虽然我在这篇文章中没有涉及 HTTPS 设置，但是你应该考虑一下你的 web 服务器的 HTTPS 配置。)

有了 nginx，我们可以使用一个简单的轻量级 web 服务器，在`/etc/nginx/sites-enabled/mirror.conf`有如下配置:

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /mirror;

    location / {
        autoindex on;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

创建文件后，应该重新加载 nginx。(例如 Debian 中的 systemd:`systemctl reload nginx`)

您可以使用您最喜欢的浏览器并浏览到服务器的 IP 地址，以验证 web 服务器是否正常工作。

### 使用缓存服务器:设置您的环境

现在，您可以在您的项目中使用该镜像。您可能还记得这篇文章开头的内容，例如，node-sass 有几种方法来定义下载源。我个人更喜欢用`.npmrc`，但你也可以用其他方式。

如果您还没有一个`.npmrc`，创建一个具有该名称的空文件，并为 node-sass 和 electron add:

```
sass_binary_site=http://<ip-of-your-server>/sass/node-sass
electron_mirror=http://<ip-of-your-server>/electron/electron/v 
```

Enter fullscreen mode Exit fullscreen mode

现在，两个包都在该位置查找所需版本的文件夹。我们之前已经用我们的脚本创建了这个文件夹(T0)。请注意，我在末尾为电子添加了一个`v`。这是因为 git 标签，我们用于这个子文件夹名称总是以 v 开头(例如`v2.0.0`)，但是电子安装脚本在搜索下载时使用没有前导`v`的版本号。

### 有选择地把所有东西打包进 docker 容器

我已经在 [GitHub](https://github.com/richardklose/github-release-mirror) 和 [docker Hub](https://cloud.docker.com/repository/registry-1.docker.io/richardklose/github-release-mirror) 上的 Docker 容器中发布了该脚本的完整工作解决方案。

我们在 [Auerswald](https://www.auerswald.de/en/) 使用了与 docker 非常相似的解决方案，但做了一些公司特定的修改。这样，我们可以轻松地维护和更新我们的镜子。