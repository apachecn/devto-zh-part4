# 我的命令行参考

> 原文：<https://dev.to/mrmartineau/my-command-line-reference-4d4b>

## 我最喜欢的 CLI 工具和我经常忘记的东西的混合体

声明:如果你不使用 [fish-shell](https://fishshell.com/) ，一些包可能无法工作。对于您自己的 shell，它们中的每一个都有替代方案。

## 键盘快捷键

### ctrl-r

搜索以前使用的命令

### ctrl-t

调用 *FZF* 模糊文件查找器(见下文)

### ctrl-l

清除终端屏幕

### ctrl-k

清除终端屏幕，但不返回。基本上抹去了

### ctrl-w

使用空格作为分隔符向后删除一个单词

### esc-t

交换光标前的最后两个单词

## 命令

### cal

显示日历

```
❯ cal  
   September 2019  
Su Mo Tu We Th Fr Sa  
 1  2  3  4  5  6 |7|  
 8  9 10 11 12 13 14  
15 16 17 18 19 20 21  
22 23 24 25 26 27 28  
29 30 
```

### 日期

显示日期

```
❯ date Sat  7 Sep 2019 09:22:28 BST 
```

### 树

将目录内容显示为一棵树

```
❯ tree -L 1  
.  
├── README.md  
├── components  
├── designsystem  
├── layouts  
├── next.config.js  
├── node_modules  
├── now.json  
├── package.json  
├── pages  
├── redirects.js  
├── scripts  
├── server.js  
├── static  
├── utils  
└── yarn.lock 
```

### 列出全球套餐

```
# with yarn 
❯ yarn global list  
yarn global v1.17.3  
info "canvas-sketch-cli@1.4.2" has binaries:  
   - canvas-sketch  
   - canvas-sketch-cli  
   - canvas-sketch-gif  
   - canvas-sketch-mp4  
info "create-react-app@2.1.2" has binaries:  
   - create-react-app  
info "docusaurus-init@1.0.1" has binaries:  
   - docusaurus-init  
info "emma-cli@2.0.1" has binaries:  
   - emma  
   - ema  
info "emoj@2.0.0" has binaries:  
   - emoj  
info "fx@10.0.0" has binaries:  
   - fx  
info "ibrew@1.1.1" has binaries:  
   - ibrew  
info "jscodeshift@0.6.2" has binaries:  
   - jscodeshift  
info "lighthouse@5.0.0" has binaries:  
   - lighthouse  
   - chrome-debug  
info "majestic@1.2.24" has binaries:  
   - majestic  
info "np@4.0.2" has binaries:  
   - np  
info "npm@6.0.0" has binaries:  
   - npm  
   - npx  
info "parcel-bundler@1.9.2" has binaries:  
   - parcel  
info "plop@2.1.0" has binaries:  
   - plop  
info "prettier@0.22.0" has binaries:  
   - prettier  
info "release-it@12.3.5" has binaries:  
   - release-it  
info "rimraf@2.6.3" has binaries:  
   - rimraf  
info "semver@5.6.0" has binaries:  
   - semver  
info "serve@10.0.2" has binaries:  
   - serve  
info "strapi@3.0.0-alpha.13.0.1" has binaries:  
   - strapi  
info "terser@3.8.2" has binaries:  
   - terser  
info "types-installer@1.4.0" has binaries:  
   - types-installer  
info "yalc@1.0.0-pre.27" has binaries:  
   - yalc  
✨  Done in 5.61s.  

# with npm 
❯ npm -g ls --depth=0 
```

## 自定义套餐

### 自动跳转

[https://github.com/wting/autojump](https://github.com/wting/autojump)

自动跳转是导航文件系统的一种更快的方式。它的工作原理是通过命令行维护一个包含您最常用的目录的数据库。

```
❯ j code 
```

### exa

[https://github.com/ogham/exa](https://github.com/ogham/exa)

`ls`的现代版

```
❯ exa -l  
❯ exa -T -L 2 # like tree 
```

### FZF

[https://github.com/junegunn/fzf](https://github.com/junegunn/fzf)

模糊查找文件。使用 ctrl+t 访问它

### 发球

[https://github.com/zeit/serve](https://github.com/zeit/serve)

静态文件服务和目录列表

### 艾玛

[https://github . com/maticzav/Emma-CLI](https://github.com/maticzav/emma-cli)

搜索 npm 包。运行 **emma** 初始化搜索，然后输入您的搜索标准

### ibrew

[https://github.com/mischah/ibrew](https://github.com/mischah/ibrew)

自制软件的交互式搜索 CLI

运行 ibrew 初始化搜索，然后输入您的搜索标准

### yalc

[https://github.com/whitecolor/yalc](https://github.com/whitecolor/yalc)

像老板一样在本地使用 yarn/npm 包。

### 枢纽

[https://hub.github.com/](https://hub.github.com/)

```
# clone one of your GitHub repos 
❯ hub clone dotfiles  
# Same as → git clone git://github.com/YOUR_USER/dotfiles.git 

# clone another project's repo 
❯ hub clone github/hub  
# → git clone git://github.com/github/hub.git 

# open the current project's issues page 
❯ hub browse -- issues  
# → open https://github.com/github/hub/issues 

# open another project's wiki 
❯ hub browse mojombo/jekyll wiki 
```

#### 列出当前回购的 PRs

```
# this is an alias 
alias prs 'hub pr list -L 20 -b develop --format="%t%n - Branch: [%H]%n - %U%n - %l%n%n"'  
# e.g. ❯ prs 
```

### 创建一个分支并切换到它

```
# FYI This is a fish shell function 
function branch  
   git branch {$argv} && git checkout {$argv}  
end  
# e.g. ❯ branch feature/WL-88-hello 
```

### 站位

[https://github.com/kamranahmedse/git-standup](https://github.com/kamranahmedse/git-standup)

显示过去 24 小时的提交列表

```
❯ standup  
/Users/zander/code/fairfx/fx-holiday-money-app  
5315f033 - build(multi): HM-925 Improve perf for a couple of pages (16 hours ago) <Zander Martineau>  
4b41c048 - index on feature/HM-925-improve-performance: 1b854d84 fix(ci): AWS-99 Remove builds/deployments associated (#845) (17 hours ago) <Zander Martineau> 
```

### 跨端口杀手

[https://github.com/milewski/cross-port-killer](https://github.com/milewski/cross-port-killer)

终止在特定端口上运行的进程

```
❯ kill-port 9001 
```

### doctoc

[https://github.com/thlorenz/doctoc](https://github.com/thlorenz/doctoc)

生成降价目录

```
❯ doctoc README.md --github 
```

### fx

[https://github.com/antonmedv/fx](https://github.com/antonmedv/fx)

命令行 JSON 处理工具

```
❯ fx package.json 
```

### jq

[https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)

jq 就像是 JSON 数据的`sed`——你可以用它来分割、过滤、映射和转换结构化数据，就像`sed`、`awk`、`grep`和朋友们让你玩文本一样容易。

```
# use jq to extract just the first commit from a repos list 
❯ curl 'https://api.github.com/repos/stedolan/jq/commits?per_page=5' | jq '.[0]' 
```

### npq

[https://github.com/lirantal/npq](https://github.com/lirantal/npq)

通过在安装过程中审计 npm 或 yarn 来安全地*安装包

```
# with npm 
❯ npq install express   

# with yarn, set an alias like so: 
alias npq="NPQ_PKG_MGR=yarn npq-hero"  
❯ npq add express 
```

### youtube-dl

[https://youtube-dl.org](https://youtube-dl.org/)

```
# install 
brew install youtube-dl  

# use 
❯ youtube-dl d2qfa3tlgH8 
```

* * *

我错过了什么吗？请在评论中告诉我