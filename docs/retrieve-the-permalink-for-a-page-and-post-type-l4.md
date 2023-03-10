# 检索页面和文章类型的永久链接。

> 原文：<https://dev.to/vbaimas/retrieve-the-permalink-for-a-page-and-post-type-l4>

你知道 WordPress 有一些有用的链接页面和存档页面的功能吗？让我们看看他们的行动。

**场景:**
你通常会通过 ID 来获取页面链接，但是如果你有一个本地主机，而在线安装的 ID 是不同的，你该如何链接页面呢？

## 通过标题获取页面的永久链接

```
<a href="<?php echo get_permalink( get_page_by_title( 'about' ) ); ?>">About Me</a> 
```

## 通过名称获取页面的永久链接

```
<a href="<?php echo get_permalink( get_page_by_path( 'About' ) ) ?>">About Me</a> 
```

## 通过 slug 获取页面的永久链接

```
<a href="<?php echo get_permalink( get_page_by_path( 'about' ) ); ?>">About Me</a> 
```

## 按父/子获取页面的永久链接

**场景:**
如果你想用 child-page 的方法获得 permalink，那么你需要传递 full slug。因此，在这种情况下，我们有一个子页面“about”和一个父页面“skills”。

```
<a href="<?php echo get_permalink( get_page_by_path( 'about/skills' ) ) ?>">Skills</a 
```

## 获取自定义帖子类型存档页面的链接

**场景:**
如果你有一个自定义的帖子循环，并且在底部你想添加“查看全部”链接，那么你应该如何引导页面显示所有该类型的帖子？

```
 <a href="<?php echo get_post_type_archive_link ( 'your_post_type' ) ?>"</a> 
```

***注意:***
在`get_post_type_archive_link()`返回`false`的情况下，请确保在使用`register_post_type()`时使用了参数`has_archive => true`。