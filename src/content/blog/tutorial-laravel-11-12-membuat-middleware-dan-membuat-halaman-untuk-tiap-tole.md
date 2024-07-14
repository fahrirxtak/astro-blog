---
title: 'Tutorial Laravel 11 #12 Membuat Middleware dan membuat halaman untuk tiap role'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semua pada blog sebelumnya kita semua sudah belajar bagaimana cara membuat proses login dan juga logout, nah pada blog kali ini kita akan belajar cara membuat middleware dan juga membuat halaman untuk tiap role.

## Langkah 1 - Membuat Middleware
Pertama-tama Silahkan temen-temen membuat middleware baru di dalam folder routes/web.php kemudian tambahkan code berikut ini.
```php
//! Route Middleware guest
Route::middleware(['guest'])->group(function() {
    Route::get('/', [SesiController::class, 'index'])->name('login');
    Route::post('/', [SesiController::class, 'login']);
});

//! Route Middleware auth
Route::middleware(['auth'])->group(function () {
    Route::post('/logout', [SesiController::class, 'logout'])->name('logout');
    Route::resource('/komik', KomikController::class);
});

//! Route for redirect to page komik
Route::get('/home', function() {
    return redirect('/komik');
});
```
Jika temen-temen perhatikan pada code di atas kita membuat 2 middleware yaitu.
```php
Route::middleware(['guest'])->group(function() {
    // ...
});
```
Pada code di atas kita mendefinisikan sekelompok route yang memerlukan middleware `guest`.
Middleware ini  memastikan bahwa hanya pengguna yang belum login yang dapat mengakses route-route di dalam kelompok ini, seperti.
```php
Route::get('/', [SesiController::class, 'index'])->name('login');
Route::post('/', [SesiController::class, 'login']);
```
Jadi pada code di atas, route-route tersebut hanya bisa di akses oleh pengguna yang belum login.
```php
Route::middleware(['auth'])->group(function () {
    // ...
});
```
Kemudian kita juga menambahkan middleware yang mendefinisikan sekelompok route yang memerlukan middleware `auth`.Middleware ini memastikan bahwa hanya pengguna yang sudah terautentikasi yang dapat mengakses rute-rute di dalam kelompok ini, seperti.
```php
    Route::post('/logout', [SesiController::class, 'logout'])->name('logout');
    Route::resource('/komik', KomikController::class);
```
Jadi pada code di atas, route-route tersebut hanya bisa di akses oleh pengguna yang sudah terautentikasi atau yang sudah login.

## Langkah 2 - Membuat Halaman Untuk Tiap Role
Oke setelah kita membuat middleware, langkah terkahir adalah membuat halaman untuk tiap role yang sudah kita buat sebelumnya, jadi kita akan buat halaman untuk role admin dan juga role user.

Silahkan temen-temen semua buka file index.blade.php di dalam folder resources/views/komik, Kemudian tambahkan code seperti berikut ini.
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
            @if (Auth::user()->role == 'admin')
            <button class="btn btn-success mb-3" data-bs-toggle="modal" data-bs-target="#modalCreate">Tambah</button>
            @endif
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
                            @if (Auth::user()->role == 'admin')
                            <button class="btn btn-secondary" data-bs-toggle="modal" data-bs-target="#modalUpdate{{ $item->id }}">Edit</button>
                            <button class="btn btn-danger" data-bs-toggle="modal" data-bs-target="#modalDelete{{ $item->id }}">Delete</button>
                            @endif
                            @if (Auth::user()->role == 'admin' || 'user')
                            <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#modalShow{{ $item->id }}">Show</button>
                            @endif
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
Pada code di atas jika temen-temen semua perhatikan kita menambahkan kondisi if pada bagian berikut ini.
```php
@if (Auth::user()->role == 'admin')
    // ...
@endif
```
Pada code di atas kita menambahkan kondisi if yang artinya memeriksa apakah peran pengguna adalah 'admin'.

Jika kondisi di atas benar (`role` pengguna adalah `admin`), maka button ini akan ditampilkan.
```php
<button class="btn btn-success mb-3" data-bs-toggle="modal" data-bs-target="#modalCreate">Tambah</button>
```
kita juga menambahkan kondisi if pada bagian berikut ini.
```php
@if (Auth::user()->role == 'admin' || Auth::user()->role == 'user')
	// ...
@endif
```
Pada code di atas kita menggunakan kondisi if yang sedikit berbeda yang artinya if tersebut memeriksa role `admin` dan juga role `user`.

jika kondisi di atas benar(`role` pengguna adalah `admin` dan juga `user`) maka button ini akan ditampilkan.
```php
<button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#modalShow{{ $item->id }}">Show</button>
```

Nah jika temen-temen sudah menambahkan kode di atas maka kita akan melihat halaman role admin dan role user yang berbeda, coba kita bandingkan kedua role tersebut jika di tampilkan.

1. Halaman role 'admin'
.![Alt text](/tutorial-laravel-11-12/admin.png)


2. Halaman role 'user'
.![Alt text](/tutorial-laravel-11-12/user.png)
Bisa diliat perbandinganya ya temen-temen, halaman admin menampilkan button Tambah, Edit, dan juga Delete. Sedangkan halaman user hanya menampilkan button Show saja.

# Kesimpulan
Terima kasih temen-temen semua yang sudah menyempatkan waktunya untuk membaca blog saya. saya harap tutorial ini dapat memberikan manfaat kepada temen-temen semua. 

Mohon Maaf jika ada tulisan saya  yang kurang jelas atau susah  di mengerti, karena ini pertama kali saya buat blog seperti ini. dan jika temen-temen merasa blog ini bermanfaat bisa tolong bagikan dengan keluarganya atau ke saudaranya yang mungkin memerlukan informasi ini.

Jika temen-temen mempunyai pertanyaan atau saran, jangan sungkan untuk bertanya. karena saya sangat menghargai masukan dari temen-temen semua dan akan berusaha untuk menjawab semua pertanyaan temen-temen.

Dan jangan lupa untuk kunjungi website ini terus untuk mendapatkan update terbaru tentang tutorial-toturial bermanfaat.

Sampai jumpa Pada Blog berikutnya!

Kzx.


<div class="link">
  <ul class="left">
    <a href="/blog">Back to blog</a>
  </ul>
</div>