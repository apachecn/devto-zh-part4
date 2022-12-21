# 我是一只生锈的青蛙

> 原文：<https://dev.to/dmerejkowsky/i-am-a-rusty-frog-1op>

*原载于[我的博客](https://dmerej.info/blog/post/i-am-a-rusty-frog/)*

你知道青蛙和沸水的寓言吗？事情是这样的:

> 如果你把一只青蛙丢进一壶沸水中，它当然会疯狂地试图爬出来。但是如果你把它轻轻地放在一壶温水中，并把温度调得很低，它会很平静地漂浮在那里。随着水逐渐升温，青蛙会沉入宁静的昏迷状态，就像我们中的一个人在洗热水澡一样，不久，它会面带微笑，毫无抵抗地让自己被煮死。

至少丹尼尔·奎因在《B 的故事》中是这么说的。

这是一个强有力的比喻，尽管如果你读了维基百科的文章，你会发现青蛙实际上并没有那样做。

但我认为还是有一些道理的:如果你逐渐经历了越来越多的痛苦，你可能意识不到你实际上承受了多少，直到为时已晚。至少我就是这样。

让我们从头开始，在我们开始谈论 Rust、重构、性能错误和人类思维的弱点之前。

# 第一部分:设置

很久以前，我意识到我花了太多的时间键入`cd`来在项目目录间导航，并且默认的`zsh`的`ctrl-r`绑定对我来说有点笨拙。

然后我意识到我可以通过编写两个非常相似的命令来解决这两个问题:`cwd-history`和`cmd-history`。

先说前者。它是一个命令行工具，有两个动作，`add`和`list`。它是这样工作的:

*   `cmd-history add`获取一个参数，并将其添加到`~/.local/share/dm-tools/commands-history`中的“数据库”中。数据库只是一个每行一个命令的文件。
*   `cmd-history list`简单地将数据库的内容转储到`stdout`。
*   然后，有一个`zsh`钩子在一个命令被读取并即将被执行后运行。在钩子中，`cmd-history add`用最后一个命令的文本调用。
*   最后，`zsh`被配置为当`ctrl-r`被按下时，`cmd-history list`的输出通过`fzf`传输，然后运行选择的命令。

我使用一个叫做`cwd-history`的类似程序，除了工作目录之外，它做完全相同的事情。它运行在另一个名为`chpwd`的 zsh 钩子中，通过键入命令`z` <sup id="fnref1">[1](#fn1)</sup> 来触发

有几个细节，比如确保条目不重复，或者确保添加的路径中的每个符号链接都被解析，但是我们将在后面讨论这些。

# 第二部分:RIR

这两个程序是用 Python 写的，共享了大量的功能，但是因为我是一个懒惰的程序员，所以我没有费心去重构代码。

但是有件事困扰着我。每当我使用它们时，都会有一点明显的延迟，尤其是在旧的硬件上。所以我决定尝试用 Rust 重写它们。当然，当我测量性能时，启动时间有巨大的差异:Rust 直接编译成机器代码，不需要等待 Python 解释器启动。我很开心。

# 第三部分:切换到 Kakoune

几个月前，我的主文本编辑器从 Neovim 切换到了 [Kakoune](https://kakoune.org/) ，遇到了一个问题。

Kakoune 在设计上没有任何持久状态，我缺少一个特殊的 vim 特性，它允许我快速选择任何最近打开的文件(又名。 <abbr title="Most Recently Used">MRU</abbr> 文件)。

但是我已经知道如何解决这个问题了！我编写了另一个名为`mru-files`的工具，用于读写另一个数据库中的 MRU 文件，并编写了一些 Kakoune 脚本:

```
# Call `mru-files add` with the buffer name when opening a new buffer
hook global BufOpenFile .* %{ nop %sh{ mru-files add "${kak_hook_param}" } }

# Call `mru files list` when pressing <leader key>o:
map global user o
  -docstring 'open old files'
  ':evaluate-commands %sh{ mru-files list --kakoune }<ret>' 
```

Enter fullscreen mode Exit fullscreen mode

注意调用`mru-files list`时的`--kakoune`选项:我们需要调用`menu`命令，这样我们就可以向用户显示 MRU 文件的列表，并在文件被选中时打开它。

看起来是这样的:

```
menu
  "/foo.txt" "edit -existing 'foo.txt:'"
  "/bar.txt" "edit -existing 'bar.txt'"
  ... 
```

Enter fullscreen mode Exit fullscreen mode

当我这样做的时候，我还为`cwd-history list`添加了一个`--kakoune`参数，这样我就可以直接从 Kakoune:
切换到以前访问过的目录

```
map global cd o
   -docstring 'open old working directory'
   ':evaluate-commands %sh{ cwd-history list --kakoune }<ret>' 
```

Enter fullscreen mode Exit fullscreen mode

# 第四部分:重构

所以现在我面临着用 Rust 写的三个非常相似的代码。是时候进行一次重构了 <sup id="fnref2">[2](#fn2)</sup>

我是这样做的:

*   首先，我添加了一个名为`EntryCollection`的特征，其中包含名为`add`和`list`的方法

```
pub trait EntriesCollection {
    fn add(&mut self, entry: &str);
    fn list(&self) -> &Vec<String>;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   该特征由三个结构实现:`Commands`、`WorkingDirs`和`MruFiles`。

```
pub struct Commands {
    entries: Vec<String>,
}

impl EntriesCollection for Commands {
  // ...
  fn add(&mut self, entry: &str) {
      // Deduplicate the entry and store it in self.entries
      // ...
  }

  fn list(&self) -> Vec<String> {
      // return self.entries
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
pub struct WorkingDirs{
    entries: Vec<String>,
}

impl EntriesCollection for WorkingDirs {
   // ...
   fn add(&mut self, entry: &str) {
         // Convert the entry to a path, check if it exists,
         // then canonicalize it and insert it in self.entries
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

(以及 MruFiles 的类似代码)

*   我编写了一个`Storage`结构来与数据库交互:

```
impl Storage {
    pub fn new(
        mut entries_collection: Box<EntriesCollection>,
        path: &std::path::PathBuf,
    ) -> Storage {
        let db_path = path.join(entries_collection.name());
        let entries = read_db(&db_path);
        for entry in entries {
            entries_collection.add(&entry);
        }
        Storage {
            db_path,
            entries_collection,
        }
    }

    pub fn list(&self) -> &Vec<String> {
        &self.entries_collection.list()
    }

    pub fn add(&mut self, entry: &str) {
        &mut self.entries_collection.add(&entry);
        write_db(&self.db_path, &self.list())
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   我编写了`StorageManager` struct 来实例化`Storage` struct，并根据`StorageType`枚举使用正确的`EntriesCollection`实现:

```
pub enum StorageType {
    CwdHistory,
    CommandsHistory,
    FilesHistory,
}

pub struct StorageManager {
   // ...
}

impl StorageManager {
    pub fn new(storage_type: StorageType) -> Self {
            let entries: Box<EntriesCollection> = match storage_type {
                StorageType::CwdHistory => Box::new(WorkingDirs::new()),
                StorageType::CommandsHistory => Box::new(Commands::new()),
                StorageType::FilesHistory => Box::new(MruFiles::new()),
            };

        };

        let storage = Storage::new(entries, &app_dir);
        StorageManager { storage }
    }

    pub fn list(&self) {
        // Delegates to self.storage.list()
    }

    pub fn add(&mut self, entry: &str) {
        // Delegates to self.storage.add()
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

*   最后，我编写了三个调用`StorageManager`构造函数的`main`函数，并向其传递 enum 变量。

```
// In src/bin/cmd-history.rs
use dm_tools::StorageType;
fn main() {
    // ...
    dm_tools::run_storage_manager(StorageType::CommandsHistory)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// In src/bin/cwd-history.rs
use dm_tools::StorageType;
fn main() {
    // ...
    dm_tools::run_storage_manager(StorageType::CwdHistory)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// In src/bin/mru-files.rs.rs
use dm_tools::StorageType;
fn main() {
    // ...
    dm_tools::run_storage_manager(StorageType::FilesHistory)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我的第一个错误:我忘记了在重构之后测量性能。我*确信*代码是正确的。毕竟，如果它能编译，它就能工作，对吗？

果然，代码*成功了*。我可以从 Kakoune 打开 MRU 文件和旧的项目目录，我对自己很满意。

当然，现在您应该已经猜到上面的代码中有一个可怕的性能错误。你发现了吗？如果你做到了，恭喜你！我当时当然不知道。

# 第五部分:对等编程会话

重构发生在 6 个月前。我在工作中使用这些工具，这意味着我在 6 个月内，每周 5 天使用这些命令。数据库文件包含数以千计的条目。可怕的 bug 仍然存在，毫无顾忌地消耗 CPU 周期，我没有注意到任何事情。

幸运的是，我和我的一个同事进行了一次对等编程会议，奇怪的事情发生了。当我使用`z`和`ctrl-r`快捷键的时候，看着我的 zsh 提示符被卡住，这让*感到厌烦。*

然后我再次测量了我的工具的性能，我发现运行`cmd-history list`命令花费了 1.7 **秒**。那是*一千七百*毫秒。这是一个很长的时间。难怪我的同事会觉得无聊！然而令人惊奇的是，直到他告诉我，我才*而不是*注意到有什么不对劲。

# 第六部分:修复

现在是时候消灭臭虫了。罪魁祸首就在这里，在`Storage`构造函数中:

```
impl Storage {
    pub fn new(
        mut entries_collection: Box<EntriesCollection>,
        path: &std::path::PathBuf,
    ) -> Storage {
        let db_path = path.join(entries_collection.name());
        let entries = read_db(&db_path);
        for entry in entries {
            entries_collection.add(&entry);
        }
        Storage {
            db_path,
            entries_collection,
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在读取数据库文件的每一行，并将其传递给`EntriesCollection.add()`。这意味着我们一遍又一遍地调用`add()`方法。它不会做太多事情，但在执行重复数据删除时，它仍然需要通过*所有*条目。这是 [Shlemiel 算法](https://www.joelonsoftware.com/2001/12/11/back-to-basics/)的一个经典案例，它解释了当数据库变得足够大时该工具的糟糕性能。

我相信我这样写代码是因为我认为在读取数据库时以某种方式“验证”条目会很好。这样，如果`add`方法中的算法改变了，数据库将自动迁移。另一个名为*<abbr title="You Ain't Gonna Need It"></abbr>*的经典错误:我怀疑我是否需要迁移数据库，当我需要时，我可能只需要编写一个很小的一次性脚本来完成。

无论如何，既然我们已经决定“自动迁移”特性可以取消，我们可以通过向 trait 添加一个`add_all`方法，并替换`Storage`构造函数中的`for`循环:
来解决我们的性能问题

```
pub trait EntriesCollection {
     fn add(&mut self, entry: &str);
+ fn add_all(&mut self, entres: Vec<String>);
     fn list(&self) -> &Vec<String>;
}

impl Storage {
    pub fn new(
        mut entries_collection: Box<EntriesCollection>,
        path: &std::path::PathBuf,
    ) -> Storage {
        let db_path = path.join(entries_collection.name());
        let entries = read_db(&db_path);
-       for entry in entries {
-           entries_collection.add(&entry);
-       }
+ entries_collection.add_all(entres);
        Storage {
            db_path,
            entries_collection,
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

反过来，这意味着我们需要在所有结构上实现`add_all()`方法:

```
impl EntriesCollection for Commands {
    // ...
    fn add_all(&mut self, entries: Vec<String>) {
        self.entries = entries;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
impl EntriesCollection for WorkingDirs {
    // ...
    fn add_all(&mut self, entries: Vec<String>) {
        self.entries = entries;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
impl EntriesCollection for MruFiles {
    // ...
    fn add_all(&mut self, entries: Vec<String>) {
        self.entries = entries;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 一个小题外话

想必你已经注意到了三个结构中的`add_all()`和*的实现是完全相同的。为什么我们没有在 trait 中添加`add_all()`方法的一揽子实现？*

因为我们需要访问结构体的`entries`字段，而在 Rust 中没有办法做到这一点。既然我爱上了 Rust，我可以用几种方式来解释这一点:

*   首先，代码重复不是问题，因为只有一行代码。
*   第二，重复是*附带的*。我们可以想象使用一个`Set`而不是一个`Vec`来存储条目，并且特征不需要知道这一点。如果我们决定重命名该字段，也是如此。
*   第三，我可以告诉自己，这种缺乏的特性是一件好事 <sup id="fnref3">[3](#fn3)</sup> ，因为我经常在其他语言中通过继承共享类成员(C++和 Python 都是如此)。

你想怎么说就怎么说:我可以告诉自己这些都是很好的论点，但你不必相信我:)

# 这个故事的寓意

以下是我的建议:

*   当性能很重要时，不要忘记不断地度量它，尤其是在重构之后(或者甚至在每次提交之后，如果你能负担得起的话)
*   沸腾的青蛙这个比喻其实很好，你可能在一些沸水中而没有意识到。
*   最后，当人们告诉你生活中的一些不好的事情时，听听他们对你说了什么。在过去的 6 个月里，我确实感觉到我的机器出了点问题，但是需要另一个人来帮助我后退一步，了解出了什么问题。

保重，下次再见:)

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  那是因为在我决定自己用[重新实现](https://dmerej.info/blog/post/rewriting-z-from-scratch/)之前，我一直在使用一个名为`z`的工具。 [↩](#fnref1)

2.  如果你想知道为什么，去看看编码恐怖博客上的 *[三](https://blog.codinghorror.com/rule-of-three/)* 法则。 [↩](#fnref2)

3.  顺便说一句，并不是所有的 Rust 用户都这么想。你可以在 GitHub 上找到一个关于使用 traits [中字段的 RFC。](https://github.com/nikomatsakis/fields-in-traits-rfc) [↩](#fnref3)