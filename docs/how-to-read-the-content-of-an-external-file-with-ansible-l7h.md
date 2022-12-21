# 如何用 Ansible 读取外部文件的内容

> 原文：<https://dev.to/mayeu/how-to-read-the-content-of-an-external-file-with-ansible-l7h>

您希望将 JSON 数据从一个文件直接加载到您的 Ansible 剧本、任务或角色中吗？你是否开始尝试通过`shell`或命令`module`来读取它，并想知道是否有更好的方法？嗯，有，形式上还是`lookup`外挂。请继续阅读，了解如何使用它。

Ansible 附带了一大堆[查找插件](https://docs.ansible.com/ansible/latest/plugins/lookup.html#plugin-list)，允许从你的游戏之外加载内容。我们今天感兴趣的是`file` [一](https://docs.ansible.com/ansible/latest/plugins/lookup/file.html)。那么，我如何用这个加载我的文件内容呢？

很简单:

```
vars:
  file_contents: "{{lookup('file',  'path/to/file.json')}}" 
```

然后嘭，你就有了`file_contents`变量中的内容！

正如你所看到的，我们正在调用`lookup()`函数，我们要使用的插件有第一个参数(在这种情况下是`file`)，然后是文件的路径。就这样，我们加载了文件内容！

**注意，该文件必须在您负责的控制器**上(即:您运行`ansible`命令的计算机)。

你可以在任何模块中直接使用它，而不需要中间变量:

```
- name: Read the content of a file instead of copying it directly
  copy:
    content: "{{lookup('file',  'path/to/file.json')}}"
    dest: /etc/config/file.json 
```

瞧🙂

*本帖原载于我的网站: [mayeu.me](https://mayeu.me/post/how-to-read-the-content-of-an-external-file-with-ansible/) 。*