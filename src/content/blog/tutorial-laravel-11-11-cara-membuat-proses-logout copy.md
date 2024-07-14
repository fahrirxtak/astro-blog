---
title: 'Tutorial Laravel 11 #11 Cara Membuat Proses Logout'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semua, pada blog kali ini kita akan belajar bagaimana cara membat Proses Logout pada applikasi crud sederhana yang kita miliki.
## Langkah 1- Menambahkan Code ke Controller
Pertama-tama kita akan menambahkan kode berikut ini ke dalam Controller yang sudah kita buat dengan nama SesiController yang berada di folder app/http/Controllers/SesiController,
Silahkan tambahkan code berikut ini.
```php
<?php

namespace App\Http\Controllers;
  
use Illuminate\Http\Request;
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

   public function logout() {
        Auth::logout();
        return redirect('')->with('success', 'Berhasil Logout');
    }
}
```
Dari penambahan code di atas kita menambahkan 1 method baru dengan nama `logout`.
```php
public function logout()
{
        // ...
}
```
Di method tersebut pertama kita akan melakukan logout pada pengguna yang sedang aktif(Login).
```php
Auth::logout();
```
Kemudian jika pengguna berhasil logout kita akan melakukan redirect ke halaman awal dengan pesan success.
```php
return redirect('')->with('success', 'Berhasil Logout');
```

## Langkah 2 - Menambahkan Route
Setelah kita berhasil menambahkan fungsi `logout`, maka langkah selanjutnya adalah dengan membuat route. Agar bisa kita gunakan di halaman views.

Silahkan temen-temen buka file routes/web.php , kemudian ubah kode-nya menjadi seperti berikut ini.
```php
<?php

use App\Http\Controllers\KomikController;
use App\Http\Controllers\SesiController;
use Illuminate\Support\Facades\Route;


//! Route for proses login
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

  
//! Route for Logout
Route::post('/logout', [SesiController::class, 'logout'])->name('logout');
```
Pada penambahan code di atas kita menambahkan satu buah route dengan jenis `post` yang kita beri name `logout`, yang mana akan kita gunakan pada halaman views.
```php
//! Route for Logout
Route::post('/logout', [SesiController::class, 'logout'])->name('logout');
```

## Langkah 3 - Menambahkan button Logout
Setelah kita membuat route, kemudian kita akan menambahkan button logout ke dalam tampilan index yang berada di dalam folder resources/views/komik/index.blade.php, silahkan tambahkan kode berikut ini di dalam file tersebut.
```php
@extends('layout.template')

@section('content')
    {{-- ! content --}}
    <main class="px-md-4">
        <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
          <h1 class="h2">Data Komik</h1>
        </div>
        <div class="mt3 center">
            <form action="{{ route('logout') }}" method="POST">
                @csrf
                <button type="submit" class="btn btn-danger mb-3 float-end">Logout</button>
            </form>
            <button class="btn btn-success mb-3" data-bs-toggle="modal" data-bs-target="#modalCreate">Tambah</button>
            <table class="table table-striped table-bordered">
                <thead>
                    <tr>
                        <th>FOTO</th>
                        <th>JUDUL</th>
                        <th>SINOPSIS</th>
                        <th>PENULIS</th>
                        <th class="text-center">Function</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                   @forelse ($komik as $item)
                    <td class="text-center">
                        <img src="{{ asset('/storage/komik/'.$item->foto) }}" class="rounded" style="width: 150px;">
                    </td>
                    <td>{{ $item->judul }}</td>
                    <td>{{ $item->sinopsis }}</td>
                    <td>{{ $item->penulis }}</td>
                    <td>
                        <div class="text-center">
                            <button class="btn btn-secondary" data-bs-toggle="modal" data-bs-target="#modalUpdate{{ $item->id }}">Edit</button>
                            <button class="btn btn-danger" data-bs-toggle="modal" data-bs-target="#modalDelete{{ $item->id }}">Delete</button>
                            <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#modalShow{{ $item->id }}">Show</button>
                        </div>
                    </td>
                    </tr>
                   @empty
                    <div class="alert alert-danger text-center">
                        Data Komik belum tersedia
                    </div>
                    @endforelse
                </tbody>
            </table>
            {{ $komik->links() }}
        </div>

        {{-- ? memanggil Form modal --}}
        @include('komik.create_modal')
        @include('komik.show_modal')
        @include('komik.update_modal')
        @include('komik.delete_modal')
        {{-- ? End --}}
      </main>
@endsection
```
Pada kode di atas kita menambahkan form dengan action `route('logout')`, route tersebut adalah route yang sudah kita buat sebelumnya, yang berfungsi untuk menghubungkan tampilan views dengan controller.Kemudian di dalam form kita menambahkan button untuk Logout.
```php
<form action="{{ route('logout') }}" method="POST">
                @csrf
                <button type="submit" class="btn btn-danger mb-3 float-end">Logout</button>
</form>
```


## Langkah 4 - Uji Coba Button logout
Setelah kita membuat button logout, langkah selanjut-nya kita akan melakukan uji coba, silahkan temen-temen jalankan project laravel-nya. setelah itu kita bisa mengaksesnya di http://localhost:8000 di dalam browser, jika berhasil maka tampilanya kurang lebih seperti ini.
.![Alt text](/tutorial-laravel-11-11/logout.png)
Nah sekarang silahkan temen-temen coba klik button `Logout`, jika berhasil maka akan tampil seperti berikut ini.
.![Alt text](/tutorial-laravel-11-11/logout_berhasil.png)

# Kesimpulan
Nah gimana temen-temen belajar-nya seru kan, kita udah belajar gimana caranya membuat proses logout, jadi makin bagus deh applikasi crud kita.

Pada blog selanjutnya temen-temen semua akan belajar bagaimana cara membuat middleware dan juga membuat halaman untuk tiap role.

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-10-cara-membuat-proses-login">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-12-membuat-middleware-dan-membuat-halaman-untuk-tiap-tole">Next</a>
  </ul>
</div>