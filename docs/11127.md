# Docker..., (beberapa) command-line (yang perlu kita ketahui)

> 原文：<https://dev.to/farid_aditya/docker-beberapa-command-line-yang-perlu-kita-ketahui-3nbo>

Pada diskusi kemarin kita membahas konsep dari Docker, diskusi kali ini kita akan membahas perintah perintah dasar docker yang perlu kita ketahui.

#### 回顾

Kita telah membahas instalasi Docker, menjalankan container menggunakan **docker run** , mendownload image menggunakan **docker pull** dan beberapa perinth yang mendukungnya.

*   **docker 版本**

Digunakan untuk melihat versi docker yang kita pakai.

*   **docker 运行【nama 图像】**

Untuk menjalankan image instance dari image di dalam container, Jika image ada damrespository locakmaka image dijalanakan jika tidak ada maka imageakan di download dulu dari respository public.

*   **docker pull【NAMA image】**

untuk mendownload image kedalam respository local, tetapi image tidak akan dijalankan.

*   **docker 帮助**

Melihat Cheatsheet bawaan dari docker.

*   **docker ps**

melihat container aktif yang ada, jika perintah ini ditambah dengan perintah all ( **docker ps -a** ), maka akan menampilkan container aktif danyang tidak aktif.

Pada dasarnya perintah docker akan seperti di bawah ini :

> **docker[选项]命令[arg...]**

#### 命令行 lainya

Sebelum saya lupa, menurut dokumen docker, **Docker Image** adalah template yang aksesnya hanya **Read-Only** . Dan Merupakan kumpulan dari componen (misalkan, Os, Web Server, dan lainlain) yang merupakan pondasi dari **Container**

Setelah kita mereview beberapa command-line, kita akan melihat beberapa command-line lain, seperti :

*   **包含 Secara interactive 的菜单**

Pada diskusi sebelumnya kita pernah mencoba menjalankan containerdengan perintah run, kai ini kita akan menjalankan containerlagi tetapi dengan mode interactive dengan perintah **--interactive --tty** , short syntaxnya **-i -t** , jika dipedekan lagi menjadi **-it** . untuk keluar dari interactive mode menggunakan **ctrl+p diikuti ctrl+q** bisa juga dengan **exit** pada terminal

misalnya:码头运行-i -t ubuntu:18.04

*   **启动、停止、重启**

fungsi dari start, stop, restart sesuai dengan namanya, perintahnya adalah

**docker [strart | stop |r estart] [Container name | Container ID]** yang perlu dilakukan kita perlu melihat dulu container yang ada dengan **docker ps** atau **docker d -a** , jika ada container yang aktif kita bisa menonaktifkan dengan perintah **docker stop namaContainer** atau sebaliknya jika kita ingin mengatifkan container kitabisa menggunakan perintah

**docker start namaContainer** . Perintah **restart** merupakan gabungan perintah stop an start.

*   **Mencari Repository di dock Hub**

Untuk mencari repository di docker hub **docker search [Keyword]** , hasilnya akan menamplkan list dari repository yang berhubungan dengan keyword yang kita cari. Dan bisa digunakan untuk mengunduh repository sesuai namanya

misalnya : **码头搜索 yi**，**码头搜索 mysql**

*   **Perintah lainya**

Command line di docker sebenarnya sangat banyak dan kita bisa melihat melalui fungsi help dari docker. Yang ingin kita diskusikan di point ini adalah kita bisa mengabungkan beberapa perintah di docker, kita mengetahui perinta untuk melihat list semua Container ID dengan **docker ps -a -q** .

untuk menonaktifkan semua kontainer yang aktif kita bisa menggunakan perintah:

**docker stop $(docker ps -q)**

jika kita inginmenghapus semua Container kita bisa menggunakan perintah di bawah ini :

**docker rm $(docker ps -aq)**

#### Penutup

Pada seri ini kita tidak membahas Docker secara mendalam, hanya beberapa perintah sederhana saja. Hapan pada seri ini sederhana saja hanya membuka sedikit wawasan tentang Docker, sehingga jika kita ingin mempelajari tentang docker melalui tutorial tutorial yang banyak bertebaran kita mudah untuk mengikutinya, Karena pembahasan docker cukup luas. Mudah mudahan pada kesempatan lainya (Diluar seri ini) kita akan membahas topik tentang docker lagi. Terima Kasih, selamat bertualang di Dunia containerization platform.