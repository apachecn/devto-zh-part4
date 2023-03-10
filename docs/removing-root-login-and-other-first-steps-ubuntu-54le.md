# 移除 Root 登录和其他第一步

> 原文：<https://dev.to/davidemily/removing-root-login-and-other-first-steps-ubuntu-54le>

# 移除 Root 登录

就在最近，我创建了一个全新的 [DigitalOcean](http://digitalocean.com/) 来玩一个附带项目。我认为管理一个远程的 Ubuntu 服务器可能有点让人不知所措，所以我想通过一些步骤来建立一个安全的远程环境。

### 连接到您的服务器

DigitalOcean 将提示您选择 SSH 密钥或一次性密码。对于这个例子，我选择使用一次性密码，尽管我建议设置 SSH 密钥，可以使用 PuttyGen 创建。对于 Windows 来说，事情有点复杂，但是我建议使用 Putty，它可以在找到[，或者如果你使用 Chocolatey 包管理器的话，可以在`choco install putty`找到。很快您就会收到来自 DigitalOcean 的电子邮件，其中包含您的新服务器的名称、ip 地址和一次性密码。您需要在 Putty 的“主机名(或 IP 地址)”框中输入 IP 地址，验证“端口”框是否设置为 22，然后点击“打开”。如果你不知道你要连接的是什么，你应该会收到一个关于这不安全的警告，但是请点击继续。](https://www.putty.org/)

### [服务器上的](#on-the-server)

所以现在你加入了！你应该有一个 CMD 外观的窗口要求你登录。对于“登录身份”,输入`root`,并按回车键。它现在会提示您输入密码，该密码是通过电子邮件发送给您的一次性密码。输入密码后，它会提示您更改密码。无论你选择什么作为根密码，都要尽量放在安全的地方。虽然您很快就不需要以 root 用户身份登录，但是拥有密码将会省去您以后可能遇到的麻烦。

#### 更新服务器

重置密码后的第一件事是确保您的服务器是最新的。这可以通过输入`sudo apt update && sudo apt upgrade`使用 Ubuntu 自带的包管理器轻松完成。这将贯穿你的程序，并确保你有最新的版本。

#### UFW 防火墙设置

接下来我会安装一个防火墙来保护你的服务器安全。我建议在你的服务器上安装[‘未完成防火墙’](https://help.ubuntu.com/community/UFW)，或者 UFW。这可以通过键入`sudo apt-get install ufw`来完成，尽管大多数 DigitalOcean droplets 都是预装的。现在选择您想要启用和禁用的端口。您将希望确保 SSH 端口(您如何连接到服务器)是启用的，所以我建议从`sudo ufw allow ssh`命令开始。下一步取决于您的服务器正在运行什么服务。如果你想把它变成一个网络服务器，你可能还想运行`sudo ufw allow 80/tcp`和`sudo ufw allow 443/tcp`。现在，通过键入`sudo ufw enable`启动 UFW 服务，当它询问您是否要继续时，键入 yes。现在服务已经启用，您可以键入`sudo ufw status`来查看服务正在运行以及它允许哪些端口。如果以后您想添加更多功能，这将非常有用。

#### 失败 2 班

启用防火墙后，我会安装 [Fail2Ban](https://www.fail2ban.org/wiki/index.php/Main_Page) ，这是一种阻止用户强行进入你的服务器的服务。这可以通过输入`sudo apt-get install fail2ban`并在提示符下点击 yes 来完成。Fail2Ban 有额外的功能，例如当可疑活动发生时发送电子邮件地址，以及查看哪些 IP 地址被禁止的能力，所以请随意阅读 [Fail2Ban Wiki](https://www.fail2ban.org/wiki/index.php/Main_Page) 以获得更多选项。

#### 创建账户

接下来，让我们创建一个新帐户，这样您就不再需要在 root 下做任何事情。首先使用“useradd”命令，键入`useradd username`，用户名是您想要创建的帐户名称。接下来，通过键入`passwd username`更改该帐户的密码，其中 username 是您刚刚创建的帐户名称。现在有了一个新帐户，让我们通过输入用户名为帐户名称的`usermod -a -G sudo username`赋予它使用 sudo 命令的能力。

#### 移除 Root 登录

你快完成了！现在我们已经有了防火墙、安全系统和新用户，让我们删除通过 root 远程 SSH 的功能。输入`vi /etc/ssh/sshd_config`打开服务器全系统配置文件。现在，您需要将“PermitRootLogin yes”改为“PermitRootLogin no ”,并在下面添加一行“AllowUsers username ”,其中 username 是您创建的帐户名称。这可以通过使用箭头键导航到该行，按“I”键进入插入模式，删除“yes”，键入“no”，按“enter”键，键入`AllowUsers username`其中用户名是您创建的帐户名，按“Q”键退出插入模式，按“ESC”键盘键，然后输入`:wq`保存您的更改并退出文件。

#### 重启

关键时刻到了。键入`service ssh restart`来重启 SSH 服务，或者只键入`sudo reboot`来重启服务器。现在打开另一个 Putty 窗口，尝试使用 root 连接..你应该被拒绝！现在尝试使用你的新帐户，你应该可以登录。

### 你做到了！

虽然这肯定不是你应该安装的安全功能的终点，但这是启动新的 DigitalOcean droplet 或任何远程 Ubuntu 服务器时的一个很好的基线。