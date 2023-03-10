# 在我的编程生涯中，每 5 分钟我是如何使用 fzf 的

> 原文：<https://dev.to/dineshba/how-i-am-using-fzf-for-every-5-min-of-my-programming-life-1lh8>

#### 为新用户安装

```
$ brew install fzf
$ $(brew --prefix)/opt/fzf/install # useful key bindings and fuzzy completion 
```

> 注意:最好使用键绑定和模糊补全

#### 我使用的默认选项

```
export FZF_DEFAULT_OPTS='--height 40% --reverse --border' 
```

#### 我的用法

##### 在命令历史中搜索:

CTRL + R

##### 改变目录:

```
cd + (CTRL + T) # type cd and then press CTRL plus T
or
cd ** + TAB 
```

##### 在 vim(或任何编辑器)中查找并打开文件:

```
vim $(fzf)
vim + (CTRL + T)
vim ** + TAB
vim $(fzf --preview 'cat {}') 
```

> 您甚至可以使用 tab 键搜索和打开多个文件

##### 杀死一个进程:

```
kill + TAB 
```

> 注意:想想正常的方式。我们必须 ps aux 来列出进程，grep 所需的进程，然后复制 pid 的正确列，然后调用 kill。这里只差一步。

##### 将 ssh 导入机器:

```
ssh ** + TAB 
```

##### 获得文件的一瞥

```
fzf --preview 'cat {}' 
```

#### 扩展性

Fzf 的用法是**无限**。我们可以在任何我们想搜索的地方使用它，并从列表中选择一个(或多个)

##### 案例 1:

```
How will you jump to different directory?
- cd /path/to/your/project/path/to/your/service 
```

##### Fzf 道:

```
alias goproject="cd $(find path/to/your/project -type d -maxdepth 2 | fzf)"
goproject 
```

多个项目或目录可以有多个别名

##### 案例二:

```
How will you switch git branch? (select one branch from many)
    - git branch -a
    - copy the branch name
    - git checkout $new_branch_name
    or
    - git checkout + TAB (auto completion feature)

    Both the above approches are not O(1) :-P. How can we optimize? 
```

##### Fzf 道:

```
alias gcx="git checkout \$(git branch -a | sed 's/remotes\/origin\///' | grep -v '\*|HEAD' | sort |uniq | fzf --select-1)"
gcx 
```

> 注意:在您的终端上试试，感受一下不同之处

##### 案例三:

```
How will you tmux workspaces? We are using tmuxp tool to do that.
- tmuxp load workspace_name
or
- tmuxp load ~/.tmuxp/workspace_name.yaml

First involes rembembering all workspace_names (Space Complexity != O(1)) and type correct and fastly (Time Complexity != O(1)). Second involes lot of TABs or searching from commit history (Time Complexity != O(1)) 
```

##### Fzf 道:

```
alias ktx='tmuxp load $(ls ~/.tmuxp/*.yaml | grep ".*/.tmuxp/" -r "" | grep "\.yaml" -r "" | fzf) --yes' 
```

##### 案例四:

```
Okay we created tmux workspaces easily using Case 3\. How will kill tmux workspaces now?
- tmux kill-session workspace_name 
```

##### Fzf 道:

```
$ alias tkill="for s in \$(tmux list-sessions | awk '{print \$1}' | rg ':' -r '' | fzf -m); do tmux kill-session -t \$s; done;"
$ tkill 
```

> 注意:我们给出了`fzf -m`,这意味着我们可以选择多个并传递到下一阶段(基本上是杀死多个会话)

##### 有效使用 fzf 的其他工具

*   库贝特尔
*   库本斯
*   惰性连接
*   可能是你的工具

> 用 [rg](https://github.com/BurntSushi/ripgrep) 替换 grep 以获得更好的体验

#### 总结:

**无论在哪里**我们都要**从列表中搜索并选择一个/多个**，然后**就可以使用 fzf** 。所以使用的选择是无限的。如果你**以不同的方式**使用 fzf，请**在此** 提出问题或公关[，以便我们了解。谢了。](https://github.com/dineshba/til/blob/master/misc/fzf.md)