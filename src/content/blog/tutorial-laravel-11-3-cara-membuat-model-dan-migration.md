---
title: 'Tutorial Laravel 11 #3 Membuat Model dan Migration'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semua, Pada blog sebelumnya kita sudah belajar banyak hal, seperti mengginstall laravel 11, menjalankanya di localhost, dan kita juga suda mengkonfigurasi koneksi database.

Pada blog kali ini kita akan belajar cara membuat model dan migration di dalam laravel 11.
###### Apa itu Model ? #####
>  Model dalam Laravel adalah **representasi dari tabel database**. Dengan menggunakan model, pengembang dapat melakukan operasi-operasi database seperti membuat, membaca, mengupdate, dan menghapus data dengan sangat mudah dan efisien.
###### Apa itu Database Migration ?
> Migration adalah sebuah version control database, dimana akan membantu team untuk mengubah dan membagikan sebuah schema database dari project yang dibangun. jika sebelumnya temen temen biasanya membuat table-table secara manual di dalam database, maka dengan migration hal itu sudah tidak diperlukan lagi.

## Langkah 1 - Membuat Model dan Migration
Nah untuk membuat Model dan Migration, Silahkan temen-temen semua buka terminal/CMD atau terminal editor temen-temen pastikan sudah berada di dalam folder laravelnya.
```php
php artisan make:model Komik -m
```
Jika perintah di atas berhasil, maka kita akan mendapatkan 2 file baru yang berada di dalam folder
1. app/models/Komik.php
2. database/migrations/migrations/2024_07_06_225101_create_komiks_table.php

> **INFORMASI** : nama file Migration akan random sesuai tanggal temen temen membuatnya.

## Langkah 2 - Menambahkan field / kolom di dalam migration
Setelah file migration berhasil dibuat, maka kita akan menambahkan field atau kolom ke dalam file migration-nya. silahkan temen-temen buka file-nya, kemudian dalam function up tambahkan kode menjadi seperti berikut.
```php
 public function up(): void
    {
        Schema::create('komiks', function (Blueprint $table) {
            $table->id();
            $table->string('foto');
            $table->string('judul');
            $table->string('sinopsis');
            $table->string('penulis');
            $table->timestamps();
        });
    }
```
Dari tambahan kode di atas, kita menambahkan 4 kolom, yaitu:

| tipe data | field/kolom |
| --------- | ----------- |
| string    | foto        |
| string    | judul       |
| string    | sinopsis    |
| string    | penulis     |
## Langkah 3 - Menambahkan Mass Assignment
Mass Assigment merupakan property yang digunakan untuk menginzinkan sebuah field/kolom dari table agar dapat dimanipulasi kita bisa membuat mass assigment dengan properti $fillable di dalam model Komik. Silahkan temen temen buka file app/models/Komik.php kemudian tambahkan kode berikut ini.
```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;


class Komik extends Model

{
    use HasFactory;
    protected $fillable = ['foto', 'judul', 'sinopsis', 'penulis'];
}
```
Di atas kita menambahkan property $fillable dengan tipe data array dan di dalamnya terdapat field/kolom yang sudah kita buat sebelumnya di file migration.

## Langkah 4 - Menjalankan Proses Migrate
Sekarang kita akan belajar bagaimana menjalankan proses migrate, nah sebelumnya temen temen sudah tau bukan apa itu migrate karena di blog sebelumnya kita sudah menggunakanya tetapi kita belum tau fungsinya. Jadi fungsi Proses Migrate adalah untuk memasukan table beserta field/kolom-nya kedalam database.

Silahkan temen temen jalankan perintah berikut ini di dalam terminal/CMD dan pastikan sudah berada di dalam folder projectnya.
```php
php artisan migrate
```
karena kita sudah pernah menggunakanya maka tidak akan muncul lagi pertanyaan. dan dia akan langsung men-generate.
![Alt text](/tutorial-laravel-11-3/migrate.png)
Jika Proses Migrate-nya berhasil temen temen bisa cek pada PhpMyAdmin di http://localhost/phpmyadmin, table-table beserta field/kolom-nya akan masuk kedalam database.
![Alt text](/tutorial-laravel-11-3/dtbe-migrate.png)

## Kesimpulan
Nah Sekarang kita sudah membuat Model dan Migration di dalam laravel 11 dan kita juga sudah belajar menambahkan field/kolom, dan mass assignment beserta menjalankan proses migrate.

pada blog selanjutnya, kita akan belajar bagaimana menampilkan data dari database di dalam laravel 11.

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-2-cara-menginstal-dan-menjalankan-laravel-11">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-4-menampilkan-data-dari-database">Next</a>
  </ul>
</div>