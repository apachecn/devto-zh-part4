# 码头工人...，我们需要了解的术语(哇 bhs 英国人！！！印度尼西亚 tetep 有限公司...)

> 原文：<https://dev.to/farid_aditya/docker-terms-that-we-need-to-understand-wow-bhs-inggris-gakjuga-tetep-bhs-indonesia-166h>

Pada diskusi sebelumnya, kita telah membahas cara menggunakan Docker. Mulai dari instalasi, menggunakan perintah run dan pull, dan juga beberapa perintah lainya. Diskusi kali ini kita akan membahas beberapa hal seputar Docker.

#### Docker Dari sudut pandang lain(big picture Dari newbie)

Sebelum kita Docker lebih lainjut. Saya ingin membahas sedikit tentang Docker jika kita melihatnya dari sudut pandan yang lain.

Kadang kita bingung mendengar istilah istilah di dunia container ( *atau istilah kerenya Container Runtime Environments* ) ini , mungkin selain Docker ada juga container container yang mirip mirip, misalnya rkt, Mesos Containerizer, OpenVZ dan lain-lain. Balik lagi ke Docker, istilah docker misalnya image, container, registry dan lain lain yang akan membuat kita (terutama saya), menjadi bingung dan akhirnya malas untuk memcoba Docker dan lainya yang mirip.

Karena itu untuk lebih memahami istilah istilah Docker, dalam diskusi ini saya ingin membahas Docker jika kita melihat Docker dari sudut pandang OOP (Object Oriented Programing).

Pertama istilah yang umum adalah **Docker registries** . Dalam Docker, registry adalah tempat untuk menyimpan **image** , registry ini bisa berupa registry local atau registry public yang secara default adalah **Docker Hub** . Dalam pandangan saya, Jika kita masukan dalam dalam paradigma OOP, registry ini bekerja seperti Class, Jika dalam OOP adalah class yang menghasilan object, begitu juga registry yang akan menghasikan image yang disimpanya.

Kedua **Image** , produk (bisa berupa Os, WebAppdan lain lain) yang disimpan dalam registry. Image ini bisa berupa image lokal atau image public. Dalam sudut pandang OOP, image ini seperti Object. Sama seperti Object, jika object merupakan merupakan representasi dari kumpulan instance. Begitu pula image yang merupakan representasi dari sekumpulan container.

Ketiga, **Container** merupakan image yang sedang berjalan. Dalam pandangan OOP instance merupakan object yang berjalan dan mempunyai nilai yang pasti, begitu pula container. container merupakan image yang sedang berjalan dan mempunyai nilai yang pasti minimal Container ID (Dalam Docker).

#### Kesimpulan

Pada diskusi singkat kali ini, kita membahas Konsep dari Docker jika kita melihatnya dari sudut pandang yang lain yang kita lebih mengerti. Pada diskusi selanjutnya kita akan membahas perintah perintah Dasar yang berhubungan dengan Container.