# 在木偶中循环

> 原文：<https://dev.to/vsreelasya/looping-in-puppet-1a8c>

如何将 puppet 清单中的散列映射数组循环到模板
的简单示例

```
 targets => [
      {
        host => 'example.com',
        port => '443',
      },
      {
        host => 'example1.com',
        port => '8081',
      },
      {
        host => 'example2.com',
        port => '80',
      },
    ] 
```

此部分用于在再培训局模板中循环。

```
<% @targets.each do |variable | -%>
    <%= variable['host'] %> <%= variable['port'] %> 
  <% end -%> 
```

**目标**是列表的数组，**变量**是你想要指定的变量的名称