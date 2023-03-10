# 确定性的

> 原文：<https://dev.to/bak3y/deterministic-ansible-3ch7>

最近，我不得不将 AppDynamics 代理部署到一些同时运行 tomcat 和 JBOSS 工作负载的系统上，下面是我如何使用一个剧本来管理它们。

* * *

## 1 -弄清楚我在和哪个平台打交道

```
#check to see if we're tomcat or jboss
- name: Check to see if tomcat user exists
  stat:
    path: /home/tomcat8
  register: tomcat8

- name: check to see if jbossadm user exists
  stat:
    path: /home/jbossadm
  register: jbossadm 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我正在对运行 java 工作负载的用户的主目录进行简单的 stat 模块检查。在我们的用例中，这两个用户永远不会共存于一个服务器上。

## 2 -根据我们上面的发现交换文件/文件夹的所有权

```
#creates directory path
- name: Create the directories for site specific configurations owned by tomcat8
  file:
    path: /usr/local/appdynamics/appagent/
    state: directory
    owner: tomcat8
    group: tomcat8
    mode: 0755
  when: tomcat8.stat.exists

- name: Create the directories for site specific configurations owned by jbossadm
  file:
    path: /usr/local/appdynamics/appagent/
    state: directory
    owner: jbossadm
    group: jbossadm
    mode: 0755
  when: jbossadm.stat.exists 
```

Enter fullscreen mode Exit fullscreen mode

因此，在每种情况下，我们都创建相同的文件夹路径，并根据我们运行这个 ansible playbook 的平台来更改 linux 文件系统的所有权。

[stat](https://docs.ansible.com/ansible/latest/modules/stat_module.html) 模块和 [when](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html?highlight=conditionals#the-when-statement) 条件是非常强大的工具，您可以使用它们来使 Ansible 超级灵活，而不必传递一堆额外的参数来控制在您的剧本中执行哪些任务。