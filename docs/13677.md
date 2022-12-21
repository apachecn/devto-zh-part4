# Yuk Belajar Yii2 第 2 部分

> 原文：<https://dev.to/farid_aditya/yuk-belajar-yii2-part-2-2okl>

#### Mengenal 控制器，动作丹视图

beberapa hal yang perlu kita mengerti sebelum memulai Yii adalah :

*   服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

Yii adalah PHP framework jadi diharapkan setidaknya kita sudah mengerti dasar-dasar bahasa pemrograman PHP

*   面向对象编程

Kita juga perlu mengetahui dasar pemrograman berorientasi objek (OOP), jika kita kurang familiar dengan OOP, kita dapat mempelajarinya melalui tutorialnya secara online

*   命令行和编写器

Yii mengunakan paket manager PHP yaitu Composer, jika kita belum megerti cara menggunakan composer ini saatnya kita mulai mempelajarinya.

Pada bagian ini kita akan membahas tentang Controller dan View, membuat interaksi antara controller dengan view, dan juga sedikit membahas tentang layout. pembahasanya akan mencakup :

*   控制器，动作丹视图
*   mengirim data dari Controller ke View

#### 控制器，动作丹视图

Sesuai dengan dokumen resmi Yii, hal pertama yang perlu kita coba untuk pengenalan yii adalah membuat Controller yang dapat menangani "hello" task, pertama kita akan membuat fungsi "Say" pada controller yg dapat membaca parameter dari Request, dan jika tidak terdapat parameter dalam request maka Action akan menampilkan "hello".

Beberapa hal yang harus diperhatikan saat membuat Controller, adalah :

> *   Namespace pada bagian paling atas dari Controller
> 
> (biasanya adalah app\controllers)
> 
> *   Use Path, untuk mendeklarasikan class-class yang digunakan
> 
> 控制器必须继承 yii\web\Controller。
> 
> Contohnya : `namaController extend yii\base\Controller`
> 
> *   Action merupkan function dari controller, action di Yii diawali dengan kalimat action
> 
> lalu diikuti nama dari action tanpa spasi dan diawali huruf Besar
> 
> contohnya : `public function actionNamaActionya();`

Setelah kita membahas anatomi dari Controller, maka kita akan membuat Controller Pertama kita. Pertama kita buat file dengan nama TutorialController.php di dalam Folder / Directori controller, controllers/TutorialController.php setelah itu masukan kode program seperti di bawah ini :

```
<?php
/*
 * Namespace pada bagian paling atas dari Controller 
 * (biasanya adalah app\controllers)
 */
namespace app\controllers;

/*
 * Use Path, untuk mendeklarasikan class-class yang digunakan
 */
use Yii;
use yii\web\Controller;

/*
 * Controller harus merupakan inheritance dari yii\web\Controller 
 * Pada contoh ini yii\web\Controller langsung dipanggil dengan nama 
 * classnya karena yii\web\Controller sudah dideklarasikan di use path
 */
class TutorialController extends Controller
{
    /*
     * Action merupkan function dari controller,
     */
    public function actionSay($message = 'Hello')
    {
        return $this->render('say', [
            'message' => $message
        ]);
    }
} 
```

selanjutanya untuk menampilkan fungsi "actionSay" kita perlu membuat view dari action tersebut di dalam folder view/tutorial , sehingga action tersebut dapat di tampilkan di browser. Di bawah ini adalah kode program dari view.

```
<?php
use yii\helpers\Html;
?>
<?= Html::encode($message) ?> 
```

setelah kedua kode di atas komplit, kita bisa mencoba dengan mengarahkan browser ke halaman dibawah ini

[http://yii2basic.test/index.php?r=tutorial/say](http://yii2basic.test/index.php?r=tutorial/say)

atau ke halaman ini jika kita ingin membuka halaman "say" denganme masukan parameter, misalnya "controller pertama saya".

[http://yii2basic.test/index.php?r=tutorial/say&消息=控制员+珀塔玛+小夜](http://yii2basic.test/index.php?r=tutorial/say&message=controller+pertama+saya)

Sampai sekarang kita telah melihat cara kerja controller beserta actionnya, serta hubunganya dengan view.

Umumnya controller pada framework mempunyai fungsi standar atau method (dalam istilah OOP) Create, Read Update dan Delete, disingkat dengan CRUD. Begitu pula dalam Yii2, jika kita membuat controller melalui gii (Tools bawaan Yii2) untuk membuat controller,akan langsun tersedia method CRUD pada controller kita.

Sekarang kita akan membuat "actionIndex" dalam controller yang telah kita buat sebelumnya,
sebagai contoh pada action index kita ingin menampilkan data dari dosen. Sekarang kita perlu memasukan kode di bawah ini, setelah Action Say yang sebelumnya telah kita buat.

```
 /**
     * Data di simpan dalam view
     * http://yii2basic.test/index.php?r=tutorial/list-dosen
     * http://yii2basic.test = domain yng digunakan
     * index.php = @app/Web/index
     * r = router yang mengarahkan ke Controller Tutorial
     * index = action atau function dalam tutorial
     */
    public function actionIndex()
    {
        return $this->render('index');
    } 
```

selanjutnya kita perlu membuat file dengan nama index.php di dalam folder view/tutorial

```
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
```

Action index ini merupakan action spesial, karena untuk mengaksesnya kita hanya perlu memasukan nama dari controllernya pada browser, tanpa memanggil nama methodenya, seperti kedua link di bawah ini :

[http://yii2basic.test/index.php?r=tutorial](http://yii2basic.test/index.php?r=tutorial)

[http://yii2basic.test/index.php?r=tutorial/index](http://yii2basic.test/index.php?r=tutorial/index)

hasilnya kedua link tersebut akan menamilkan halaman yang sama, dalam framework php pada umumnya jika kita tidak memasukan methode dari controller pada browser, router akan menganggap kita memanggil methode index.

#### Mengirim data dari Controller ke View

selanjutnya kita akan mencoba mengirimkan data dari Controller ke View, pada tutorial kali ini kita akan mengunnakan contoh dari action index tetapi, kita akan menyimpan data dari dosen di dalam controller. Untuk memulainya marikita buat function di dalam controller dengan nama dataDosen dibawah action index yang telah kita buat sebelumnya, lalu masukan kode berikut ini :

```
 /**
     * mengirim data dari controller ke view
     * Data di simpan dalam Controller
     * http://yii2basic.test/index.php?r=tutorial/daftar-dosen
     */
    public function actionDataDosen()
    {
        $listDosen = [
            [
                'id' => 1,
                'nama' => 'Prof. Dadang',
                'tanggal_lahir' => '1960-07-28'
            ],
            [
                'id' => 2,
                'nama' => 'Heri, ST, MM.',
                'tanggal_lahir' => '1959-09-01'
            ],
            [
                'id' => 3,
                'nama' => 'Amel',
                'tanggal_lahir' => '1969-07-20'
            ]
        ];
        return $listDosen;
    } 
```

Selanjutnya kita akan membuat action pada controller yang sama tetapi dengan nama action "actionDaftarDosen", lalu masukan kode di bawah ini :

```
 public function actionDaftarDosen()
    {
        $dosen = $this->actionDataDosen();
        return $this->render('daftarDosen', [
            'dosen' => $dosen
        ]);
    } 
```

Pada method actionDaftarDosen di atas, method mengambil data dari method sebelumnya untuk dimasukan ke dalam variable

*$dosen = $ this- > actionDataDosen();*

kemudian variabel dosen diteruskan oleh action ke view dengan perintah

*return $this- > render('daftarDosen', [ 'dosen' = > $dosen ]);*

Seperti sebelumnya, kita perlu membuat file daftarDosen.php di dalam folder View/tutorial, lalu masukan kode seperti pada view index tetapi tanpa memasukan data array, seperti pada contoh di bawah ini :

```
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
```

#### Kesimpulan

Pada bahasan ini kita telah mempelajari tentang hubungan antara controler dan view. Secara sederhana Controller dan View terhubung oleh kesamaan nama controler dengan nama folder yang terdapat dalam view. Seperti pada pembahasan kita TutorialController yang terdapat dalam folder Controllers berarti memiliki view dengan nama folder tutorial yang terletak di dalam folder view. Dan action di dalam controller terhubung dengan file php yang terapat di dalam folder view/tutorial misalnya actionIndex terhubung dengan file index di dalam folder view/tutorial.

> Aturan dasar dalam penamaan controler diawali dengan huruf besar pada tiap kalimatnya, lalu diikuti dengan suffix controller, misalnya :

DosenController, MahasiswaArsitekturController, dan lan-lain.

> Sedangkan penamaan dari method atau function diawali dengan prefix action lalu diikuti dengan nama dari action yang diawali huruf besar, misalnya :

actionNilai, actionDaftarMahasiswa, actionJadwalKuliah, dan lain-lain.

> Untuk penamaan file view diawali dengan huruf kecil diikuti huruf besar pada kalimat selanjutnya tanpa spasi, misalnya :

nilai.php, daftarMahasiswa.php, jadwaKuliah.php, dan lain-lain.

Sebelum kita membahas tentang model, pada part setelah ini kita akan membahas :

*   membagi konten menjadi beberapa view (render partial).
*   membuat halaman statik (static pages).
*   berbagi data antara vew dengan layout.