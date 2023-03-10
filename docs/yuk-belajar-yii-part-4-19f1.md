# Yuk Belajar Yii2 第 4 部分

> 原文：<https://dev.to/farid_aditya/yuk-belajar-yii-part-4-19f1>

#### 型号

Model dalam mvc adalah bagian yang berhubungan dengan data, jadi model adalah class yang langsung berhubungan dengan data, dari mulai pencariaan data, validasi, penyimpanan data dan lain lain.

***Dari dokumen resmi Yii2***

> 模型是 MVC 架构的一部分。它们是表示业务数据、规则和逻辑的对象。
> 
> 你可以通过扩展 [yii\base\Model](http://yii-docs-2.0-en.test/yii-base-model.html) 或者它的子类来创建模型类。基类 [yii\base\Model](http://yii-docs-2.0-en.test/yii-base-model.html) 支持许多有用的特性:
> 
> *   [属性](http://yii-docs-2.0-en.test/guide-structure-models.html#attributes):表示业务数据，可以像普通对象属性或数组元素一样访问；
> *   [属性标签](http://yii-docs-2.0-en.test/guide-structure-models.html#attribute-labels):指定属性的显示标签；
> *   [大规模赋值](http://yii-docs-2.0-en.test/guide-structure-models.html#massive-assignment):支持单步填充多个属性；
> *   [验证规则](http://yii-docs-2.0-en.test/guide-structure-models.html#validation-rules):根据声明的验证规则确保输入数据；
> *   [数据导出](http://yii-docs-2.0-en.test/guide-structure-models.html#data-exporting):允许模型数据以可定制格式的数组形式导出。
> 
> `Model`类也是更高级模型的基类，比如[主动记录](http://yii-docs-2.0-en.test/guide-db-active-record.html)。有关这些高级模型的更多详细信息，请参考相关文档。

Dalam Yii class model yang kita buat adalah class yang merupakan inheritance dari **yii\base\model** dalam implementasinya biasa ditulis dengan :

```
class NamaClass extends model {} 
```

Untuk membahas model, sekaligus agar kita konsisten dengan contoh sebelumnya. Juga agar kita konsisten dengan latihan latihan kita ke depanya, kita akan membahasnya melalui contoh kasus.

Contoh kasus yang kan kita gunakan adalah sistem perkuliahan sederhana, seperti dalam diagram class di bawah ini :

[![5d37d81d7a84512404](img/f1cd5176075f1a407dcdafcb7647a3f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CEHMelcF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.loli.net/2019/07/24/5d37d81d7a84512404.png)

亚松森·阿瓦勒:

*   Agar lebih mempermudah kita dan tetap terfokus pada Yii2, latihan kita tidak membahas Jurusan, Angkatan, kelas dan lain lain.
*   User model adalah model yang berhubungan dengan identity Class
*   **Dosen** Dan **Mahasiswa** adalah detail dari **User** model.
*   **Dosen** boleh mengajar banyak **Matakuliah** , Tetapi satu **Matakuliah** hanya boleh di pegang satu dosen.
*   **Mahasiswa** boleh mempunyai banyak **Jadwal** , dan satu **Jadwal** boleh diisi oleh banyak **Mahasiswa** . jadi kita akan memakai model **JadwalMahasiswa** untuk menghubungkan model **Mahasiswa** dengan **Jadwal** .
*   Satu **Matakuliah** bisa mempunyai banyak **Jadwal** , satu **Jadwal** hanya diisi satu **Matakuliah** .
*   Satu **Jadwal** hanya boleh mengisi satu **RuangKelas** , satu **RuangKelas** bisa diisi banyak **Jadwal** .

Jika pada latihanlatihan kita sebelumnya kita memakai project **yii2basic** maka untuk lathan ini kita akan membuat project baru, disini kita akan menamakan **yii2campus** . untuk instalasi dan lainya silahkan liat referensi dari [https://www.yiiframework.com/doc/guide/2.0/en](https://www.yiiframework.com/doc/guide/2.0/en)

#### Membuat Model Pertama

Pada contoh ini kita akan membuat model Dosen (Agar lebih konsisten dengan cotoh kita sebelumnya) model bekerja memvalidasi input dari pengguna.

Pertama kita perlu membuat model file dengan nama **Dosen.php** dalam folder **models** , kemudian masukan kode di bawah ini :

> jika dalam kode ada coment block seperti /* on */ atau /* * off */ abaikan saja karena itu fungsi custom Eclipse IDE untuk menghiraukan auto format.

```
<?php
namespace app\models;

use Yii;

class Dosen extends \yii\db\ActiveRecord
{

    /* off */
    /* property dari model dosen */
    public $id;
    public $nama;
    public $tanggal_lahir;
    public $alamat_rumah;
    public $foto;
    public $file_foto;
    public $user_id;
/* on */

    /**
     * methode ini tidak diperlukan dalam coding,
     * tetapi berguna untul menampilkan label ke pengguna akhir
     */
    public function attributeLabels()
    {
        return [
            'id' => 'ID',
            'nama' => 'Nama',
            'tanggal_lahir' => 'Tanggal Lahir',
            'alamat_rumah' => 'Alamat Rumah',
            'foto' => 'Foto',
            'user_id' => 'User ID'
        ];
    }

    /**
     * aturan validasi model Dosen
     */
    public function rules()
    {
        return [
            /* off */
            [ [  'id','nama', 'tanggal_lahir' ],
                'required'
            ],
            [ [ 'id', 'user_id' ],
                'integer'
            ],
            [ [ 'nama', 'tanggal_lahir', 'alamat_rumah' ],
                'string',
                'max' => 45
            ],
            [ [ 'foto' ],
                'string', 
                'max' => 145
            ],
            [ [ 'id' ],
                'unique'
            ],
        ];
        /* on */
    }
} 
```

科特兰根:

*   **namespace** digunakan untuk mengatur nama menjadi deklaratif dalam mengatur file menjadi group logis untuk mencegah kekacauan file karena nama yang sama antara file. simpelnya name space seperti nama folder tempat file disimpan.

*   **use** digunakan untuk mendeklarasikan class objek lain yang digunakan dalam class kita.

*   **class Dosen extends \yii\db\ActiveRecord** adalah tag pembuka dari class yang kita bangun. **class** mendeklarasikan class, **Dosen** adalah nama atu ID dari class tersebut, **extend** menyatakan kelas ini merupakan inheritance dari class **ActiveRecord** . kemudian **\yii\db\ActiveRecord** bentuk lain dari deklarasi **use** , jika kita deklarasikan **use \yii\db\ActiveRecord** maka deklaras class bisa di buat seperti **class Dosen extends ActiveRecord** karena kita sudah memberitahukan class bahwa kita menggunakan Active pada deklarasi **use** .

*   **public function attribute labels()**merupakan 方法 yang kita 实现 dari 方法 pada kelas【Model.php】T3】kita bisa Meng 重写方法 terse but karena**T5】merupakan 继承 Dari**Model.php**。**

**function attrbuteLabels ()** sendiri berduna untuk merubah nama property (mudahnya variabel) dari class kita menjad nama yang lebih mudah dibaca end user. misalnya **user_name menjadi Nama Pengguna** atau **id menjadi Nomor Induk Dosen** , dan lain lain.

*   **public function rules()**sama sep erti**public function attribute labels()**merupakan 方法 yang kita 实现 dari 类【Model.php】。

**function rules()** berfungsi untuk menmvalidasi data yang di input user sesuai dengan aturan yang kia tetapkan. misalnya

**[ [ 'id','nama', 'tanggal_lahir' ], 'required' ]** artinya field Id, nama, dan tanggl_lahir tidak boleh kosong. contoh lainya **[ [ 'id', 'user_id' ], 'integer' ]** field id dan user_id harus bertipe integer. referensi lengkap validator dapat dilihat di [https://www.yiiframework.com/doc/guide/2.0/en/tutorial-core-validators](https://www.yiiframework.com/doc/guide/2.0/en/tutorial-core-validators)

Setelah model di bangun kini saatnya kita membuat controller, seperti biasa kita membuat controler lainya, pertama kita membuat file **DosenController.php** dalam folder controllers, kemudian masukan kode :

```
<?php

namespace app\controllers;

use Yii;
use yii\web\Controller;
/* model yang dipakai  */
use app\models\Dosen;

/**
 * DosenController implements the CRUD actions for Dosen model.
 */
class DosenController extends Controller
{
    public function actionCreate()
    {
        $model = new Dosen();
        $modelCanSave = false;

        if ($model->load(Yii::$app->request->post()) && 
        $model->validate()) {
            $modelCanSave = true;
        }

        return $this->render('create', [
            'model' => $model,
            'modelCanSave' => $modelCanSave
        ]);
    }
} 
```

*   Tidak ada perbedaan dengan controller sebelumnya yang pernah kita buat hanya saja dalam deklarasi **use** ada deklarasi **use app/models/Dosen** yang berguna untuk menyatakan bahwa kita menggunakan model **Dosen.php** dalam class ini.

*   **model- > load** , **model- > validate** berfungsi untuk memangil mehod dari class model dengan method sesuai nama fungsinya.

*   **Yii::$app- > request- > post()** , **Yi::app** secara sederhana merupakan entry script point yang dapat di akses secara global. request- > post sendiri memanggil method post dari class request. methodnya sendiri berhubungan dengan request parameter

*   **return $this- > render()** memanggil fungsi render.

*   **$model = new Dosen();** membuat instance dari Dosen dengan nama model.

*   **$modelCanSave** variable yang diinisilisasi dengan false.

selanjutnya kita membuat folder baru dalam view/dosen kemudian diisi dengan kode di bawah ini :

```
use yii\helpers\Html;
use yii\widgets\ActiveForm;
use yii\helpers\Url;
use yii\helpers\ArrayHelper;
?>

<?php if($modelCanSave) { ?>
<div class="alert alert-success">model siap disimpan!</div>
<?php } ?>

<?php $form = ActiveForm::begin(); ?>
<div class="row">
    <div class="col-lg-12">
        <h1>Room form</h1>
              <?= $form->field($model, 'id')->textInput() ?>
            <?= $form->field($model, 'nama')->textInput(['maxlength' => true]) ?>
            <?= $form->field($model, 'tanggal_lahir')->textInput(['maxlength' => true]) ?>
            <?= $form->field($model, 'alamat_rumah')->textInput(['maxlength' => true]) ?>
            <?= $form->field($model, 'foto')->textInput(['maxlength' => true]) ?>    
   </div>
</div>

<div class="form-group">
    <?= Html::submitButton('Create' , ['class' => 'btn btn-success']) ?>
</div>
<?php ActiveForm::end(); ?> 
```

Setelah semua beres di input, kita bisa mencoba untuk menjalankan di browser dengan URL [http://yii2campus.test/dosen/create](http://yii2campus.test/dosen/create) , kita bisa mencoba dengan input yang tidak sesuai dengan validasi. form field akan berubah menjadi merah tanda validasi gagal. jika semuanya berhasil view akan menampilkan pemberitahuan **model siap disimpan** .

Jika Kita merubah kode dibawah :

```
<div class="alert alert-success">model siap disimpan !</div> 
```

Dengan ini :

```
<div class="alert alert-success">
    Id Dosen : <?php echo $model->id; ?> <br />
    Nama: <?php echo $model->nama; ?> <br />  
    Tanggal Lahir: <?php echo Yii::$app->formatter->asDate($model->tanggal_lahir,'php:m/d/Y'); ?> <br />
    Alamat: <?php echo $model->alamat_rumah; ?> <br />  
      Foto : <?php echo $model->foto; ?> <br />
</div> 
```

Hasilnya akan menampilkan informasi yang kita telah kita masukan.

#### Kesimpulan

Pada part ini kita telah mempelajari tentang model, meskipun tidak menghubungkan model tersebut dengan database, tetapi kita telah melihat bahwa model buka hanya berhubungan dengan penyimpanan dan pencarian data. Di sini model kita gunakan untuk memvalidasi input pada form. Kita juga telah membuat Controller yang mengatur flow antara model dengan view.

pada part selanjutnya kita akan membahas gii, sebuah fitur ajaib yang disediakan oleh Yii.