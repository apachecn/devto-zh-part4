# Yuk Belajar Yii2 第 3 部分

> 原文：<https://dev.to/farid_aditya/yuk-belajar-yii2-part-3-2f7k>

#### 链接、渲染、静态页面、丹布局

Pada artikel sebelumnya kita telah membahas tentang controller dan view, sesuai dengan janji saya pada artikel kali ini kita akan membahas tentang :

*   Membagi konten menjadi beberapa view (render partial).
*   Membuat halaman statik (static pages).
*   Berbagi data antara view dengan layout.

Tetapi sebelum membahas materi di atas kita akan mengupdate beberapa bagian dari pembahasan kita sebelumnya. Pada bahasan sebelumnya kita sudah membuat list dosen. Kali ini kita akan mencoba memberi link pada view dosen agar kita bisa melihat detail dari dosen. Pertama kita perlu merubah file view daftarDosen.php menjadi seperti kode di bawah ini :

```
<table>
    <tr>
        <th>Id</th>
        <th> nbsp nbsp nbsp</th>
        <th>Nama</th>
        <th> nbsp nbsp nbsp</th>
        <th>Tanggal lahir</th>
    </tr>

<?php foreach($dosen as $item) { ?>
<tr>
        <th><a
            href="<?=Yii::$app->urlManager->createUrl(['tutorial/detail-dosen','id' => $item['id']])?>">
            <?= $item['id'] ?>
            </a></th>
        <th></th>
        <th><a
            href="<?=Yii::$app->urlManager->createUrl(['tutorial/detail-dosen','id' => $item['id']])?>">
        <?= $item['nama'] ?>
        </a></th>
        <th></th>
        <th><b><?= $item['tanggal_lahir'] ?></b></th>
    </tr>
<?php } ?>
</table> 
```

selanjutnya kita perlu membuat method baru dengan nama actionDetailDosen di dalam TutorialController, dengankode program seperti :

```
 public function actionDetailDosen($id)
    {
        $dosen = $this->actionDataDosen();
        $hasil = null;
        foreach ($dosen as $item) {
            if ($id == $item['id'])
                $hasil = $item;
        }
        return $this->render('detailDosen', [
            'dosen' => $hasil
        ]);
    } 
```

seperti sebelumnya kita perlu membuat view baru untuk action yang telah kita buat, sebelum membuat view mari kita mengingat kembali aturan penulisan pada Yii2

> catatan, Perhatikan huruf besar dan kecil di dalam penamaan padaYii2
> 
> Jika Controller mempunyai nama **TutorialController** maka akan mempunyai view dengan nama folder **tutorial** , kemudian jika nama method **actionDetailDosen** maka file view pada folder controller akan bernama **detailDosen.php** dan detailDosen.php pada browser akan dipanggil menjadi **detail-dosen** .

setelah kita membuat file detaildosen.php, maka file tersebut kita isi dengan kode berikut :

```
<h3>Detail Data Dosen</h3>
<br />
Nama:
<b><?= $dosen['nama'] ?></b>
<br />
Tanggal Lahir:
<?php echo Yii::$app->formatter->asDatetime($dosen['tanggal_lahir'], "php:d/m/Y"); 
// Yii::$app->formatter->asDatetime  merupakan perintah bawaan Yii2 
// untuk memformat tanggal
?> 
```

Sekarang mari kita coba kode yang telah kita buat dengan membuka halamand bawah ini pada browser

[http://yii2basic.test/index.php?r=tutorial/daftar-dosen](http://yii2basic.test/index.php?r=tutorial/daftar-dosen)

pada tutorial kali ini kita membuat link di dua tempat pada record yang sama, yaitu id dan nama. Kedua link tersebut mengarah ke method yang sama.

#### 渲染局部

Kadang kita mempunyai bagian yang sama dari beberapa konten, sehingga kita dapat membagi kesamaan konten konten tersebut menjadi bagian yang lebih kecil agar dapat di gunakan di konten lain. Sejujurnya sulit buat saya untuk menjelaskan melalui kalimat yang benar, jadi lebih baik kita langsung praktikan saja, misalnya kita mempunyai halaman seperti ini :

```
Nama Kampus

Isi Konten

Slogan kampus 
```

kita belum membahas layout, jadi kita anggapsaja nama kampus sebagai header dari konte kita dan slogan kampus sebagai footer kita. Untuk kasus kita kita akan membagi header dan footer dalam file terpisah menggunakan partial view. Untuk mengetahui partial view lebih jelas bisa menglihat di situs [Link API partial View Yii](https://www.yiiframework.com/doc/api/2.0/yii-base-controller#renderPartial) . Kita pelu untuk keperluan partial view ini kita akan membuat folde bru di dalam folder **view/tutorial** folder baruini kita bernama **common** . Selanjutnya kita buat 2 buah file baru dalam folder view/tutorial/common, _namaKampus.php dan _sloganKampus.php, masukan kode ke dalam file __namaKampus.php misalnya :

```
<H4>
Marvel Univers(e)ity</br>
_______________________________________

</H4> 
```

pada file _sloganKampus.php kita masukan kode :

```
<h5>
_______________________________________</br>
Membentuk kepribadian <b>Superhero</b> yang baik

</h5> 
```

Setelah itu kita perlu meng update view fari halaman yang ingin kita masukan nama dan slogan. masukan kode :

```
<?php
echo $this->context->renderPartial('common/_namaKampus');
?> 
```

pada bagian atas file, dan kode berikutnya di bagian bawah :

```
<?php
echo $this->context->renderPartial('common/_sloganKampus');
?> 
```

Misalnya pada file index.php akan menjadi :

```
<?php
echo $this->context->renderPartial('common/_namaKampus');
?>

<?php
$dosen = [
    [
        'nama' => 'Prof. Dadang',
        'tanggal_lahir' => '1960-07-28'
    ],
    [
        'nama' => 'Heri, ST, MM.',
        'tanggal_lahir' => '1959-09-01'
    ],
    [
        'nama' => 'Amel',
        'tanggal_lahir' => '1969-07-20'
    ]
];
?>
<table>
    <tr>
        <th>Nama</th>
        <th>Tanggal Lahir</th>
    </tr>
<?php foreach($dosen as $item) { ?>
<tr>
        <td><?php echo $item['nama'] ?></td>
        <td><?php echo $item['tanggal_lahir'] ?></td>
    </tr>
<?php } ?>
</table>

<?php
echo $this->context->renderPartial('common/_sloganKampus');
?> 
```

Update juga view lainya yang telah kita buat hingga saat ini. Jadi dengan Render Partial kita bisa membuat beberapa fitur yang sama untuk banyak halaman, misalnya untuk menampilkan box untuk login user, atau informasi user saat ini, dan lain lain.

#### 静态页面

Halaman statik mungkint terdengar seperti topik tidak menarik, apa lagi jika memakai framework yang memudahkan kita membuat halaman halaman yang dinamis. Tetapi dalam beberapa hal statik page juga dierlukan misalnya untukmenampilkan info backgroun organisasi, info kontak dan lain-lain.

Yii menyediakan cara yang mudah untuk kita membuat statik page dengan menggunakan method **action **dari controller.

```
public function actions()
{
    return [
        'pages' => [
        'class' => 'yii\web\ViewAction',
        ],
    ];
} 
```

Sekarang mari kita mulali buat static pages, pertama kita buat folder dengan nama **pages** baru di dalam **view/tutorial** , kemudian kita buat file baru dengan **coba.php** nama **infoKampus.php** , setelah itu isi kedua file dengan apa saja yang kita sukai, misalnya file **coba.php** saya hanga mengisi dengan isi seperti :

```
<h1>Coba</h1> 
```

dan untuk isi **infokampsus.php** seperti ini :

```
<?php
echo $this->context->renderPartial('common/_namaKampus');
?>

<h3>
Daftar Dosen
</h3>
</br>
<ul>
  <li>Hulk</li>
  <li>ironman</li>
  <li>captain america</li>
</ul>

<?php
echo $this->context->renderPartial('common/_sloganKampus');
?> 
```

kemudian kita buat kode seperti ini :

```
 public function actions()
    {
        return [
            'pages' => [
                'class' => 'yii\web\ViewAction',
            ],
        ];
    } 
```

letakan di bawah deklarasi class. Setelah selesai membuat semua kita bisa mengarahkan browser ke halaman :

*   untuk pageg coba

[http://yii2basic.test/index.php?r=tutorial/pages&view = coba](http://yii2basic.test/index.php?r=tutorial/pages&view=coba)

*   untuk pages infoKampus

[http://yii2basic.test/index.php?r=tutorial/pages&view = info kampus](http://yii2basic.test/index.php?r=tutorial/pages&view=infokampus)

Kita juga bisa menggunakan nama folder lain selain pages, misalnya kita buat folder baru di dalam **view/turorial** misalnya folder **kontak** , setelah itu kita buat file dengan nama jurusanMenembak.php, isi dengan kode :

```
<?php
echo $this->context->renderPartial('common/_namaKampus');
?>

<h3>
Kontak Jurusan
</h3>
</br>
<h5>
hawkeye</br>
</br>
+00 1234 567 89
</h5>

<?php
echo $this->context->renderPartial('common/_sloganKampus');
?> 
```

Kemudian update method action menjadi seperti :

```
 public function actions()
    {
        return [
            'pages' => [
                'class' => 'yii\web\ViewAction',
            ],
            'kontak' => [
                'class' => 'yii\web\ViewAction',
                'viewPrefix' => 'kontak'
            ],
        ];
    } 
```

untuk melihat halaman yang kita buat arahkan browser ke alamat :

[http://yii2basic.test/index.php?r=tutorial/kontak&view = jurusanmenebak](http://yii2basic.test/index.php?r=tutorial/kontak&view=jurusanmenembak)

#### Berbagi Data View Dengan Layout

Layout adalah file yang untuk mengatur tampilan dasar yang ada di site kita, file layout sendiri terletak didalam folder **view/layout** , dan file layout standar dari Yii2 adalah **main.php** .

Selama ini kita, jika menampilkan view dari method yang kita buat adalah bagian dari layout yang letaknya dalam area **< ?= $content ? >** pada **main.php** . yang terletak antara Body Tag.

Untuk berbagi data konten yang kita buat dengan layout salah satu caranya bisa dengan variable

```
$this->params['variable'] = 'isi variable'; 
```

Misalkan kita akan developer name di bagian bawah footer, tambahkan kode ini:

```
<?php
        isset($this->params['devParams']) ? 
        $this->params['devParams'] : [];
        ?> 
```

tambahkan di bawah konten sehingga **main.php** menjadi seperti dibawah ini :

```
 <div class="container">
        <?php
        echo Breadcrumbs::widget([
            'links' => isset($this->params['breadcrumbs']) ? 
            $this->params['breadcrumbs'] : []
        ]);
        ?>
        <?= Alert::widget() ?>
        <?= $content ?>

         <?php
        isset($this->params['devParams']) ? 
        $this->params['devParams'] : [];
        ?>

    </div> 
```

kemudian tambahkan kode berikut ke bagian paling bawah disetiap file dalam **view/tutorial** yang telah kita buat :

```
<?php
echo $this->params['devParams'][] = 'develop by  || Avengers ||';
?> 
```

Sekarang coba buka halaman mana saja yang telah kita buat dari browser, akan muncul tulisan **develop by || Avengers ||** di bagian paling bawah dari konten kita.

menggunakan parameter **params** memang sangat mudah, tetapi juga menyediakan cara lain untuk membagi data dengan view.

Yii menyediakan **Block Statement** untuk berbagi data antara view dengan layout, pada contoh kali ini kita akan membuat blok yang hanya akan menampilkan link jika kita membuka halaman dengan nama linkHalaman.php, pada halaman lain kita hanya akan melihat blok kosong tanpa link. Untuk memulainya kita bisa menggunakan block statemen dengan syntax di bawah ini :

```
<?php $this->beginBlock('block1'); ?>
...content of block1...
$this->endBlock(); ?> 
```

语法**begin block()**akan mengawali block 语句 sedangkan 语法**end block()**akan mengakhiri block 语句。

Untuk lebih memperjelas kita akan membuat blok statemet yang akan menampilkan link ke halaman index dan daftarDosen. Pertama kita akan buat **linkHalaman.php** pada foler **view/tutorial** :

```
<?php $this->beginBlock('linkHalaman'); ?>
<a href="<?=Yii::$app->urlManager->createUrl($index)?>">
    <?= 'index' ?>
    <?= '&nbsp &nbsp'?>
   <a href="<?=Yii::$app->urlManager->createUrl($daftar)?>">
    <?= 'list dosen'  ?>
<?php $this->endBlock('linkHalaman'); ?> 
```

kemudian tambahkan kode dibawah pada **main.php** , letakan tepat diatas conten :

```
 <div class="well">
            <a href="<?=
            Yii::$app->urlManager->createUrl(
            'tutorial/link-halaman')?>">
            kembali ke halaman link </a><br />
            <?php
              if (isset($this->blocks['linkHalaman'])) {
                 echo $this->blocks['linkHalaman'];
             } else {
                 ?>            
                <i>link tidak ditampilkan</i>
                <?php
            }
        ?>
    </div> 
```

kemudian buat method baru pada **TutorialController.php** :

```
 public function actionLinkHalaman()
    {
        $index = 'tutorial/index';
        $daftar = 'tutorial/daftar-dosen';
        return $this->render('linkHalaman', [
            'index' => $index,
            'daftar' => $daftar
        ]);
    } 
```

Setelah semua selesai kita bisa melihat hasilnya pada halaman :

[http://yii2basic.test/index.php?r=tutorial%2Flink-halaman](http://yii2basic.test/index.php?r=tutorial%2Flink-halaman)

Sejauh ini kita sudah melihat beberapa cara untuk membagi data antara view, untuk cara lainya akan kita bahas sambil membahas topik lainya.

#### Tambahan

Selanjutanya kita akan sediki membahas **PrettyUrl** , sejauh ini kita selalu menggunakan URL seperti ini :

[http://yii2basic.test/index.php?r=tutorial%2Flink-halaman](http://yii2basic.test/index.php?r=tutorial%2Flink-halaman)

> `http://yii2basic.test` = domain yng digunakan
> 
> `index.php` = @app/Web/index
> 
> `r=tutorial` = router yang mengarahkan ke Controller Tutorial
> 
> `%2F` = mengarahkan ke action atau function dalam controller

jika kita ingin merubah contoh URL atas menjadi seperti URL yang lebihmudah kita pahami, seperti :

[http://yii2basic.test/tutorial/link-halaman](http://yii2basic.test/tutorial/link-halaman)

Kita perlu menggunakan fungsi yang telah tersedia dalam Yii2 yaitu, **PrettyUrl** . Untuk menggunakan **PrettyURL** . Pertama kita akan membuat file **.htaccess** di dalam folder root kita misalnya pada contoh ini yaitu **yii2basic** . kemudian masukan kode dibawah in pada **.htaccess** :

```
RewriteEngine on
# If a directory or a file exists, use it directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
# Otherwise forward it to index.php
RewriteRule . web/index.php 
```

Kemudian kita tambahkan kode di bawah ini pada **web.php** yang terdapat pada folder **yii2basic\config** , letakan di bagian bawah dari `$config=>'components'` .

```
'urlManager' => [
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules' => []
        ], 
```

hingga `$config=>'components'` menjadi seperti di bawah ini.

```
$config = [
    'id' => 'basic',
    'basePath' => dirname(__DIR__),
    'bootstrap' => [
        'log'
    ],
    'aliases' => [
        '@bower' => '@vendor/bower-asset',
        '@npm' => '@vendor/npm-asset'
    ],
    'components' => [
        'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
            'cookieValidationKey' => 'c0vGsMPuS0qWtgzyHk8c4xfKYfNwOPGh'
        ],
        'cache' => [
            'class' => 'yii\caching\FileCache'
        ],
        'user' => [
            'identityClass' => 'app\models\User',
            'enableAutoLogin' => true
        ],
        'errorHandler' => [
            'errorAction' => 'site/error'
        ],
        'mailer' => [
            'class' => 'yii\swiftmailer\Mailer',
            // send all mails to a file by default. You have to set
            // 'useFileTransport' to false and configure a transport
            // for the mailer to send real emails.
            'useFileTransport' => true
        ],
        'log' => [
            'traceLevel' => YII_DEBUG ? 3 : 0,
            'targets' => [
                [
                    'class' => 'yii\log\FileTarget',
                    'levels' => [
                        'error',
                        'warning'
                    ]
                ]
            ]
        ],
        'db' => $db,
        'urlManager' => [
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules' => []
        ],
    ],
    'params' => $params
]; 
```

Setelah langkah langkah diatas, kita dapat memasukan Url, misalnya :

[http://yii2basic.test/tutorial/link-halaman](http://yii2basic.test/tutorial/link-halaman)

Sepertinya kita cukupkan sampai disini dulu, pada part selanjutnya kita mulai akan membahas tentng model. sampai jumpa di part selanjutnya.