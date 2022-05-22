# Praktikum 8

## Profil
| | Biodata |
| -------- | --- |
| **Nama** | Humam Fathurrahman |
| **NIM** | 312010389 |
| **Kelas** | TI.20.A2 |
| **Mata Kuliah** | Pemrograman Web |

## 1. Menjalankan MySQL Server

Untuk menjalankan MySQL Server dari menu XAMPP Control seperti berikut.

![MySql](img/sqlserver.png)

Mengakses MySQL Client menggunakan PHP MyAdmin
Pastikan webserver Apache dan MySQL server sudah dijalankan. Kemudian buka melalui browser: http://localhost/phpmyadmin/

## 2. Membuat Database dan Tabel

### DATABASE
```sql
CREATE DATABASE latihan1;
```

### TABEL
```sql
CREATE TABLE data_barang (
    id_barang int(10) auto_increment Primary Key,
    kategori varchar(30),
    nama varchar(30),
    gambar varchar(100),
    harga_beli decimal(10,0),
    harga_jual decimal(10,0),
    stok int(4)
);
```

Hasilnya seperti berikut.
![Database](img/db.png)

## 3. Menambahkan Data

```sql
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5), ('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5), ('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```

Hasilnya seperti berikut.
![InsertData](img/insert.png)

## 4. Membuat Program CRUD

1. Membuat folder `Lab8web` di direktori htdocs
2. Lalu buat file dengan nama `lab8_php_database` , kemudian tambahakan seperti berikut.

![MembuatFolder](img/folderr.png)

Kemudian untuk mengakses direktori tersebut pada web server dengan mengakses URL: http://localhost/Lab8web/lab8_php_database/

![Web](img/web.png)

## 5. Membuat file koneksi database
Buat file dengan nama `koneksi.php`
lalu tambahkan kode seperti berikut.
```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";
$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false){
    echo "Koneksi ke server gagal.";
    die();
}   echo "Koneksi berhasil";
?>
```

Buka melalui browser untuk menguji koneksi database (untuk menyampilkan pesan koneksi berhasil, uncomment pada perintah echo “koneksi berhasil”; 
Maka hasilnya seperti berikut.

![Koneksi](img/koneksi.png)

## 6. Membuat file index untuk menampilkan data(Read)
Buat file dengan nama `index.php`
Lalu tambahkan kode berikut.
```php
<?php
include("koneksi.php");

// query untuk menampilkan data
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);

?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="style.css" type="text/css" />
    <title>Data Barang</title>
</head>
<body>
    <div class="container">
        <h1>Data Barang</h1>
        <div class="main">
            <a href="tambah.php">Tambah Barang</a>
            <table>
            <tr>
                <th>Gambar</th>
                <th>Nama Barang</th>
                <th>Kategori</th>
                <th>Harga Jual</th>
                <th>harga Beli</th>
                <th>Stok</th>
                <th>Aksi</th>
            </tr>
            <?php if($result): ?>
            <?php while($row = mysqli_fetch_array($result)): ?>
            <tr>
                <td><img src="gambar/<?=$row['gambar'];?>" alt="<?=$row['nama'];?>"></td>
                <td><?= $row['nama'];?></td>    
                <td><?= $row['kategori'];?></td>    
                <td><?= $row['harga_beli'];?></td>    
                <td><?= $row['harga_jual'];?></td>    
                <td><?= $row['stok'];?></td>    
                <td><?= $row['id_barang'];?></td>    
            </tr>
            <?php endwhile; else: ?>
            <tr>
                <td colspan="7">Belum ada data</td>
            </tr>
            <?php endif; ?>
            </table>
        </div>
    </div>
</body>
</html>
```

Maka hasilnya seperti berikut.

![Index](img/data(read).png)

## 6. Menambahkan Data (Create)

Buat file dengan nama `tambah.php`
Lalu tambahkan kode berikut.

```php
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit']))
{
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_POST['file_gambar'];
    $gambar = null;
    if ($file_gambar['error'] == 0)
    {
        $filename = str_replace(' ', '_',$file_gambar['name']);
        $destination = dirname(__FILE__) .'/gambar/' . $filename;
        if(move_uploaded_file($file_gambar['tmp_name'], $destination))
        {
            $gambar = '/gambar/' . $filename;;
        }
    }
    $sql = 'INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) ';
    $sql .= "VALUE ('{$nama}', '{$kategori}','{$harga_jual}', 
    '{$harga_beli}', '{$stok}', '{$gambar}')";
    $result = mysqli_query($conn, $sql);
    header('location: index.php');
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="style.css" type="text/css" />
    <title>Tambah Barang</title>
</head>
<body>
<div class="container">
    <h1>Tambah Barang</h1>
    <div class="main">
        <form methode="post" action="tambah.php" enctype="multipart/form-data">
            <div class="input">
                <label>Nama Barang: </label>
                <input type="text" name="nama" />
            </div>
            <div class="input">
                <label>Kategori: </label>
                <select name="kategori">
                    <option value="">~Pilih~</option>
                    <option value="Komputer">Komputer</option>
                    <option value="Elektronik">Elektronik</option>
                    <option value="Hand Phone">Hand Phone</option>
                </select>
            </div>
            <div class="input">
                <label>Harga Jual: </label>
                <input type="text" name="harga_jual">
            </div>
            <div class="input">
                <label>Harga Beli: </label>
                <input type="text" name="harga_beli">
            </div>
            <div class="input">
                <label>Stok: </label>
                <input type="text" name="stok">
            </div>
            <div class="input">
                <label>File Gambar: </label>
                <input type="file" name="file_gambar">
            </div>
            <div class="submit">
                <input type="submit" name="submit" value="Simpan" />
            </div>
        </form>
    </div>
</div>
</body>
</html>
```

Maka hasilnya seperti berikut.

![Create](img/tambahbrg.png)

## 7. Mengubah Data (Update)

Buat file dengan nama `ubah.php`

```html

```


