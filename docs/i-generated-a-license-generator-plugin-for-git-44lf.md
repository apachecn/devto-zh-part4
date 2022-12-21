# 我为 Git 生成了一个许可证生成器插件

> 原文：<https://dev.to/itachiuchiha/i-generated-a-license-generator-plugin-for-git-44lf>

你好。我为 Git 生成了一个许可证生成器插件。欢迎所有的贡献

你可以在 GitHub 上找到:[https://github.com/foss-dev/git-license-generator](https://github.com/foss-dev/git-license-generator)

## 安装

有两种方法可以安装这个插件

### 手动安装

```
git clone https://github.com/foss-dev/git-license-generator

cd git-license-generator

sudo mv git-licenses/* /usr/local/bin/
sudo mv git-generate-license /usr/local/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

### 直接安装

只需复制并粘贴这一行命令:

```
$ bash -c  "$(wget -qO- https://git.io/fj2J9)" 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果你是 Mac 用户:

```
$ bash -c  "$(curl -sLo- https://git.io/fj2J9)" 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

选择许可证

```
git generate-license -s 
```

Enter fullscreen mode Exit fullscreen mode