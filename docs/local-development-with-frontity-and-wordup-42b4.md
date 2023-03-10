# Frontity 和 wordup 的本地开发

> 原文：<https://dev.to/shry/local-development-with-frontity-and-wordup-42b4>

Frontity 是一个开发基于 React 的 WordPress 主题的伟大工具。

对于本地开发，最好也安装一个本地 WordPress，Frontity 需要它，因为它使用 WP API 来检索它的内容。

为了实现无缝的本地开发栈，你可以将 Frontity 与名为 [wordup](https://github.com/wordup-dev/wordup-cli) 的 WordPress 开发工具包结合起来。

## 要求

你需要在你的机器上安装 [Docker](https://docs.docker.com/install/) 和 [wordup-cli](https://github.com/wordup-dev/wordup-cli) ，并设置系统路径。

如果您还没有 Frontity 项目，您可以使用:
创建一个新项目

```
$ npx frontity create my-app && cd my-app 
```

更多信息:

*   [Frontity docs](https://docs.frontity.org/)
*   [文字整理文档](https://docs.wordup.dev)

## 为您的 Frontity 项目设置 wordup

为了在您的项目中使用 wordup，您必须创建一个 wordup 配置文件。 **config.yml** 必须位于**中。项目的 wordup** 文件夹。

文件的内容应该是这样的:

```
 # For Frontity you only need the local WordPress 
# installation. Because you don't develop a theme
# or plugin in a classic way
type: installation

# The internal project name
projectName: Testproject

# The slug of your installation
slug: testproject

# The relative folder where the wp-content folder will be located
srcFolder: wp-content

# The localhost port of your WP server
port: 9000

# WordPress installation config
wpInstall:
  # Title of the WordPress installation
  title: Testproject

  # WordPress users with role
  users:
    - name: admin
      password: 123456
      email: test@example.com
      role: administrator

  # If necessary: custom roles
  roles:
    - name: A Custom editor role
      key: custom_role
      clone_from: editor
      capabilities:
        - view_products
        - read_products
        - build_products

  # Plugins you need for your project
  plugins: {}

  # Themes you need for your project
  themes: {} 
```

> 提示:如果您的项目使用 git，请确保排除 wp-content 文件夹。

## 更改字体设置

这就是 wordup 配置。现在你只需要编辑`frontity.settings.js`，这样 Frontity 就知道你的本地 WordPress API 在哪里了。

```
//... Other settings ...
  "packages": [
    // ...
    "name": "@frontity/wp-source",
        "state": {
        "source": {
            "api": "http://localhost:9000/wp-json"
        }
    }
  ]
//... Other settings ... 
```

## 本地运行项目

您的项目现在可以使用 wordup 了。使用:
启动您的本地 WP 服务器

```
$ wordup install 
```

几分钟后，你的 WordPress 安装应该运行了。为了使用 Frontity，你只需要在你的 WP 管理中改变永久链接结构。在*设置下>永久链接*设置永久链接为:*日期和名称*

现在，您也可以使用
启动 Frontity dev 服务器

```
$ npm run dev 
```

你现在应该在你的 Frontity React 应用中看到标准的 *Hello World* 帖子。

> 信息:如果您将 frontity 设置为超级用户(在 mac 或 linux 上)，可能会出现文件权限错误。只需将项目文件夹权限更改为当前用户即可。

## 高级功能

有了 wordup，你可以为你的 WordPress 安装创建固定装置。

这个功能帮助你拥有一些基本的 WordPress 文章、页面、类别、菜单等。为您的本地发展做好准备。参见[文字处理文档](https://docs.wordup.dev/config#fixtures)了解更多信息。