# 实现一个开源的备份恢复系统。

> 原文：<https://dev.to/matteoguadrini/implement-an-open-source-backup-restore-system-4cpo>

### [T1】简介](#intro)

有多种开源备份/还原解决方案，但几乎所有都采用代理/服务器方法。

这种方法的缺点是客户端和服务器端都存在硬件资源的消耗。

rsync 是一个非常简单的工具，有许多非常有用的选项。遗憾的是，该实用程序不提供备份/恢复方法，只提供从 A 点到 b 点的同步。

但是，可以在其上实现一个稍微复杂一些的逻辑，以便可以管理多个备份，保持某种历史记录。在这一点上，rsync 成为更复杂的东西的核心。

[Butterfly Backup](https://matteoguadrini.github.io/Butterfly-Backup/) 是一个 rsync 包装器，增加了它的容量，成为一个真正的备份/恢复/存档工具。 *Butterfly Backup 是 rsync 的一个简单命令行包装器，用于复杂任务，用 python 编写。*

只需几个简单的步骤，无需存储库配置、特定设置和第三方软件或插件，您就可以实现自动企业备份/恢复/归档系统。

### 部署

比如？分四步走:

**1)安装**

```
git clone https://github.com/MatteoGuadrini/Butterfly-Backup.git
cd Butterfly-Backup
sudo python3 setup.py 
```

**2)编辑备份中的计算机/服务器列表**

```
cat << EOF > /mnt/san/bck_list
server01.local
server02
10.0.0.1
192.168.2.42 EOF 
```

**3)部署配置 SSH 密钥**

```
bb config --new
bb config --deploy server01.local
bb config --deploy server02
bb config --deploy 10.0.0.1
bb config --deploy 192.168.2.42 
```

**4) SETUP CRONTAB**

```
CMDBB="bb backup --list /mnt/san/bck_list --destination /mnt/san --data User Config --type Unix"
echo "00 12 * * * $CMDBB" | crontab - 
```

*就这样！*

在 5 分钟内，您已经实现了一个自动备份系统，每天中午 12:00 保存四台服务器的用户文件夹和配置文件夹(`/etc`)。

可以在一个易于阅读和查阅的目录中查阅所有执行的备份，方法如下:

```
bb list --catalog /mnt/san 
```

### 结论

有时候最简单的解决方案是最好的选择。

有关此工具的更多信息，请访问以下链接:

网址:[https://matteoguadrini.github.io/Butterfly-Backup/](https://matteoguadrini.github.io/Butterfly-Backup/)GITHUB:[https://github.com/MatteoGuadrini/Butterfly-Backup](https://github.com/MatteoGuadrini/Butterfly-Backup)T5】DOCS:[https://butterfly-backup.readthedocs.io/en/latest/](https://butterfly-backup.readthedocs.io/en/latest/)