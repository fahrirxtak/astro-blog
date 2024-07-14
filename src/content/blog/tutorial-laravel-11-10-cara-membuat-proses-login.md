---
title: 'Tutorial Laravel 11 #10 Cara Membuat Proses Login'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semua, Pada kesempatan kali ini kita semua akan belajar cara membuat proses login pada applikasi crud kita.

Untuk User Interface(UI) kita akan menggunakan framework CSS yaitu Bootstrap agar mempercepat prosesnya.
## Langkah 1 - Membuat Controller
Silahkan temen-temen tuliskan perintah berikut ini di dalam terminal/cmd pastikan sudah berada di dalam project laravel-nya.
```php
php artisan make:controller SesiController
```
Setelah temen-temen berhasil menjalankan perintah diatas maka kita akan mendapatkan sebuah file baru dengan nama SesiController yang berada di folder.
app/http/Controllers/SesiController

Kemudian silahkan temen temen buka file tersebut, dan ubah semua kode-nya menjadi seperti ini.
```php
<?php
namespace App\Http\Controllers;

// import http Request  
use Illuminate\Http\Request;

// import Auth
use Illuminate\Support\Facades\Auth;
  

class SesiController extends Controller
{
    public function index() {
        return view('login');
    }

    public function login(Request $request) {
        $request->validate([
            'email' => 'required',
            'password' => 'required',
        ]);

        $infoLogin = [
            'email' => $request->email,
            'password' => $request->password,
        ];

  
        if(Auth::attempt($infoLogin)) {
            return redirect('komik')->with('success', 'Berhasil Login');
        }else {
            return back()->withErrors('Username dan password yang dimasukkan tidak sesuai')->withInput();
        }
    }
}
```
Pada kode di atas pertama kita menambahkan import Request
```php
// import http Request  
use Illuminate\Http\Request;
```
kemudian kita juga menambahkan import Auth
```php
// import Auth
use Illuminate\Support\Facades\Auth;
```
Setalah itu kemudian di dalam class SesiController kita menambahkan satu function dengan nama index.
```php
public function index() {
	// ...
}
```
Di dalam function index kita menambahkan code yang berfungsi untuk Mengembalikan tampilan dengan nama 'login'.
```php
return view('login');
```
Kemudian kita menambahkan method baru dengan nama `function login()`.
```php
public function login(Request $request) {
   // ...
}
```
Di dalam method login kita menambahkan code yang berfungsi untuk melakukan proses login.

1. membuat validasi, untuk memeriksa apakah data yang dikirimkan User/Pengguna melalui Http Request sudah sesuai atau belum.
```php
  $request->validate([
            'email' => 'required',
            'password' => 'required',
        ]);
```
2. Setelah temen-temen membuat validasi form, langkah selanjutnya adalah kita membuat variabel $infoLogin yang digunakan untuk menyimpan data email dan password yang diambil dari request yang diterima melalui validasi form.
```php
$infoLogin = [
            'email' => $request->email,
            'password' => $request->password,
        ];
```
3. Setelah kita menyimpan data email dan password melalui variable $infoLogin, kemudian kita periksa jika informasi yang di berikan berhasil diotentikasi, pengguna akan diarahkan ke halaman 'komik'. Jika tidak berhasil, pengguna akan dikembalikan ke halaman sebelumnya dengan pesan error.
```php
if(Auth::attempt($infoLogin)) {
            return redirect('komik')->with('success', 'Berhasil Login');
        }else {
            return back()->withErrors('Username dan password yang dimasukkan tidak sesuai')->withInput();
        }
    }
```

## Langkah 2 - Membuat Route
Setelah kita berhasil membuat Controller, maka langkah selanjutnya adalah dengan membuat route. Route ini akan bertugas sebagai penghubung antara User dengan logika applikasi yang ada di controller. 

Silahkan temen-temen buka file routes/web.php , kemudian ubah kode-nya menjadi seperti berikut ini.
```php
<?php

use App\Http\Controllers\KomikController;
use App\Http\Controllers\SesiController;
use Illuminate\Support\Facades\Route;
  

Route::middleware(['guest'])->group(function() {
    Route::get('/', [SesiController::class, 'index']);
    Route::post('/', [SesiController::class, 'login']);
});

  
//! Route for redirect to page komik
Route::get('/home', function() {
    return redirect('/komik');
});

//! Route resource for Komik
Route::resource('/komik', KomikController::class);
```
Di atas, kita menambahkan sebuah Route baru dengan jenis `middlleware guest` yang digunakan untuk memastikan bahwa hanya pengguna yang tidak sedang masuk(login) yang dapat mengakses route tersebut.
```php
//! Route for proses login
Route::middleware(['guest'])->group(function() {
    Route::get('/', [SesiController::class, 'index']);
    Route::post('/', [SesiController::class, 'login']);
});
```
Code di atas ini berarti kita membuat sekelompok rute yang hanya dapat diakses oleh pengguna yang belum masuk (belum login).

default dari middleware guest adalah path '/home' maka dari itu kita akan membuat fungsi yang jika seseorang mengunjungi path '/home' maka kita akan redirect ke halaman '/komik'.
```php
//! Route for redirect to page komik
Route::get('/home', function() {
    return redirect('/komik');
});
```

## Langkah 3 - Membuat View
Sekarang kita lanjutkan untuk membuat view, yang akan kita gunakan nantinya untuk menampilkan halaman login.

 Silahkan temen-temen buat file baru di dalam folder resource/views dengan nama login.blade.php kemudian tambahkan code berikut ini di dalam file-nya.
```php
@extends('layout.template')

@section('content')
<body>
    <div class="container py-5">
        <div class="w-50 center border rounded px-3 py-3 mx-auto">
        <h1>Login</h1>
        @if ($errors->any())
            <div class="alert alert-danger">
                <ul>
                    @foreach ($errors->all() as $item)
                    <li>{{ $item }}</li>
                    @endforeach
                </ul>
            </div>
        @endif
        <form action="" method="POST">
            @csrf
            <div class="mb-3">
                <label for="email" class="form-label">Email</label>
                <input type="email" value="{{ old('email') }}" name="email" class="form-control">
            </div>
            <div class="mb-3">
                <label for="password" class="form-label">Password</label>
                <input type="password" name="password" class="form-control">
            </div>
            <div class="mb-3 d-grid">
                <button name="submit" type="submit" class="btn btn-primary">Login</button>
            </div>
        </form>
    </div>
    </div>
@endsection
```
Pada code di atas kita membuat halama login sederhana, jika temen-temen perhatikan kita menambahkan code berikut ini.
```php
@if ($errors->any())
            <div class="alert alert-danger">
                <ul>
                    @foreach ($errors->all() as $item)
                    <li>{{ $item }}</li>
                    @endforeach
                </ul>
            </div>
        @endif
```
Pada code di atas di gunakan untuk menampilkan pesan kesalahan (error mesassages) kepada pengguna(user) jika ada kesalahan saat mengisi formulir(form).

## Langkah 4 - Uji Coba Proses Login
Setelah kita membuat halaman login, langkah selanjut-nya kita akan melakukan uji coba, silahkan temen-temen jalankan project laravel-nya. setelah itu kita bisa mengaksesnya di http://localhost:8000 di dalam browser, jika berhasil maka tampilanya kurang lebih seperti ini.
.![Alt text](/tutorial-laravel-11-10/login.png)
Jika temen-temen klik button Login tanpa mengisi data maka akan memunculkan pesan error, kurang lebih seperti berikut ini.
.![Alt text](/tutorial-laravel-11-10/login_error.png)
Nah sekarang temen temen coba masukan Data dummy yang sudah kita buat sebelumnya,  kemudian klik button Login, dan  jika berhasil maka akan menampilkan hasil seperti berikut ini.
.![Alt text](/tutorial-laravel-11-10/berhasil_login.png)

# Kesimpulan
Pada blog kali ini kita telah belajar cara membuat proses login, menambahkan Route dengan jenis middleware dan membuat halaman login.

pada blog selanjut-nya temen-temen semua akan belajar bagaimana cara membuat proses logout pada applikasi crud sederhana kita.

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-9-membuat-kolom-baru-dan-membuat-seeder">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-11-cara-membuat-proses-logout-copy">Next</a>
  </ul>
</div>