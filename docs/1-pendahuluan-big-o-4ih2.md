# Big O: [1] Pendahuluan Big O

> 原文：<https://dev.to/luthfiajiw/1-pendahuluan-big-o-4ih2>

Bismillahirrahmanirrahim，

Dimulai dari artikel ini, saya akan mencoba merangkum materi yang sudah saya kumpulkan tentang **Big O Notation** dari berbagai sumber yang sudah tersedia di internet. Dan bahasa pemrograman yang akan saya gunakan sebagai pengantar adalah bahasa **Javascript** .

Mengenai pembahasan tentang Big O ini, saya akan menjadikannya ber- *series* dan membaginya per bagian-bagian supaya lebih ringkas, cepat dan urut dalam memahami dan mempelajarinya.

Akan ada beberapa bagian yang sedikit mengandung unsur matematik. Tapi tenang, kuasai dan jangan panik! Karena untuk memahami **Big O** tidak terlalu diperlukan pemahaman matematika yang pekat. Cukup siapkan mental dan keinginan yang kuat seperti saat kau ingin mendapatkan hatinya, *eh* .

Karena bahasa yang saya gunakan disini adalah **Javascript** , maka akan sangat membantu jika kamu sudah menguasai sintaksis dasar milik Javascript. Namun tak apa jika kamu ingin mencoba mengikuti materi dengan bahasa pemrograman yang sudah kamu kuasai sekarang, saya yakin kamu pasti bisa.

Oke, selanjutnya objektif-objektif kita pada materi ini adalah:

1.  Memotivasi kebutuhan akan sesuatu seperti **Big O Notation** , sebagai seorang programmer atau developer terkadang kita hanya peduli pada kode yang kita tulis, asalkan kode itu berhasil berjalan tanpa error, kita senang dan kita tidak peduli pada performanya. Disinilah kita akan menumbuhkan rasa ~~sayang~~ peduli kita akan performa kode yang kita tulis.
2.  Mendeskripsikan *Apa itu Big O Notation?*
3.  Menyederhanakan ekspresi-ekspresi Big O
4.  时间复杂度丹*空间复杂度*
5.  Mengevaluasi *Time Complexity* dan *Space Complexity* dari algoritma yang berbeda menggunakan Big O Notation
6.  Mendeskripsikan apa itu *logaritma*

* * *

Baiklah, tarik napas dalam-dalam dan keluarkan perlahan lewat pori-pori kulit.

Kita akan mulai objektif kita pada poin pertama yaitu Menumbuhkan rasa butuh pada Big O Notation.

Pembahasan materi ini sejatinya adalah tentang **Algoritma** , yaitu tentang memecahkan tantangan-tantangan dan masalah-masalah.

Pada setiap bagian nantinya akan ada banyak cara untuk memecahkan tantangan yang bermacam-macam dan semuanya berhasil diimplementasikan.

Tapi selanjutnya bagaimana kita bisa tahu mana cara yang *terbaik* untuk diterapkan?

Oke, mari kita ambil sebuah contoh; jika kita memiliki dua cara implementasi berbeda pada satu fungsi yang sama, lalu bagaimana cara kita menentukan mana implementasi yang terbaik?

Disinilah peran **Big O** hadir, Big O adalah *sebuah cara untuk menggeneralisasi kode, kemudian membandingkan kode tersebut dan membandingkan performanya dengan kode yang lain.*

*Misalkan* , Tulislah sebuah fungsi yang menerima string sebagai argumennya dan mengembalikan string tersebut secara terbalik.

```
reverse('helloww') => 'wwolleh' 
```

Enter fullscreen mode Exit fullscreen mode

Ada 10 cara berbeda yang saya temukan di stackoverflow, kamu bisa lihat [di sini](https://stackoverflow.com/questions/958908/how-do-you-reverse-a-string-in-place-in-javascript) .

Nah, akan lebih baik jika ada sebuah sistem yang mengurutkan klasifikasi kode tersebut sehingga kita bisa memberi label dari yang terbaik hingga yang terburuk. Ilustrasi nya seperti di bawah ini,

[![metric](img/f9b1bbb7556978b17ffc53819ea626b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W8ueDods--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cs3ojWb.png)

Big O memiliki ekspresi yang mewakili label-label di atas, tetapi agak sedikit matematis. Untuk sekarang gambar di atas akan sedikit lebih membantu kita untuk membayangkan seperti apa Big O itu.

* * *

### 世卫组织在乎吗？

Siapa yang peduli pada hasil dari fungsi di atas tadi? tidak ada yang peduli!

Yang harus kita pedulikan sekarang adalah bagaimana cara kita memilih performa terbaik dari algoritma yang kita buat untuk fungsi tersebut.

Karena yang ditanyakan dalam interview-interview perusahaan besar, yang memiliki ribuan data, adalah bagaimana kita membuat sebuah algoritma yang dapat menghemat waktu sesingkat-singkatnya dan secepat-cepatnya dalam memroses data tersebut.

**Performa** akan sangat diperhatikan disini dan dari situlah hadir solusi atau algoritma terbaik.

马卡:

*   Penting untuk memilih diksi atau kosa kata yang tepat untuk menjelaskan bagaimana kode kita berjalan
*   Akan sangat baik mendiskusikan kekurangan dari masing-masing cara yang digunakan
*   Ketika kode kita berjalan lambat atau bahkan *crash* , identifikasilah bagian kode mana yang tidak efisien sehingga kita dapat memperbaiki kualitas kode yang dibuat. Jadi jangan hanya mencari *error* nya saja, tapi juga pahami cara kerja dari setiap baris kode yang ditulis, dan itulah yang akan di bahas pada materi **Big O** .

Sedikit informasi, bahwa pada saat kamu menjalani interview kerja, maka teori Big O ini seringkali ditanyakan untuk mengetahui seberapa paham kamu dengan apa yang kamu tuliskan. Jadi ikuti terus materi tentang Big O ini, Oke?

Sepertinya kita cukupkan dulu sedikit perkenalan tentang **Big O Notation** di sini. Dan pada artikel selanjutnya kita akan mulai membahas contoh-contoh kode dan mencari tahu efisiensi dari kode tersebut.

Semoga yang sedikit ini bisa bermanfaat, sampai bertemu lagi :)