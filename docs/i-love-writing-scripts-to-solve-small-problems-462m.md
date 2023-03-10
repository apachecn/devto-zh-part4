# 我喜欢写脚本来解决小问题

> 原文：<https://dev.to/hamatti/i-love-writing-scripts-to-solve-small-problems-462m>

我如此喜欢编程的一个原因是，它允许我自动完成一些小而烦人的事情，否则就需要大量的手工工作。

昨天，我下载了一组文件，它们在一个 zip 文件中有如下的目录结构:

```
- Main Folder
  - Theme A (1)
    - FileA.pdf
    - FileA.txt
    - FileA.jpg
  - Theme B (2)
    - FileB.pdf
    - FileB.txt
    - FileB.jpg
  - Theme C (3)
    ... 
```

Enter fullscreen mode Exit fullscreen mode

不过，对于我的用例，我只对 pdf 文件感兴趣，并希望记录那些写在文件夹括号中的文件的顺序。我本可以在 Finder 中手动将它们全部移动到一个新文件夹中，但是因为它们有几十个，所以我打开了我的编辑器并开始编写 Python。

```
import os
import re
import shutil

NUMBER_PATTERN = re.compile(r'\((\d+)\)')

for directory, _, files in os.walk('.'):
    if directory == './Output':
        continue
    for filename in files:
        if not filename.endswith('.pdf'):
            continue

        episode_number = NUMBER_PATTERN.findall(directory)[0]
        path = os.path.join(directory, filename)

        new_filename = f'{episode_number:0>2} - {filename}'
        new_path = os.path.join('Output', new_filename)

        print(f'Copying {path} to {new_path}')
        shutil.copyfile(path, new_path) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个单独运行的脚本，依赖于一个非常具体的命名和文件结构，以及输出文件夹的存在。所以如果有什么东西坏了，它就会坏掉。

这意味着它不太容易维护，可能不是我能写的最好也不是最 pythonic 化的代码。但是由于它是一个在这种特殊情况下运行一次的脚本，我可以手动从错误情况中恢复。

这就是它的美妙之处。它不一定是好代码，只需要工作一次。它为我省去了许多烦人的手动复制和重命名文件的工作。

与在工作中为产品编写良好的、可维护的和抗错的代码的追求相反，这些脚本允许我通过拼凑一些代码来获得小的成功。

**编辑**我想在评论 :
中突出这个由 [@teroyks](https://dev.to/teroyks) 创作[的漂亮 bash 脚本](https://dev.to/teroyks/comment/c9im)

```
find . -iname "*.pdf" | while read F; do FILE=$(basename "$F"); NR=$(printf "%02d" "$(echo "$F" | sed "s/.*(\(.*\)).*/\1/")"); cp -v "$F" "./Output/$NR - $FILE"; done 
```

Enter fullscreen mode Exit fullscreen mode

**edit2**

```
for f in (find . -iname "*.pdf")
    set file (basename $f)
    set number (string match -r "\((.*)\)" $f)[2]
    set number (printf "%02d" $number)
    cp -v $f "./Output/$number - $file"
end 
```

Enter fullscreen mode Exit fullscreen mode