# 使用标准化用户数字状态描述文件的建议。yml

> 原文：<https://dev.to/nirmal_kumar/proposal-for-standardising-user-digital-presence-profile-using-yml-2l3h>

在开发者社区中，我们遇到了许多人，他们通过他们的伟大作品、博客、视频、课程教程、播客等等激励着我们。如果我们有一种标准的方式，使用标准的约定，轻松地分享那些鼓舞人心的数字存在配置文件，会怎么样？

在此向社区分享我的想法，并希望听到大家的意见。如果有任何服务已经解决了这些挑战，请告诉我您的想法，并分享您的意见。

### 背景

在手机中共享个人资料的传统方式使用 VCARD 文件
[(VCard，JCARD) - Wiki](https://en.wikipedia.org/wiki/VCard#jCard) 。但是我没有找到一个人分享他在网络上的数字形象。

初步建议使用简单的。yml 文件，便于应用程序共享和使用。

### 总结

在这个 RFC 中，我提出了一个标准化的方法来创建公共配置文件，并轻松地与其他应用程序共享。该标准化提案将不包括电话号码、地址等个人信息。这主要集中在用户的数字存在和他们的作品、博客和任何其他他们想与任何人分享的公共账户的相应链接上。

### 挑战

*   随着用户使用的应用程序越来越多，这些应用程序中的每一个都使用自定义注册表单、基于 OAuth 基于 SSO 的集成来收集配置文件信息。注册后，个人资料详情不会定期更新。一些信息已经过时(假设用户博客站点被移动到不同的站点)
*   即使是作者也没有一个通用的方法来管理和更新他们的配置文件并保存版本历史。

### 如果每个人都有一个共同的配置文件作为 yaml 文件会怎样:

*   假设一个用户 ABC 使用一个像 GitHub.com 一样的源代码控制版本来维护他的配置文件。他们可以创建多个配置文件，只需一个 Url/文件就可以在任何需要的地方共享。

(例如)
[http://github.com/](http://github.com/)/我的档案/默认档案. yml
[http://github.com/](http://github.com/)/我的档案/工作档案. yml

希望获得用户配置文件信息的应用程序，可以简单地读取 yaml 文件并定期更新它们。

样本 default-profile.yml

```
website: gyanmoti.in
country: india
name: Nirmal
profile_image_url: "https://media.licdn.com/dms/image/C4D03AQFgMmks5SVa_w/profile-displayphoto-shrink_200_200/0?e=1566432000&v=beta&t=XehIjAJiGztgNbN42I5fY6RWvY8dpeN4J_czWn0IRY0"
social_accounts: 
  - facebook: NA
  - twitter: nirmal_kumar
  - youtube: NA
  - linked_in: nirmalweb
creations:
  - courses: http://usercourses.com
  - publications: http://userpublications
  - podcasts: http://userpodcasts 
```

特此请求您对此公开个人资料的评论，从而使网络成为一个更好的分享和管理个人资料的地方。

如果您想在 Github 的这里添加任何内容，请随时发送 PR。