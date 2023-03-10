# Yuk Belajar Yii2 第 1 部分

> 原文：<https://dev.to/farid_aditya/yuk-belajar-yii2-part-1-506h>

#### Berkenalan Dengan Framework

Jika kita pernah membaca buku atau forum yang membahas pemrogaman, kemungkinan besarkita pernah membaca atau mendengar istilah MVC, kempendekan dari istilah Model-view-Control(ler). MVC adalah metode berorientasi objek yang membagi kode program menjadi beberapa bagian dengan tujuah mempermudah dalam pengkodean program (coding) dengan mengimplementasikan paradigma pemrograman berorientasi objek.

Framework sendiri dapat diartikan sebagai sebuah kerangka kerja, yang digunakan memudahkan pekerjaan kita dalam membuat sebuah progra. Framework PHP dapat diartikan sebagai suatu kerangka kerja yang terpola dan memudahkan pengembang web dalam pembuatan web menggunakan script PHP.

Pada umumnya Framework-Framework PHP yang ada menggunakan konsep MVC dalam penerapanya, dengan tujuan mempermudah pengembangan aplikasi dengan membagi program menjadi 3 layer yaitu layer model, view dan controller.

*   View sendiri lebih bertangung jawab pada tampilan antar muka pengguna
*   Model merupakan layer yang berhubungan dengan data
*   Controller berhubungan dengan Request dan Respond, yang mengarahkan View dengan Model yang digunakan oleh view

Untuk mempelajari Framework sebenarnya tidak terlalu sulit jika sebelumnya kita sudah mengerti bahasa pemroraman yang digunakan oleh framework yang kita gunakan. Misalnya Framework PHP, jika kita sudah pernah mempelajari bahasa pemrograman PHP maka untuk mempelajari frameworknya akan cendrung lebih mudah. Begitu pula untuk framework dengan bahasa pemrograman lain akan lebih mudah jika kita telah sedikit mengerti bahasa pemrograman yang digunakan framework tersebut. Tetapi jika sebelumnya belum mengerti tidak akan terlalu masalah jika kita pernah mempelajari algoritma dan mengerti paradigma pemrograman berorientasi objek (OOP).

#### 耶 2

Pada pembahasan ini kita akan membahas Yii (Yii2) Framework, Yii adalah salah satu framework PHP, framework PHP sebenarnya sangat banyak dan semuanya mempunyai penggunanya atau penggemarnya masing-masing. Selain mempunyai pengguna dan penggemar, masing-masing framework juga mempunya keunggulan dan kekurangan. Sebagai pengenalan selain Yii masih ada framework lain seperti Laravel, CakePHP, Symfony, Zend, CodeIgniter dan lain-lain.

Yii2 menurut pengembangnya adalah :

> [Yii2 框架](https://www.yiiframework.com/)
> 
> Yii 是一个高性能、基于组件的 PHP 框架，用于快速开发现代 Web 应用程序。Yii(发音为 Yee 或[ji:])在中文中的意思是“简单和进化的”。它也可以被认为是 Yes It Is 的首字母缩写。

Pada bagian ini kita akan hanya membahas pengenalan Yii dan framework secara umum saja, kita akan mulai mempelajari Yii2 lebih mendalam lagi mulai dari bagian-bagian selanjutnya, dan untuk instalasi Yii2 kita, dapat menggunakan composer, dengan perintah seperti ini :

> PHP composer . phar create-project-prefere-dist yi isoft/yii 2-app-basic namap project

atau mendownload file archive langsung dari [github](https://github.com/yiisoft/yii2) . Untuk Detail cara instalasi Yii2 dapat di lihat langsung di [The Definitive Guide to Yii 2.0](https://www.yiiframework.com/doc/guide/2.0/en/start-installation) . pada seri ini kita tdak akan membahas cara instalasi ataupun struktur folder Yii2 ( kedua bahasan tersebut sudah sangat jelas di dalam situs Yii ).