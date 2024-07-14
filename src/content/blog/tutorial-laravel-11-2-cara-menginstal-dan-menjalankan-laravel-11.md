---
title: 'Tutorial Laravel 11 #2 Cara menginstal dan Menjalankan laravel 11'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Hallo semuanya pada blog pertama ini kita akan belajar bersama sama bagaimana cara membuat crud pada laravel 11.
## langkah 1 - menginstal Composer
Sebelum kita menginstall laravelnya kita harus menginstall dulu yang namanya composer temen temen, agar kita bisa  menginstall framework laravel.
Berikut cara untuk menginstall composer.
- Windows - https://getcomposer.org/download/
- Linux        - https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos
- MacOs     - https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos

> Composer adalah sebuah tools manajemen dependensi untuk bahasa pemrograman PHP.

Untuk memastikan bahwa Composernya telah di install temen temen silahkan buka CMD lalu jalankan perintah berikut.
``` 
composer
```

![Alt text](/tutorial-laravel-11-2/composer.png)

## Langkah 2 - Menginstal Laravel
Sekarang setelah kita menginstall composer kita akan menginstall framework laravelnya. Silahkan temen temen jalankan terlebih dahulu XAMPP / Laragon, Setalah itu silahkan temen temen masuk ke dalam folder dimana akan menyimpan project-nya.

Setelah itu, silahkan temen temen buka CMD/Terminal lalu tuliskan perintah berikut ini.
```php
composer create-project laravel/laravel laravel-crud
```
Perintah diatas, digunakan untuk membuat project laravel dengan versi terbaru yaitu laravel 11 dengan nama folder laravel-crud. Silahkan tunguin sampe instalasinya selesai dan pastikan terhubung dengan internet. 
![Alt text](/tutorial-laravel-11-2/instal-laravel.png)

## Langkah 3 - Menjalankan Laravel 11
Setelah proses instalasinya selesai, maka kita akan mencoba untuk menjalankanya di browser temen temen.
silahkan temen temen buka CMD/Terminal lalu jalankan perintah berikut ini.
```php
cd laravel-crud
```
Perintah diatas digunakan untuk masuk ke dalam folder laravel-crud.

Setelah berada di dalam folder laravel-crud silahkan tuliskan perintah berikut di CMD/Terminal.
```php
code .
```
Perintah diatas digunakan untuk masuk ke dalam Vscode atau editor temen temen.

![Alt text](/tutorial-laravel-11-2/editor.png)
Setelah berada di Editornya silahkan temen-temen buka file bernama .env kemudian cari kode berikut ini.
```php
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=
```
Lalu ubah kodenya menjadi seperti berikut ini.
```php
DB_CONNECTION=mysql
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel-crud
# DB_USERNAME=pai
# DB_PASSWORD=
```
Di atas, Pertama kita ubah DB_CONNECTION yang semula sqlite menjadi mysql.

kemudian kita hapus tanda # tujuanya agar variable-nya diaktifkan. Kemudian kita atur 
DB_DATABASE yang semula laravel menjadi laravel-crud.

Untuk DB_USERNAME saya ganti menggunakan pai namun secara default adalah root jadi temen temen gunakan saja root. Dan untuk DB_PASSWORD jika temen-temen menggunakan XAMPP / Laragon , maka di kosongkan saja.

Setelah kita mengkonfigurasi Koneksi Database langkah selanjutnya silahkan temen-temen jalankan perintah berikut di terminal editor temen-temen.
```php
php artisan migrate
```
Jika keluar pertanyaan seperti ini. Itu artinya kita belum memiliki database di dalam MySql, maka dari itu silahkan temen temen pilih yes agar dibuatkan databse dengan nama laravel-crud.![Alt text](/tutorial-laravel-11-2/migration.png)

Nah setelah itu silahkan temen temen jalankan perintah berikut di dalam CMD/terminal untuk menjalankan servernya.
```php
php artisan serve
```
Jika berhasil, Project laravel akan dijalankan di dalam localhost dengan port 8000. Temen-temen bisa membukanya di browser dengan menggetikan http://localhost:8000
jika berhasil, maka akan tampil seperti berikut ini.
![Alt text](/tutorial-laravel-11-2/laravel11.png)

## Langkah 4 - Menjalankan Storage:link

***Laravel*** itu hanya bisa mengakses file yang berada di dalam folder public, sedangkan file-file atau gambar yang akan kita upload akan berada di dalam folder storage

Terus bagaimana caranya agar ***Laravel***  bisa membaca file-file yang telah diupload tersebut ? jawabanya dengan menggunakan symlink atau storage link.

Dimana perintah storage link ini nantinya akan membuatkan sebuah link folder dari `storage` ke dalam folder `public`.

Dengan begini, maka *Laravel* akan bisa membaca isi di dalam folder storage melalui folder `public`.

Silahkan teman-teman sekalian jalankan perintah berikut ini di dalam terminal/CMD dan pastikan sudah berada di dalam project ***Laravel*** nya.
```php
php artisan storage:link
```
## Kesimpulan
Nah sekarang temen temen sudah bisa membuat project dan menjalankanya di browser kita juga sudah menjalankan Storage:link agar bisa mengupload gambar.

pada blog selanjutnya kita akan belajar membuat model dan migration di dalam laravel 11.

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-1-pengenalan-laravel/">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-3-cara-membuat-model-dan-migration">Next</a>
  </ul>
</div>