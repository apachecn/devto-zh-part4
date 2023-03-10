# 使用 python 进行文件管理

> 原文：<https://dev.to/marvinkweyu/file-management-with-python-2kp0>

我们大多数人都有过这样的经历，我们的机器里有大量杂乱无章的文件。它发生了。前一分钟，你正在打开一个大的 zip 文件，接下来的事情你知道，这些文件在目录中无处不在，与你所有的重要文件混在一起，随机放置，留给你的任务是手动排序什么需要去哪里。真的很痛苦。为了简化这个过程，我们将使用 python 智能地研究文件管理。

> 聪明地工作，而不是努力。

我们开始吧。我们将使用 python 3.4 或更高版本。
假设您已经启动并运行了 python，我们将带着`OS`模块和其他几个我们将在路上介绍的模块走一走。大多数都是 python 自带的，所以不需要安装其他任何东西。

### 创建随机文件

创建要使用的目录。称之为`ManageFiles`。在这个文件夹中创建另一个文件夹`RandomFiles`。您的目录结构现在应该看起来像这样:

```
ManageFiles/
 |
 |_RandomFiles/ 
```

Enter fullscreen mode Exit fullscreen mode

我们将在`RandomFiles`目录下创建随机文件
在`ManageFiles`目录下创建一个文件`create_random_files.py`。你现在有这个:

```
ManageFiles/
 |
 |_ create_random_files.py
 |_RandomFiles/ 
```

Enter fullscreen mode Exit fullscreen mode

完成了吗？现在进入下面的代码，我们一会儿将进入它的细节。

```
import os
from pathlib import Path
import random

list_of_extensions = ['.rst','.txt','.md','.docx','.odt','.html','.ppt','.doc']

# get into the RandomFiles directory
os.chdir('./RandomFiles')

for item in list_of_extensions:
    # create 20 random files for each file extension
    for num in range(20):
        # let the file begin with a random number between 1 to 50
        file_name = random.randint(1,50)
        file_to_create = str(file_name) + item
        Path(file_to_create).touch() 
```

Enter fullscreen mode Exit fullscreen mode

从 python 3.4 开始，我们有了`pathlib`，我们的小魔盒。我们还导入 python 的`random`函数来创建随机数；坚持这个想法，当我们到达使用它的那一行时，我们会讲到它。

首先，我们创建一个文件扩展名列表，从中我们可以获得随机文件。你可以随意补充。
接下来，我们转到`RandomFiles`目录，然后是我们的循环，开始吧。
我们只是简单地说，取这个`list_of_extensions`中的每一个项目，并对其进行以下操作。让我们以`.txt`为例。我们进入另一个循环，到这个`.txt`，我们对它做 20 次。

还记得我们进口的`random`吗？我们用它来为我们的文件选择一个 1 到 50 之间的随机数。简而言之，这个小循环所做的是为我们这些缺乏创造力的人(别担心，我也是这个团队的一员)节省命名随机文件的时间。我们将简单地创建一个文件，比如说`23.txt`或`14.txt`，只要它在我们的 50 到 20 倍的范围内。这只是为了制造一个足够大的混乱，当手动移动时会产生疼痛。其他扩展也将完成相同的过程。下一个？在你的终端上运行这个。

```
python create_random_files.py 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！我们现在有一个混乱的目录。现在来清理一下。
在我们的`create_random_files.py`所在的同一个位置，创建一个文件`clean_up.py`并获取下面的内容。

### 方法 1:

```
import os
import shutil
import glob

# get into the RandomFiles directory
os.chdir('./RandomFiles')

# get the list of files in the directory RandomFiles
files_to_group = []
for random_file in os.listdir('.'):
    files_to_group.append(random_file)

# get all the file extensions present
file_extensions = []
for our_file in files_to_group:
    file_extensions.append(os.path.splitext(our_file)[1])

print(set(file_extensions))

file_types = set(file_extensions)

for type in file_types:
    new_directory = type.replace(".", " ")
    os.mkdir(new_directory)  # create directory with given name

    for fname in glob.glob(f'*.{type[1:]}'):
        shutil.move(fname, new_directory) 
```

Enter fullscreen mode Exit fullscreen mode

为此，我们导入了两个新的库；`shutil`和`glob`。`shutil`将帮助我们移动文件，而`glob`将帮助我们找到要分类的文件。就像以前一样，当我们到达终点时，一切都会变得清晰。

首先，我们得到目录中所有文件的列表。

> 这里，我们假设我们不知道目录中有什么文件。这意味着不像你可以手动获得所有的扩展名并使用`if statements`或`switch`，我们希望程序浏览目录并为我们做这些。如果文件有许多扩展名或日志文件会怎样？你会手动这样做吗？

一旦我们获得了文件夹中所有文件的列表，我们就进入另一个循环，以获得这些文件的文件扩展名。注意我们如何使用:

```
os.path.splitext(our_file)[1] 
```

Enter fullscreen mode Exit fullscreen mode

目前，our_file 变量看起来像这样`5.docx`(例如)。当我们分割它时，我们得到这个:

```
`('5', '.docx')` 
```

Enter fullscreen mode Exit fullscreen mode

然后我们从它那里得到索引[1]，它又取`.docx`，因为`5`是索引[0]。
所以我们现在有了文件夹中所有文件扩展名的列表，不管是否重复。

为了不重复，我们做了一套。这将获取列表中的所有项目，并且只获取唯一的项目。在我们的例子中，如果我们有一个列表，其中有一个扩展，比如说`.docx`在集合中反复重复将确保我们只有其中的一个。

```
# create a set and assign it to a variable 
file_types = set(file_extensions) 
```

Enter fullscreen mode Exit fullscreen mode

记住我们的文件类型列表仍然有每个文件扩展名的`.`。这意味着如果我们要创建一个完全相同命名的文件夹，我们最终会创建隐藏的文件夹，这是我们不希望的。

因此，当我们遍历这个集合时，我们创建了一个具有相同扩展名的目录，只是这一次，我们用一个空字符串替换了名称中的`.`。

```
new_directory = type.replace(".", " ")
# our directory would now be called  'docx' 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要`.docx`扩展名来移动文件。

```
for fname in glob.glob(f'*.{type[1:]}') 
```

Enter fullscreen mode Exit fullscreen mode

这仅仅意味着获取任何以文件扩展名`.docx`结尾的文件(注意在`f'*.{type[1:]}'`中使用的空格)。**没有空间。**

通配符`*`意味着文件可以被命名为任何名称，只要它以`.docx`结尾。因为我们已经放置了句点`.`，所以我们获取我们拥有的字符串，并在之后获取所有其他内容，这就是为什么我们使用[1:]的原因，它只是意味着从第一个字符之后获取，因此获取`docx`。

接下来呢？将具有此扩展名的任何文件移动到名为 so 的目录中。

```
shutil.move(fname, new_directory) 
```

Enter fullscreen mode Exit fullscreen mode

这样，一旦为循环中找到的第一个文件创建了一个目录，就不能再复制其他文件了。简而言之，我们不会有一个文件夹来存储`5.docx`和许多其他的文件夹来存储`34.docx`等等。一旦我们有了一个目录，所有其他看起来像这样的文件夹都会移到那里。就是这样！

### 方法二

或者，您可以使用发电机。这是一种用一行代码创建列表的奇特方式。

```
import os
import shutil
import glob

# get into the RandomFiles directory
os.chdir('./RandomFiles')

#take every file from the directory and add to a list for all files
all_files = [x for x in os.listdir('.') ]

# make a set for the extensions present in the directory
file_types = set((os.path.splitext(f)[1] for f in all_files))

for ftype in file_types:
    new_directory = ftype.replace(".", '')
    os.mkdir(new_directory)

    for fname in glob.glob(f'*.{ftype[1:]}'):
        shutil.move(fname, new_directory) 
```

Enter fullscreen mode Exit fullscreen mode

这两个都可以。现在，您已经根据扩展名对所有文件进行了排序。

```
ManageFiles/
 |
 |_create_random_files.py
 |_RandomFiles/
    |_doc
    |_docx
    |_html
    |_md
    |_odt
    |_ppt 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。太多了。不过，我们确实节省了一些时间。有什么问题吗？请随意联系。暂时就这样了。下周我们会更上一层楼。