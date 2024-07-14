---
title: 'Tutorial Laravel 11 #4 Menampilkan Data dari Database'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Hallo semuanya, pada kesempatan kali ini kita akan belajar bagaimana menampilkan data dari database di laravel 11.

Untuk User Interface(UI) kita akan menggunakan framework CSS yaitu Bootstrap agar mempercepat prosesnya.
## Langkah 1 - Membuat Controller Komik
Oke sekarang kita akan membuat sebuah Controller, Controller ini yang bertugas untuk mengatur kode-kode yang akan kita tulis nantinya, Dan dia juga yang bertugas untuk menghubungkan antara Model dan View.

Silahkan temen temen buka terminal/CMD dan jalankan perintah berikut ini di dalam folder project kita.
```php
php artisan make:controller KomikController
```
Setelah temen-temen berhasil menjalankan perintah diatas maka kita akan mendapatkan sebuah file baru yang berada di folder.
app/http/Controllers/KomikController

Kemudian silahkan temen temen buka file tersebut, dan ubah semua kode-nya menjadi seperti ini.
```php
<?php

namespace App\Http\Controllers;

//import model Komik
use App\Models\Komik;

//import return type View
use Illuminate\View\View;

class KomikController extends Controller
{
 public function index() : View

    {
        //! get all data komik
        $komik = Komik::latest()->paginate(5);

        //! redirect to page komik.index
        return view('komik.index', compact('komik'));
    }
}
```
Dari kode di atas kita menambahkan terlebih dahulu import Model Komik.
```php
//import model Komik
use App/Models/Komik;
```
Kemudian kita juga manambahkan import return type view.
```php
// import return type view
use Illuminate\View\View;
```
Setalah itu kemudian di dalam class KomikController kita menambahkan satu method dengan nama index.
```php
public function index() : View
{
	Code...
}
```
Nah di dalam method itu kita tambahkan code yang fungsinya untuk memanggil data Komik dari database melalui Model Komik.
```php
$komik = Komik::latest()->paginate(5);
```
Nah setelah kita memanggil semua data komik dengan menggunakan Metode latest() yang fungsinya  untuk memanggil data tersebut dengan mengurutkan berdasarkan data terbaru.

Maka langkah selanjutnya adalah mengembalikan tampilan kedalamn (view) yang bernama komik.index
```php
// redirect to page komik.index
return view('komik.index', compact('komik'));
```
dan fungsi compact adalah untuk mengirim data $komik dari controller ke dalam view.

## Langkah 2 - Membuat Route
Setelah kita berhasil membuat Controller, maka langkah selanjutnya adalah dengan membuat route. Route ini akan bertugas sebagai penghubung antara User dengan logika applikasi yang ada di controller. kurang lebih seperti ini contohnya.

[User] ---> [Router] ---> [Controller] ---> [Response]

Silahkan temen-temen buka file routes/web.php , kemudian ubah kode-nya menjadi seperti berikut ini.
```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});

//! Route resource for Komik
Route::resource('/komik', KomikController::class);
```
Di atas, kita menambahkan sebuah Route baru dengan Path '/komik' dan kita arahkan route-nya ke dalam class KomikController. Untuk route-nya kita menggunakan jenis resource yang artinya laravel akan secara otomatis menghasilkan route-route untuk berbagai operasi CRUD seperti.


| Method       |       path       | Keterangan                                   |
| ------------ | ---------------- | -------------------------------------------- |
| _GET_        | `/komik/create`  | **Menampilkan form untuk membuat data baru** | 
| _POST_       | `/komik`         | **Menyimpan data komik bar**                 | 
| _GET_        | `/komik{id}`     | **Menampilkan detail dari sebuah komik**     | 
| _GET_        | `/komik{id}/edit`| **Menampilkan form untuk memgedit komik**    | 
| _PUT_        | `/komik{id}   `  | **Memperbarui Data komik**                   | 
| _DELETE_     | `/komik{id} `    | **Menghapus Data komik**                     | 


Untuk melihat apakah routenya berhasil dijalankan, temen-temen buka terminal/CMD lalu ketikan kode berikut ini.
```php
php artisan route:list
```
![Alt text](/tutorial-laravel-11-4/route.png)

## Langkah 3 - Membuat View
Sekarang kita lanjutkan untuk membuat view, yang akan kita gunakan nantinya untuk menampilkan data Komik dari database.

 Silahkan temen-temen buat folder layout di dalam folder resources/views, kemudian di dalam folder layout silahkan temen temen buat file baru dengan nama template.blade.php, kemudian masukan kode berikut ini di dalam file template.blade.php.
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>Data Komik</title>
	<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css"  rel="stylesheet">
</head>

<body style="background: lightgray">
@yield('content')
</body>
</html>
```
Oke kode di atas kita buat kerangka atau template dasar yang digunakan untuk membuat tampilan halaman web yang konsisten di seluruh situs web. Dengan menggunakan layout temen-temen tidak perlu lagi menambahkan elemen-elemen yang sama, seperti header, footer, sidebar.
```php
@yield('content')
```
Nah diatas kita menambahkan yield yang fungsinya untuk menunjukkan tempat dimana content dari template lain akan diisi.

Silahkan temen-temen buat folder komik di dalam folder resources/views, kemudian di dalam folder komik silahkan temen temen buat file baru dengan nama index.blade.php, kemudian masukan kode berikut ini di dalam file index.blade.php.
```php
@extends('layout.template')

@section('content')

    {{-- ! content --}}
    <main class="px-md-4">
        <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
          <h1 class="h2">Data Product</h1>
        </div>
        <div class="mt3 center">
            <button class="btn btn-success mb-3" data-bs-toggle="modal" data-bs-target="#modalCreate">Create</button>
            <table class="table table-striped table-bordered">
                <thead>
                    <tr>
                        <th>FOTO</th>
                        <th>JUDUL</th>
                        <th>SINOPSIS</th>
                        <th>PENULIS</th>
                        <th class="text-center">function</th>
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
            {{ $products->links() }}
        </div>
      </main>
@endsection
```
Nah dari penambahan kode di atas kita menggunakan extends untuk memanggil template kita yang ada di folder layout.template.
```php
@extends('layout.template')
```
Kemudian untuk bisa menggunakan template-nya kita bisa memakasi section yang kita samakan dengan yield yang berada di layout utama.
```php
@section('content)
```
Nah selanjutnya untuk menampilkan data kita akan gunakan fitur dari blade yaitu @forelse.
```php
@forelse ($komik as $item)
 
// tampilkan data
 	
@empty

// Data Komik belum Tersedia.

@endforelse
```
Untuk pagination, kita cukup memanggilnya seperti berikut.
```php
{{ $komik->links() }}
```

## Langkah 4 - Mencoba menampilkan Data Komik
Sekarang setelah kita sudah berhasil membuat tampilanya kita akan menjalankanya menggunakan perintah berikut.
```php
php artisan serve
```
setelah itu kita bisa mengaksesnya di http://localhost:8000/komik di dalam browser, jika berhasil maka tampilanya kurang lebih seperti ini.
![Alt text](/tutorial-laravel-11-4/tampilan.png)
 Di atas masi menampilkan  Data komik belum tersedia karena memang kita belum memiliki data apapun di dalam table komiks.

## Kesimpulan
Nah pada blog kali ini kita suda belajar banyak ya temen-temen dari cara membuat Controller beserta methodnya, juga kita belajar cara membuat dan memahami routing dan terakhir kita menampilkan data menggunaka view.

Pada blog selanjutnya kita semua akan belajar bagaimana cara melakukan proses Tambah data komik ke dalam database di laravel 11.

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-3-cara-membuat-model-dan-migration">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-5-menambahkan-data-ke-dalam-database">Next</a>
  </ul>
</div>