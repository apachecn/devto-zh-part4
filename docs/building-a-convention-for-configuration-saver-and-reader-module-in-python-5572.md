# 在 Python 中构建配置保存器和读取器模块的约定

> 原文：<https://dev.to/prahladyeri/building-a-convention-for-configuration-saver-and-reader-module-in-python-5572>

*(最初发表于[prahladyeri.com](https://prahladyeri.com/blog/2019/06/building-a-convention-for-configuration-saver-and-reader-module-in-python.html))*

我在 github 上维护了几个 [python 项目](https://github.com/prahladyeri)，其中一些项目如 [VTScan](https://github.com/prahladyeri/vtscan) 需要用户配置。现在 python 有了过多的[方法和标准](https://martin-thoma.com/configuration-files-in-python/)，用于*解析配置文件*，比如 json，*。ini 文件等。，但是没有关于在用户机器上安装后将它们保存在哪里的标准。

许多程序员使用的一种方法是将其保存到“应用程序路径”或 python 包本身的安装位置。在 windows 机器上，恰好是这样的:

```
C:\Users\<username>\AppData\Local\Programs\Python\Python36-32\Lib\distutils\<package_name> 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有几个问题。首先，它是存储程序的位置，不应该与数据或配置混在一起。其次，如果您的用户在将来卸载您的程序或者甚至升级它(这是一种非常常见的情况)，整个位置将会随着您的用户的配置数据一起消失！当您像这样升级软件包时，pip 首先删除旧的软件包，然后开始安装新的软件包:

```
pip install --upgrade package_name 
```

Enter fullscreen mode Exit fullscreen mode

linux 为用户存储配置文件的标准方式是~/。app-name 甚至~/。config/app-name，其中波浪号(~)表示用户的主目录(通常扩展为/home/username/)。这是我最喜欢的方式，因为它对应用程序和人类都是可读和可访问的！linux 上也有/etc/app-name 位置，但那是针对以 root 权限安装的应用程序，但作为典型的 python 开发人员，我们不需要担心这一点。如果你的应用需要 root，很可能你不会首先使用 pip/pypi，而是选择一个合适的 linux 打包系统，比如 apt/dnf/pacman。

但是在安装时通过 setup.py 将配置文件存储到那个位置并不容易！事实上，您必须从 setuptools.command.install 导入 install 类，并对其进行子类化，以覆盖安装后过程(如果您希望这样做的话)!

```
from setuptools.command.install import install

class PostInstallCommand(install):
    """Post-installation for installation mode."""
    def run(self):
        install.run(self)
        fpath = os.path.join(self.install_lib, pkg_name)
        fpath = os.path.join(fpath, "cfg.json")
        cfg_dir = os.path.join(os.path.expanduser("~"), ".config/%s" % pkg_name)
        if not os.path.isdir(cfg_dir): os.makedirs(cfg_dir)
        tpath = os.path.join(cfg_dir, "cfg.json")
        shutil.move(fpath, tpath) 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可以创建某些约定，比如为您的所有配置保留一个标准名称(比如 cfg.json ),并将它们存储到一个标准位置，比如~/。就像上面的代码一样。

另一件要注意的事情是，将这个配置文件包含在你的安装文件中的方法是将它包含在 MANIFEST.in 中，并且它不会从应用程序的源目录之外读取任何东西！不能给它分配像~/这样的路径。config/pkg_name/cfg.json，所以您必须手动将 cfg.json 复制到您的源目录，以便构建您的包。然后可以在 MANIFEST.in 中这样设置[:](https://github.com/prahladyeri/gar-cron/blob/master/MANIFEST.in)

```
include gar_cron/cfg.json 
```

Enter fullscreen mode Exit fullscreen mode

当然，我不能自动化整个事情，并把它变成一个完全“即插即用”的库模块，因为像上面这样的事情是特定于应用程序的，每个开发人员都必须为她的设置过程专门做这件事。但是我还可以做其他事情，比如从这个标准位置保存和检索数据:

```
def save(pkg_name, cfgobject, cfgpath=None):
    if cfgpath == None:
        cfgpath = os.path.expanduser("~/.config/%s" % pkg_name)
    if not os.path.isdir(cfgpath):
        os.makedirs(cfgpath)
    cfgpath = os.path.join(cfgpath,  "cfg.json")
    ss = json.dumps(cfgobject)
    open(cfgpath, 'w').write(ss)
    return True

def get(pkg_name , cfgpath=None):
    if cfgpath == None:
        cfgpath = os.path.expanduser("~/.config/%s" % pkg_name)
    if not os.path.isdir(cfgpath):
        os.makedirs(cfgpath)
        return None
    cfgpath = os.path.join(cfgpath,  "cfg.json")
    if not os.path.isfile(cfgpath):
        return None
    ss = open(cfgpath).read()
    return json.loads(ss) 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数允许您将配置对象(通常是 python 字典)保存到这个标准位置，方法是将它序列化为 json 并写入 cfg.json。您还可以覆盖 cfgpath 参数以将其存储在非标准位置，但是除非有任何特殊原因，否则不应该这样做。

第二个函数同样通过读取和反序列化 cfg.json 文件来获取配置数据。最后，我还编写了一个 get_from_cmd()函数，该函数有助于通过命令行直接从用户那里获取配置，方法是向它传递一个预定义的配置键列表:

```
def get_from_cmd(pkg_name, keys):
    print("Configuration Saver version %s\n" % cfgsaver.__version__)
    obj = {}
    for key in keys:
        try: 
            obj[key] = input("Enter %s: " % key)
        except KeyboardInterrupt as ex:
            return None
    save(pkg_name, obj)
    return obj

config_keys = ['github_username', 'alert_email']
config = cfgsaver.get_from_cmd(pkg_name, config_keys) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想马上使用整个功能，我写了一个名为 [cfgsaver](https://github.com/prahladyeri/cfgsaver) 的库，你可以安装并开始使用！

```
pip install cfgsaver 
```

Enter fullscreen mode Exit fullscreen mode

项目自述文件也有针对特定问题的特殊说明——设置您的 MANIFEST.in 和定制 setup.py 脚本。您也可以派生或复制这个库，并创建自己的版本来适应您自己的构建过程。

最后，您还可以继续使用非标准的方法来处理配置文件，但是这种方法充满了痛苦和麻烦。此外，如果每个开发人员都开始遵循保存配置文件的标准方式，这个世界将会变得更加美好，对人类和应用程序都是如此！