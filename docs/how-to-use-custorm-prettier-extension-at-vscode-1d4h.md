# 如何在 vscode 使用 custorm beautiful 扩展？

> 原文：<https://dev.to/zuhairtaha/how-to-use-custorm-prettier-extension-at-vscode-1d4h>

你好，
我想在 vscode 使用更漂亮的扩展。但是我想用这个

代替原来的那个。
在`.prettierrc` file or 和 vscode 设置中有什么设置可以让它用它来格式化代码吗？

👌我试图格式化当前打开的文件并保存所有文件，就是添加`macros`扩展名，然后在`settings.json`添加下一行:

```
 "macros":  {  "prettierAndSaveAll":  [  {  "command":  "workbench.action.terminal.sendSequence",  "args":  {  "text":  "./node_modules/.bin/prettier --write '${file}'\u000D"  }  },  "workbench.action.files.saveAll"  ]  } 
```

但是我不得不打开终端让它工作，我觉得它很慢🙄。所以我对这种方式不满意，并寻求更好的方式