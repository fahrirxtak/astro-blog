---
title: 'Tutorial Laravel 11 #9 Membuat Kolom Baru Dan Membuat Seeders'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semuanya, pada blog kali ini kita akan menambahkan fitur proses login ke dalam applikasi crud sederhana kita.

## Langkah 1 - Menambahkan Kolom baru ke database
Sebelum kita membuat proses login, kita akan menambahkan kolom dengan nama role yang berfungsi untuk menentukan peran atau level akses yang dimiliki oleh pengguna..

Silahkan temen-temen buka project laravel-nya kemudian tuliskan perintah seperti berikut ini di dalam terminal/CMD temen-temen.
```php
 php artisan make:migration add_role_to_users_table --table=users
```
Perintah di atas di gunakan untuk menambahkan kolom role ke dalam table users, Menggunakan laravel.

Kemudian setelah kita membuat migration-nya, langkah selanjut-nya adalah temen-temen tuliskan perintah berikut di dalam folder 
database/migrations/2024_07_10_211744_add_role_to_users_table.php.
```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::table('users', function (Blueprint $table) {
			 $table->enum('role', ['admin', 'user'])->default('user');
        });
    }

    /**

     * Reverse the migrations.

     */

    public function down(): void
    {
        Schema::table('users', function (Blueprint $table) {

            //

        });

    }

};
```
Pada code di atas kita menambahkan perintah ke dalam ``function up()`` yang fungsinya  untuk menambahkan kolom role dengan tipe data enum, dan memasukanya dalam table users.
```php
 public function up(): void
    {
        Schema::table('users', function (Blueprint $table) {
			 $table->enum('role', ['admin', 'user'])->default('user');
        });
    }
```
Setelah kita berhasil membuat field atau kolom role, langkah selanjutnya silahkan temen-temen jalankan perintah berikut ini di dalam terminal/CMD.
```php
php artisan migrate
```
Perintah di atas digunakan untuk men-generate atau memasukan kolom baru ke dalam database.

Nah setelah kita memasukan kolom role kedalam database, langkah selanjut-nya adalah kita menambahkan kolom role ke dalam $fillable yang berada di `app/Models/User.php` , silahkan tulsikan perintah berikut ini.
```php
  protected $fillable = [
        'name',
        'email',
        'password',
        'role',
    ];
```
## Langkah 2 - Membuat Seeder
Nah setelah temen-temen membuat kolom/field baru, sekarang kita akan membuat Seeder yang digunakan untuk mengisi basis data dengan data dummy untuk pengembangan dan pengujian.

>INFORMASI : Dummy dalam konteks pengembangan perangkat lunak merujuk pada data palsu atau data yang dibuat secara acak untuk tujuan pengujian atau demonstrasi.

Silahkan temen-temen tuliskan perintah berikut ini di dalam terminal/cmd pastikan temen-temen berada di dalam project-nya.
```php
php artisan make:seeder DummyUsersSeeder
```
Jika temen-temen menjalankan perintah di atas maka kita akan mendapatkan satu file yang berada di falam folder database/seeders/DummyUsersSeeder.

Silahkan temen-temen buka file seeders yang sudah kita buat di dalam folder `database/seeders/DummyUsersSeeder`, kemudian tuliskan perintah berikut ini.
```php
<?php
  
namespace Database\Seeders;
  
use App\Models\User;
use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;

  
class DummyUsersSeeder extends Seeder
{
    /**

     * Run the database seeds.

     */

    public function run(): void
    {
        $userData = [
            [
                'name'     => 'Admin',
                'email'    => 'Admin@gmail.com',
                'role'     => 'admin',
                'password' => bcrypt('admin123'),
            ],
            [
                'name'     => 'User',
                'email'    => 'User@gmail.com',
                'role'     => 'user',
                'password' => bcrypt('user123'),
            ]
            ];

            foreach($userData as $item) {
                User::create($item);
            }
    }
}
```
Pada kode di atas Pertama kita menambahkan import Model User.
```php
use App\Models\User;
```
Kemudian pada `function run()` kita menambahkan variable `$userData` dengan tipe data array, yang berisi data dummy.
```php
$userData = [
            [
                'name'     => 'Admin',
                'email'    => 'Admin@gmail.com',
                'role'     => 'admin',
                'password' => bcrypt('admin123'),
            ],
            [
                'name'     => 'User',
                'email'    => 'User@gmail.com',
                'role'     => 'user',
                'password' => bcrypt('user123'),
            ]
            ];
```
Setelah kita mengisi variable $userData dengan data dummy, langkah selanjut-nya kita melakukan `foreach()` pada variable $userData yang berfungsi untuk membuat data tersebut masuk ke dalam table User.
```php
foreach($userData as $item) 
{
   User::create($item);
}
```
Setelah kita membuat seeder dan mengisi data dummy, langkah berikutnya silahkan temen-temen tuliskan perintah berikut ini di dalam terminal/cmd.
```php
php artisan db:seed --class=DummyUsersSeeder
```
Code di atas berfungsi untuk menjalankan seeder, yang telah temen-temen buat dan akan menambahkan data dummy ke database.

# Kesimpulan
Nah Pada blog kali ini temen-temen semua sudah belajar cara membuat kolom baru dengan menggunakan migration dan membuat seeder.

Pada blog selanjut-nya kita semua akan belajar cara membuat proses login pada applikasi crud kita.

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-8-membuat-proses-delete-data">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-10-cara-membuat-proses-login">Next</a>
  </ul>
</div>