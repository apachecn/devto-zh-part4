# 我的 zsh 设置

> 原文：<https://dev.to/eddinn/my-zsh-setup-2gge>

[![](img/48bdcf8b84552e19041d266899f0663b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RnLjI8bK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.eddinn.nimg/1/b/f/a/9/1bfa9018e2e882683ea5ae08e0a4dcfbcf5d5aec-zsh.png)

我从使用`bash`切换到`zsh`作为我的主要 shell 环境，并且从未回头。你可以在 [GitHub](https://github.com/eddinn/zsh/) 上找到我的`.zshrc`配置文件和[我的 ZSH](https://github.com/robbyrussell/oh-my-zsh)

#### 主题

```
ZSH_THEME="agnoster" 
```

#### 我的。zshrc 插件列表

```
plugins=(
  git
  dotenv
  rake
  ruby
  ansible
  autoenv
  autopep8
  docker
  docker-compose
  docker-machine
  django
  git-prompt
  gnu-utils
  man
  nmap
  npm
  pip
  pipenv
  pyenv
  pylint
  python
  rsync
  sudo
  systemd
  ubuntu
  virtualenv
  vscode
) 
```