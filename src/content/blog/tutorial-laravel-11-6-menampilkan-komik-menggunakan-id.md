---
title: 'Tutorial Laravel 11 #6 Menampilkan detail Komik Menggunakan id'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo teman-teman semuanya, pada kesempatan kali ini kita akan belajar bagaimana cara menampilkan detail data berdasarkan ID di Laravel 11.

## Langkah 1 - Menambahkan Method show di Controller
Sekarang kita akan menambahkan 1 method baru di dalam KomikController dengan nama show, nah kenapa kita harus menamakanya dengan nama show, karena sebelumnya kita menggunakan route dengan jenis resource, maka untuk penamaan method-method di dalam Controller harus sesuai dengan aturan dari Laravel.

Silahkan temen-temen buka folder app/Http/Controllers/KomikController.php kemudian ubah kode-nya menjadi seperti berikut ini.
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

	public function show(string $id)
	{
		//get data Komik By id
		Komik::findOrFail($id);

		//return to page index
		return back();
	}
}
```
Dari penambahan kode di atas, kita menambahkan method baru dengan nama show dan di dalam parameternya kita berikan varibale $id
```php
public function show(string $id) 
{
  // ...
}
```
Setelah data berhasil didapatkan, selanjutnya kita return back, yang artinya kita mengembalikan data-nya ke halaman sebelum-nya.
```php
//return to page index
return back();
```

## Langkah 2 - Membuat view Detail Data Komik
Setelah kita berhasil menambahkan method show dan berhasil mendapatkan id-nya, maka langkah selanjutnya adalah kita membuat tampilan data-nya.

Silahkan temen-temen buat file baru dengan nama show_modal.blade.php di dalam folder resources/views/komik. kemudian masukan kode berikut ini di dalamnya.
```php
@foreach ($komik as $item)

 <div class="modal fade" id="modalShow{{ $item->id }}" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header bg-primary text-white">
          <h1 class="modal-title fs-5" id="staticBackdropLabel">Show Komik</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
       </div>
       <div class="modal-body">
           <div class="text-center">
                <img src="{{ asset('/storage/komik/'.$item->foto) }}" class="rounded" style="width: 150px">
            </div>
            <div class="text-center">
                {{ $item->judul }}
            </div>
            <br>
            <div class="flex-end">
                Sinopsis: {{ $item->sinopsis }}
            </div>
            <br>
            <div class="flex-end">
               Penulis: {{ $item->penulis }}
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
              </div>
        </div>
      </div>
    </div>
  </div>
  @endforeach
```
Dari penambahan kode di atas, kita menampilkan detail data-nya dengan cara melakukan foreach dari objek $komik. Kemudian kita panggil data-nya agar tampil di view.

1. Menampilkan Foto
```php
<img src="{{ asset('/storage/komik/'.$item->foto) }}" class="rounded"
style="width: 150px">
```
2.  Menampilkan Judul
```php
{{ $item->judul }}
```
3.  Menampilkan Sinopsis
```php
{{ $item->sinopsis }}
```
4.  Menampilkan Penulis
```php
{{ $item->penulis }}
```

## Langkah 3 - Menambahkan include 
Nah agar kita bisa menampilkan halaman show, kita harus menambahkan direktif @include ke dalam file index.blade.php, silahkan temen-temen buka folder resources/views/komik dan buka file index.blade.php kemudian tambahkan  kode-nya menjadi seperti berikut ini.
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
	        @include('komik.show_modal')
        {{-- ? End --}}
      </main>
@endsection
```
Nah diatas kita telah menambahkan satu include lagi, yang fungsinya yaitu untuk menampilkan halaman show di halaman index.
```php
        {{-- ? memanggil form modal --}}
	        @include('komik.create_modal')
	        @include('komik.show_modal')
        {{-- ? End --}}
```

## Langkah 4 - Uji Coba Menampilkan Detail Data Komik
Nah sekarang setelah temen-temen menambahkan include di file index.blade.php, kita akan mencobanya dengan meng-klik button SHOW di data temen-temen, dan jika berhasil maka akan menampilkan hasil seperti berikut.
![Alt text](/tutorial-laravel-11-6/show_modal.png)

# Kesimpulan
Pada Blog ini temen-temen semua sudah belajar bagaimana cara menampilkan detail data komik berdasarkan ID, dimana data ID tersebut di ambil dari parameter di dalam URL browser.\

Pada blog selanjutnya temen-temen akan belajar cara membuat proses update dan edit data ke dalam database di laravel 11.

Terimakasih semuaa.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-5-menambahkan-data-ke-dalam-database">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-7-membuat-proses-update-dan-edit-data">Next</a>
  </ul>
</div>