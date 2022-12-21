# 颤振安装步骤。

> 原文：<https://dev.to/sidkhore1002/flutter-installation-steps-5ajd>

步骤:-

1.  下载 Flutter SDK :-链接:-"[https://flutter.dev/docs/get-started/install/linux"](https://flutter.dev/docs/get-started/install/linux%E2%80%9D)
2.  提取 Flutter:-command:-tar xf ~/Downloads/Flutter _ Linux _ v 1 . 7 . 8+hot fix . 4-stable . tar . xz
3.  专用终端的路径导出:-导出路径="$PATH: `pwd` /flutter/bin "
4.  永久路径导出:-打开 bashrc 文件:-转到主目录终端-> nano ~/。bashrc 在文件末尾写入:-export PATH = "[PATH _ Where _ Flutter SDK Extracted]/Flutter/bin:$ PATH "示例:-export PATH = "/home/sid/0 _ AAA _ iauro/Flutter/s/Flutter/bin:$ PATH "
5.  运行以下命令，查看是否需要安装任何依赖项来完成安装:- command :- flutter doctor -v
6.  将颤振扩展添加到 vs 代码中。
7.  找到 Flutter SDK。
8.  在 flutter 中创建新项目:- command:- flutter 创建项目名称

左侧支撑:-

1.  [https://flutter.dev/docs/get-started/install/linux](https://flutter.dev/docs/get-started/install/linux)
2.  [https://jonathanmh . com/getting-started-with-flutter-on-Linux-for-Android-](https://jonathanmh.com/getting-started-with-flutter-on-linux-for-android-)初学者教程/
3.  [https://www . techo Moro . com/how-to-install-and-setup-flutter-on-Ubuntu-18-04-1-](https://www.techomoro.com/how-to-install-and-setup-flutter-on-ubuntu-18-04-1-)lts-bionic-beaver/
4.  [http://flutter-tutorials.com/install-flutter-in-windows/](http://flutter-tutorials.com/install-flutter-in-windows/)
5.  检查路径变量(bashrc 文件):-[https://www . tutorial kart . com/flutter/flutter-install-on-Linux-Ubuntu/](https://www.tutorialkart.com/flutter/flutter-install-on-linux-ubuntu/)