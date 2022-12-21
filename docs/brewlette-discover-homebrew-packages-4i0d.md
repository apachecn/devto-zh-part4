# Brewlette -发现自制软件包

> 原文：<https://dev.to/garettmd/brewlette-discover-homebrew-packages-4i0d>

我有点强迫症的保持包更新，也许是一个错误。这种痴迷的一个结果是，我每天早上都在我的 Mac 上运行 brew update。这个命令会更新包的列表，甚至是您不使用的包，所以在运行它之后，您会看到所有种类的包的列表。如果我不赶时间，我通常会发现 1 或 2 个听起来有趣的包，并可能最终安装它们。在这样做了一段时间后，我决定如果我能发出一个命令并随意获得一个随机包，那将会很酷。事实证明这其实很容易做到。

事不宜迟，我来介绍:brewlette！

```
$ brewlette

Try out a random homebrew package!

flvmeta: stable 1.2.1 (bottled), HEAD
Manipulate Adobe flash video files (FLV)
https://www.flvmeta.com/
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/flvmeta.rb
==> Dependencies
Build: cmake ✔
==> Options
--HEAD
    Install HEAD version

Command "brew install flvmeta" copied to clipboard 
```

Enter fullscreen mode Exit fullscreen mode

真的，只是我放在/usr/local/bin(brew 平时安装包的地方)的一个简单的 shell 脚本。它的工作原理是从 brew 缓存的配方列表中随机选择一个包。我做的一个小的漂亮的添加是，它复制 brew 命令来安装软件包到你的剪贴板。只去⌘+v，享受新套餐。目前，该脚本在 Github gist 上。想投稿就随便评论/叉。脚本如下:

```
#!/usr/bin/env bash
# Utility to suggest a new package to try from Homebrew
# Simply run and it will give you a new package to try out

files=(/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core/Formula/*)
total=${#files[@]}
rando=$((RANDOM % total))
package=$(basename "${files[rando]}" .rb)
printf "\\nTry out a random homebrew package!\\n\\n"
brew info "${package}"

if [[ $(command -v pbcopy) ]]
then echo "brew install ${package}" | pbcopy
    printf "\\nCommand \"brew install %s\" copied to clipboard\\n" "$package"
else printf "\\nUse \"brew install %s\" to try it out\\n" "$package"
fi 
```

Enter fullscreen mode Exit fullscreen mode