---
title: 'Tutorial Laravel 11 #5 Menambahkan Data Ke Dalam Database'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo semuanya, pada kesempatan kali ini kita semua akan belajar kembali dengan laravel 11 yaitu menambahkan data ke dalam laravel 11 dan kita juga akan belajar bagaimana cara mengupload gambar di laravel 11.

## Langkah 1 - Menambahkan Method  store di Controller
Karena kita akan membuat proses insert atau menambahkan data, kita akan membuat method baru yaitu method store. kenapa kita tidak membuat method create karena disini kita akan membuatnya menggunakan Modal bootstrap, jadi tidak memerlukan method create. Nah apa itu method store?

| Method | Keterangan                                                                                 |
| ------ | ------------------------------------------------------------------------------------------ |
| store  | Digunakan untuk melakukan logika insert data ke dalam database dan melakukan upload gambar |
Silahkan temen-temen buka file app/Http/Controllers/KomikController.php, kemudian ubah kode-nya menjadi seperti berikut ini.

```php
<?php

namespace App\Http\Controllers;

//import model Komik
use App\Models\Komik;

  
//import Http Request
use Illuminate\Http\Request;

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

    public function store(Request $request) {
        //! Validation form
        $request->validate([
            'foto'     => 'required|image|mimes:png,jpg,jpeg',
            'judul'    => 'required',
            'sinopsis' => 'required',
            'penulis'  => 'required',

        ]);

        //! Upload Gambar
        $foto = $request->file('foto');
        $foto->storeAs('public/komik', $foto->hashName());
  
        //! create Data komik
        Komik::create([
            'foto' => $foto->hashName(),
            'judul' => $request->judul,
            'sinopsis' => $request->sinopsis,
            'penulis' => $request->penulis,
        ]);

        //! return to page index
        return back()->with('success', 'Data Komik berhasil dibuat');
    }
}
```
Dari kode yang kita ubah di atas, pertama kita import Http Request.
```php
//import Http Request
use Illuminate\Http\Request;
```
Setelah itu, di dalam class KomikController kita menambahkan 1 method baru yaitu. 
1. function store.

Function store
Method ini fungsinya untuk melakukan proses tambah data atau insert data ke dalam database dan juga upload gambar.
```php
  public function store() 
    {
        // code...
    }
```
Di dalam method ini, hal pertama yang kita lakukan adalah dengan membuat validasi form, yaitu untuk memeriksa apakah data yang dikirimkan User/Pengguna melalui Http Request sudah sesuai atau belum.
```php
  //! Validation form
        $request->validate([
            'foto'     => 'required|image|mimes:png,jpg,jpeg',
            'judul'    => 'required',
            'sinopsis' => 'required',
            'penulis'  => 'required',
        ]);
```
Dari kode di atas kita melakukan validasi, berikut penjelasanya.

| KEY      | VALIDATION | KETERANGAN                                             |
| -------- | ---------- | ------------------------------------------------------ |
| foto     | required   | field/kolom wajib diiisi                               |
|          | image      | field/kolom wajib berupa gambar                        |
|          | mimes      | field/kolom harus memiliki enxtensi png, jpg, dan jpeg |
| judul    | required   | field/kolom wajib diisi                                |
| sinopsis | required   | field/kolom wajib diisi                                |
| penulis  | required   | field/kolom wajib diisi                                |
Setelah kita melakukan validasi, kita melakukan yang namanya Upload gambar.
```php
//! Upload Gambar
$foto = $request->file('foto');
$foto->storeAs('public/komik', $foto->hashName());
```
Setelah berhasil melakukan validasi dan juga upload gambar, maka langkah selanjutnya adalah melakukan proses insert data ke dalam database menggunakan Model Komik.
```php
      //! create Data komik
        Komik::create([
            'foto' => $foto->hashName(),
            'judul' => $request->judul,
            'sinopsis' => $request->sinopsis,
            'penulis' => $request->penulis,
        ]);
```
Jika proses insert data atau menambahkan data berhasil, maka selanjutnya kita akan melakukan back artinya kita akan kembali ke halaman sebelumnya dengan sebuah pesan.
```php
//! return to page index
return back()->with('success', 'Data Komik berhasil dibuat');
```

## Langkah 2 - Membuat View untuk form create Komik
Setelah kita berhasil membuat method di dalam KomikController, maka langkah selanjutnya adalah membuat halaman view untuk menampilkan form data komik-nya.

 Silahkan temen-temen buat file dengan nama create_modal.blade.php di dalam folder resources/views/komik , kemudian masukan kode berikut ini di dalamnya.
```php
<div class="modal fade" id="modalCreate" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header bg-success text-white">
          <h1 class="modal-title fs-5" id="staticBackdropLabel">Tambah Komik</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form action="{{ url('komik') }}" method="POST" enctype="multipart/form-data">
            @csrf
            <div class="form-group mb-3">
                <label class="font-weight-bold">FOTO</label>
                <input type="file" class="form-control @error('foto') is-invalid @enderror" name="foto">
               <!-- error message untuk FOTO -->
                @error('foto')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>
  
            <div class="form-group mb-3">
                <label class="font-weight-bold">JUDUL</label>
                <input type="text" class="form-control @error('judul') is-invalid @enderror" name="judul" value="{{ old('judul') }}" placeholder="Masukan judul Komik">

                <!-- error message untuk title -->
                @error('judul')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>

            <div class="form-group mb-3">
                <label class="font-weight-bold">SINOPSIS</label>
                <input type="text" class="form-control @error('sinopsis') is-invalid @enderror" name="sinopsis" value="{{ old('sinopsis') }}" placeholder="Masukan sinopsis Komik">
  
                <!-- error message untuk title -->
                @error('sinopsis')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>
  
            <div class="form-group mb-3">
                <label class="font-weight-bold">PENULIS</label>
                <input type="text" class="form-control @error('penulis') is-invalid @enderror" name="penulis" value="{{ old('penulis') }}" placeholder="Masukan penulis Komik">

                <!-- error message untuk title -->
                @error('penulis')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>

            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary">Save</button>
              </div>
          </form>
        </div>
      </div>
    </div>
  </div>
``` 
Dari penambahan kode di atas, temen-temen ga usah terlalu pusing ngeliat template bootstarp-nya karena kita akan fokus ke code laravelnya. Nah jika temen-temen perhatiin pada bagian form action, kita arahkan ke url komik, yang artinya saat form itu dikirim dia akan menuju ke URL komik.
```php
<form action="{{ url('komik') }}" method="POST" enctype="multipart/form-data"> 

    //... 

</form>
```
Pada bagian form kita tambahkan attribute enctype="multipart/form-data" ini ditambahkan jika ada sebuah upload file di dalam form-nya.

## Langkah 3 - Menambahkan include 
Nah agar kita bisa menampilkan halaman Form Create, kita harus menambahkan direktif @include ke dalam file index.blade.php, silahkan temen-temen buka folder resources/views/komik dan buka file index.blade.php kemudian tambahkan kode-nya menjadi seperti berikut ini.
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
        {{-- ? memanggil form modal --}}
	        @include('komik.create_modal')
        {{-- ? End --}}
      </main>
@endsection
```
Pada kode di atas kita menambahkan include yang digunakan untuk menyertakan tampilan lain (view) di dalam tampilan saat ini. jadi dengan menggunakan include temen-temen bisa membuka tampilan lain di satu tempat. 
```php
    {{-- ? memanggil form modal --}}
	        @include('komik.create_modal')
    {{-- ? End --}}
```

## Langkah 4 - mencoba insert data Komik
Sekarang setelah kita sudah menambahkan include di halaman index, silahkan temen-temen jalankan perintah berikut ini agar jalan di localhost.
```php
php artisan serve
```
Silahkan temen-temen klik button Tambah pada halaman komik index, dan jika berhasil maka akan menampilkan hasil berikut ![Alt text](/tutorial-laravel-11-5/modal_tambah.png)
Silahkan temen-temen cobain klik button save tanpa mengisi data apapaun, jika berhasil maka akan muncul validasi error yang kurang lebih seperti ini.
![Alt text](/tutorial-laravel-11-5/modal_salah.png)
Nah sekarang temen temen coba masukan data di dalam form dengan benar kemudian klik button Save, dan  jika berhasil maka akan menampilkan hasil seperti berikut ini.![Alt text](/tutorial-laravel-11-5/berhasil.png)

# Kesimpulan
Pada blog kali ini kita telah belajar banyak hal, seperti membuat method store untuk proses insert data, membuat fungsi upload gambar, membuat halaman view untuk form insert data komik dan juga kita sudah mengenal sedikit tentang include.

Pada blog selanjutnya, kita semua akan belajar bagaimana cara menampilkan detail data komik berdasarkan id.

Terimaksih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-4-menampilkan-data-dari-database">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-6-menampilkan-komik-menggunakan-id">Next</a>
  </ul>
</div>