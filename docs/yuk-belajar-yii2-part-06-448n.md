# Yuk Belajar Yii2 第 6 部分

> 原文：<https://dev.to/farid_aditya/yuk-belajar-yii2-part-06-448n>

Pada diskusi kita kemarn kita membahas bagaimana cara membuat class dari Model, Cotroller dan View menggunan fasilitas dari **Yii** yang bernama **Gii** , pada diskusi kita kali ini kita akann membahas hasil dari Gii Generator dan mengupdate code yang dihasilkan Gii sesuai kebutuhan kita.

#### 型号

Sebenarnya kita pernah membahas model pada bagian lain dari seri ini, saat ini akan kita bahas kembali agar memperjelas pemahaman kita tentang model, dan juga kerena moleh yang kita buat sekarag di create melalui gii.

Model pertama yang akan kita bahas adalah **Role.php** , Hasil Code Generator dari Gii adalah seperti :

```
<?php

namespace app\models;

use Yii;

/**
 * This is the model class for table "role".
 *
 * @property int $id
 * @property string $role_akses
 * @property string $nama
 *
 * @property User[] $users
 */
class Role extends \yii\db\ActiveRecord
{
    /**
     * {@inheritdoc}
     */
    public static function tableName()
    {
        return 'role';
    }

    /**
     * {@inheritdoc}
     */
    public function rules()
    {
        /* off */
        return [
            [['role_akses', 'nama'], 'required'],
            [['role_akses'], 'integer'],
            [['nama'], 'string', 'max' => 45],
        ];
    }

    /**
     * {@inheritdoc}
     */
    public function attributeLabels()
    {
        return [
            'id' => 'ID',
            'role_akses' => 'Role Akses',
            'nama' => 'Nama',
        ];
        /* on */
    }

    /**
     * @return \yii\db\ActiveQuery
     */
    public function getUsers()
    {
      return $this->hasMany(User::className(), ['role_id' =>'id']);
    }
} 
```

*   **namespace** , telah kita bahas dalam diskusi sebelumnya, secara sederhana namespace adalah URI atau lokasi tempat class berada. pada kasus ini, dapat dibaca seperti class ini berada di app/models.

*   **use** , secara sederhana dapat diartikan deklarasi class lain yang digunakan pada class ini.

*   **class Role extends \yii\db\ActiveRecord** , adalah deklarasi dari class ini, yang merupakan inheritance dari class ActiveRecord. Deklarasi ini bisa juga didekarasikanseperti **class Role extend ActiveRecord** , dengan catatan kita harus mendeklarasikan ActiveRecord dapa Use . Yang perlu diperhatikan adalah Gii secara default mebuat model yang merupakan inheritance dari ActiveRecord.

*   **public static function tableName()** , mendeklarasikan/meberitahukan model bahwa dalam model ini kita menggunakan tabel role dari database.

*   **public function rules()** , ini adalah methode yang penting karena dalam method ini kita mendeklarasikan rule dari validasi yang digunakan ketika method validation() dijalankan. Gii mengcreate fungsi dari rule sesuai dengan validasi dan tipedata kita di database.

**[['role_akses', 'nama'], 'required'],** artinya role_akses dan nama tidak boleh kosong. required sama seperti NotNull atau tudak boleh kosong.

**[['role_akses'], 'integer'],** artinya role_akses harus bertipe integer.

**[['nama'], 'string', 'max' = > 45],** string artinya input harus berupa string, max= > 45 artinya panjang input maximal adalah 45 karakter.

> untuk rule lengkapnya bisa di lihat di link di bawah ini : [https://www.yiiframework.com/doc/guide/2.0/en/input-validation](https://www.yiiframework.com/doc/guide/2.0/en/input-validation)

*   **public function attributeLabels()** , merepresentasikan nama field ketika digunaan dalam form, Grid view atau lainya. misalnya **'role_akses' = > 'Role Akses'** , kira kira akan menghasilkan role_akses ketika di form input labelnya akan menjadi Role Akses.

*   **public function getUsers()** , adalah method yangg menghubungkan, model role dengan model User, dengan penghubung berupa :

**return $ this->has many(User::class name()，[' role _ id ' =>' id '])；**

> 卡塔坦
> 
> hasMany dan hasOne merupakan method dari class **baseActiveRecord** yang merupakan parent class dari **ActiveRecord**

HasMany dan HasOne adalah Relation antar tabel dalam database. (User::className(), ['role_id' => 'id']) dapat diartikan method terhubung ke model User dengan foreinKey role_id.

Model selanjutnya yang akan kita bahas adalah model **User.php** , model ini sebenarnya tidak kita create dengan Gii, tetapi model ini sudah di sediakan oleh Yii2,umumnya digunakan untuk Autentication dan/atau Autorization. Untuk sekarang kita hanya akan tentang link model ini dengan model lain pembahasan lebihlanjut tentang User model akan di bahas di bagian lain ketika kita membahas **Autentication dan Autorization** .

Dari class diagram pada [part04](https://dev.to/farid_aditya/yuk-belajar-yii-part-4-19f1) dari seri ini, kita melihat bahwa model user terhubung dengan model Role, Dosen dan Mahasiswa oleh karena itu kita akan menambahkan code berikut pada bagian paling bawah dari class :

```
 public function getDosen()
    {
        return $this->hasMany(Dosen::className(), [
            'user_id' => 'id'
        ]);
    }

    public function getMahasiswa()
    {
        return $this->hasMany(Mahasiswa::className(), [
            'user_id' => 'id'
        ]);
    }

    public function getRole()
    {
        return $this->hasOne(Role::className(), [
            'id' => 'role_id'
        ]);
    } 
```

pada code diatas kita mendeklarasikan method **getDosen()** yang menghubungkan relasi antara **User.php** dan **Dosen.php** kemudian ada **getMahasiswa()** dan **getRole()** yang melakuka mirip seperti getDosen().

Yang ingin ditegaskan dalam bagian ini adalah :

```
public function getRole()
 {
     return $this->hasOne(Role::className(), [
     'id' => 'role_id'
     ]);
 } 
```

Jika tadi dalam model Role.php kita melihat **return $this- > hasMany (User::className(), ['role_id' = > 'id']);** maka pada model ini kita mendeklarasikan sebaliknya, sehingga relasi **One To many** terpenuhi. (Karena setahu saya Yii hanaya mengenal HasOne dan HasMany).

Selanjutnya pada deklarasi method pada **getUsers()** yang dicreate oleh Gii akan berupa Jamak karena secara naturalnya Role mempunyai banyak User, Mungkin ini hambatan bahasa kita maka, agar lebih bersahabat dengan bahasa kita kita akan rubah menjadi **getUser()** .

Untuk model lainya tidak akan kita bahas secara detail karena penjelasanya akan mirip seperti kedua penjelasandi atas, tetapi, Sebaiknya kita perhatikan tiap modelnya dan kita ubah fungsi yang memiliki relasi HasMany menjadi bentuk Singular (tunggal). Misalnya seperti **GetUsers()** mejadi **GetUser()** .

> Beberapa Framework sangat ketat dalam aturan penamaan (naming conventions), biasanya aturan ini diterapkan pada model dan controllernya. misalnya : CakePhp, FuelPHP atau yang lainya. Yii juga menerapkanya, tetapi tidak ketat, jadi kita dengan mudah dapat membuat nama class sesuai yang kita mau, meskipun masih lebih baik mengikuti aturan yang mengikuti standar.
> 
> untuk mengetahui sabagian dari aturan aturan ini kita bisa melihat melalui link ini [https://www.php-fig.org/](https://www.php-fig.org/)

#### 控制器

Untuk sedikit membedakan dengan pembahasan model, Controller pertama yang akan kita bahas adalah **RuangKelasController.php** , alasanya seserhana saja karena model dari **ruangkelasContoller.php** sama seperti **RoleController.php** tidak bergantung pada model lain.

```
<?php
namespace app\controllers;

use Yii;
use app\models\RuangKelas;
use yii\data\ActiveDataProvider;
use yii\web\Controller;
use yii\web\NotFoundHttpException;
use yii\filters\VerbFilter;

class RuangKelasController extends Controller
{

    public function behaviors()
    {
        return [
            'verbs' => [
                'class' => VerbFilter::className(),
                'actions' => [
                    'delete' => ['POST'],
                ],
            ],
        ];
    }

    public function actionIndex()
    {
        $dataProvider = new ActiveDataProvider([
            'query' => RuangKelas::find(),
        ]);

        return $this->render('index', [
            'dataProvider' => $dataProvider,
        ]);
    }

    public function actionView($id)
    {
        return $this->render('view', [
            'model' => $this->findModel($id),
        ]);
    }

    public function actionCreate()
    {
        $model = new RuangKelas();

        if ($model->load(Yii::$app->request->post()) && $model->save()) {
            return $this->redirect(['view', 'id' => $model->id]);
        }

        return $this->render('create', [
            'model' => $model,
        ]);
    }

    public function actionUpdate($id)
    {
        $model = $this->findModel($id);

        if ($model->load(Yii::$app->request->post()) && $model->save()) {
            return $this->redirect(['view', 'id' => $model->id]);
        }

        return $this->render('update', [
            'model' => $model,
        ]);
    }

    public function actionDelete($id)
    {
        $this->findModel($id)->delete();

        return $this->redirect(['index']);
    }

    protected function findModel($id)
    {
        if (($model = RuangKelas::findOne($id)) !== null) {
            return $model;
        }

        throw new NotFoundHttpException('The requested page does not exist.');
    }
} 
```

Kode di atas sama dengan kodeyang dihasilkan oleh Gii, hanya untuk kenyamanana diskusi kita, saya menghapus barsi komentarnya.

sama seperti model kita juga pernah menbahas Controller pada bagan lain dari seri ini. Disini kita akan ulangi lagi sedikit tentang Controller.

*   **use** , merupakan deklarasi dari class lain yang digunakan oleh controller.

Pada bagian deklaras perhatikan bagan **use app\models\RuangKelas;** ini artinya kita mendeklarasikan/memanggil/menggunakan model **RuangKelas.ph** p yang sebeumnyatelah kita create.

*   **class RuangKelasController extends Controller** , ini adalah part deklarasi dari class. penamaanya sesuai dengan mana file. Umumnya class controller merupakan class turunan dari Controller.php.

*   **public function behaviors()** dan **'class' = > VerbFilter::className()** , penjelasan sederhanya, behaviors sesuai dengan artinya yaitu kebiasaan. Method bahaviors adalah method yang akan mempengaruhi kebiasaa yang akan dilakukan class ini. Jika kita melihat dari sudut pandang yii secara teknis method behaviors adalah instance dari **yii/base/behaviors** , yang berfungsi untuk meningkatkan fungsi dari component tanpa menggangu/merubah class parent atau inheritancenya.

Filter Merupakan object yang bekerja seperti BeforAction atau AfterAction pada beberapa framework lain. sederhananya sebelum atau sesudah action dari class, kasus ini class ruangKelas.php. VerbFilternya sendiri adalah objek yang methodnya membaca izin dari **method Request HTTP Action** , apakah diizinkan atau tidak jika tidak akan mengembalikan nilai **HTTP 405 exception** .

Jadi fungsi dari method ini sederhananya seperti menambah kebiasaan dari class agar selalu mengecek method dari request actionya Apakah get, post, put dan lain-lain jika ditolah maka akanmengembalikannilai error.

Untuk melihatnya masuk ke [http://yii2campus.test/ruang-kelas](http://yii2campus.test/ruang-kelas) kemudian buat sebuah record dari ruang kelas, submit, kemudian delete lagi. setelah itu coba ganti action verbnya menjadi :

```
 'verbs' => [
                  'class' => VerbFilter::className(),
                  'actions' => [
                      'delete' => ['get'],
                  ],
              ], 
```

kemudian buat record kembali, submit, kemudian delete.

*   Method lainya bekerja sesuai nama actionya. da akankita bahas pada diskusi lainya.

#### 视图

Pada bagian view ini kita tidak akan membahas detail tentang View, karena kita sedikitnya sudah pernah berdiskus tentang view pada [part 3](https://dev.to/farid_aditya/yuk-belajar-yii2-part-3-2f7k) . Selain untuk meghindari diskusi yang redundant, rasaya lebih nyaman membahas view ketika secara bersamaan ketika kita mencoba firtur fitur dari Yii2 lebih dalam lagi.

#### Kesimpulan

Pada diskusi kali ini kita hanya membahas sedikit tentang hasil code generate dari Gii, dan sedikit mengubah kode agar sesuai keperluan diskusi kita kedepanya. Bahasan selanjutnya kita akan mencoba ActiveRecord, dan beberapa fitur lain yang masih berhubungan.

> Sedikit Catatan dari saya
> 
> Mohon Maaf jika dalam seri ini alurnya terasa lambat, karena saya harus meluangkan waktu dengan kegiatan rutin saya.
> 
> Terima Kasih Atas Pengertianya.