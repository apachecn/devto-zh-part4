# 将文件移动到子文件夹时出现问题

> 原文：<https://dev.to/philambert54/problem-with-moving-files-to-subfolders-21n4>

嘿，
我对下面的代码有问题。它应该自动将与目录同名(或仅部分同名)的文件移动到目录和子目录中。
它对根目录非常有效，但对子目录无效。我以为 os.walk 是遍历整个树形结构。如果你看到错误来自哪里....谢谢你的帮助。

def move files 2 folder():
root _ path = lien vers mon répertoire de départ
for(root，dirs，files)in OS . walk(root _ path):
for dir in dirs:
for file in files:
fileX = OS . path . split ext(file)[0]
if fileX in dir:
shutil . copy(os.path.join(root_path，file)、OS . path . join(root _ path，dir))