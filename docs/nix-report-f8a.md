# nix 报告

> 原文：<https://dev.to/dannypsnl/nix-report-f8a>

本文旨在记录用户对 nix 的使用反馈，同时也帮助用户学习如何在日常生活中使用 nix。请记住，我不会深究 nix 内部的实现或模型概念，而是讨论如何使用它来改进开发环境。

### 安装

要安装 nix，你所要做的就是在 Linux 或 macOS 上运行:`sh <(curl https://nixos.org/nix/install) --no-daemon`并遵循脚本安装过程结束后脚本所提供的内容。

为了避免过时的信息，这里有[单用户安装](https://nixos.org/nix/manual/#sect-single-user-installation)(你在上面做的)，和[多用户安装](https://nixos.org/nix/manual/#sect-multi-user-installation)。

### 简介

安装后，您会得到几个用于不同目的的不同命令。我会一个一个地介绍它们(只针对我正在使用的那些)。

#### nix-env

`nix-env`就像`brew`对于 MacOS，`apt-get`对于 Ubuntu，`yum`对于 CentOS，但是对于所有带有 nix 的平台。这个命令是第一个可以立即提供帮助的命令。你可以做:`nix-env -i go`或`nix-env --install go`，之后让我们通过`which go`检查二进制`go`，位于`$HOME/.nix-profile/bin/go`。你也可以使用不同的通道(包源)来进行自部署包或者 nix 通道没有提供的任何其他通道。

使用`nix-env --help`获取更多信息

#### nix-shell

`nix-shell`将读取`shell.nix`或回退以读取`default.nix`这些配置文件。为了理解我们在其中做了什么，我们需要一个例子:

```
let
  pkgs = import <nixpkgs> { };
  inherit (pkgs) mkShell;
  inherit (pkgs) haskellPackages;
  inherit (haskellPackages) cabal-install;
  inherit (haskellPackages) stylish-haskell;

  ghc = haskellPackages.ghcWithPackages (pkgs: with pkgs; [base mtl]);
in
mkShell {
  buildInputs = [
    ghc
    cabal-install
    stylish-haskell
  ];
} 
```

`mkShell`会通过它的参数集`{}`返回一个 shell，在这里我们可以看看`stylish-haskell`这个 Haskell 格式化程序，在`nix-shell`环境中`which stylish-haskell`会得到`/nix/store/qqj9ldclapfbxhnvb357mjy5d5rjg6ip-stylish-haskell-0.9.2.2/bin/stylish-haskell`，如果你退出环境应该不会有二进制。

#### nix-build

现在，我们已经介绍了全局级安装程序和项目级安装程序。我们必须研究如何创建自己的包。

`nix-build`会读作`default.nix`来使用它的值作为你的包。我们来看看这是什么意思。

```
let
  pkgs = import <nixpkgs> { };

  dependencies = import ./deps.nix;
in
  pkgs.haskellPackages.callPackage ./little-scheme.nix { dependencies=dependencies; } 
```

忽略`deps.nix`，这是一个包含我使用的所有 Haskell 依赖项的列表。

通过它的参数创建一个 haskell 包。让我们深入了解一下`little-scheme.nix`是做什么的:

```
{ mkDerivation, base, mtl, dependencies, stdenv }:
mkDerivation {
  pname = "little-scheme";
  version = "0.1.0";
  src = stdenv.lib.sourceFilesBySuffices ./. [".hs" ".cabal" "LICENSE"];
  isLibrary = false;
  isExecutable = true;
  executableHaskellDepends = [base mtl] ++ dependencies;
  license = stdenv.lib.licenses.mit;
} 
```

`src`设置为`stdenv.lib.sourceFilesBySuffices ./. [".hs" ".cabal" "LICENSE"]`，这条线表示只有`.hs`、`.cabal`和`LICENSE`会领导新的构建。

会接受一个 Haskell 库的列表。

`{}: expression`是一个函数。`{}`使用模式匹配从输入集中提取值。`a: c`是取`a`返回`c`，`a: b: c`是取`a`返回`b: c`，考虑阅读[λ演算](https://en.wikipedia.org/wiki/Lambda_calculus)了解 nix 中的函数。

### 高级用例

#### direnv

是一个强大的 shell 环境扩展，它根据当前目录加载或卸载环境。要安装它，我们可以执行`nix-env --install direnv`，然后在`$HOME/.zshrc`的末尾添加`eval "$(direnv hook zsh)"`(其他 shells 的为[)。](https://github.com/direnv/direnv/blob/master/docs/hook.md)

`direnv`支持使用 nix 作为环境配置，你可以将`use_nix`放在`$dir/.envrc`中，它会观察`$dir/shell.nix`和`$dir/default.nix`来更新环境。但是目前的版本`use_nix`有缓存缺失的问题，所以你只需要从[https://github . com/kalbasit/Nur-packages/blob/master/pkgs/nix ify/env RC](https://github.com/kalbasit/nur-packages/blob/master/pkgs/nixify/envrc)中复制整个内容，把`use_nix -s shell.nix`放在`$dir/.envrc`中。

附注:一个小问题是`direnv`不能和`alias`一起工作，所以可能还是得用`$HOME/.zshrc`来管理他们。