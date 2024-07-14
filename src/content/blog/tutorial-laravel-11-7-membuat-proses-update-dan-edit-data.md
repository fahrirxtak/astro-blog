---
title: 'Tutorial Laravel 11 #7 Membuat Proses Update dan Edit Data'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semuanya, pada blog sebelumnya kita sudah belajar cara membuat dan menampilkan detail data komik, nah pada kesempatan kali ini kita semua akan belajar cara membuat proses update dan edit data ke database di laravel 11.

## Langkah 1 - Menambahkan Method Update di Controller
Sekarang kita akan menambahkan 1 method baru dengan nama update, method ini yang bertugas untuk melalakukan proses update data atau edit ke dalam database.

Oke langsung saja ya, pertama kita buka folder app/Http/Controllers/KomikController.php kemudian tambahkan kode-nya menjadi seperti berikut ini.
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
        //! get data Komik by id
        Komik::findOrFail($id);
  
        //! return to page index
        return back();
    }

    public function update(Request $request, string $id) {
        //! validasi
        $request->validate([
            'foto'     => 'image|mimes:png,jpg,jpeg',
            'judul'    => 'required',
            'sinopsis' => 'required',
            'penulis'  => 'required',
        ]);

  
        //! get data by id
        $komik = Komik::findOrFail($id);
  
        if ($request->file('foto')) {
        //! delete gambar old
        Storage::delete('public/komik');
  
        //! Upload Gambar new
        $foto = $request->file('foto');
        $foto->storeAs('public/komik', $foto->hashName());

        //! create Data komik
        $komik->update([
            'foto' => $foto->hashName(),
            'judul' => $request->judul,
            'sinopsis' => $request->sinopsis,
            'penulis' => $request->penulis,
        ]);
        } else {
            //! update withaout Gambar
            $komik->update([
                'judul' => $request->judul,
                'sinopsis' => $request->sinopsis,
                'penulis' => $request->penulis,
            ]);
        }

         //! return to page index
         return back()->with('success', 'Data Komik berhasil di update');
    }
}
```
Pada kode di atas kita menambahkan satu method baru dengan nama update dan di dalamnya kita beri parameter Request dan variable $id.
```php
public function update(Request $request, string $id)
{
	 // ...
}
```
Di dalam method update pertama tama kita tambahkan terlebih dahulu kode validasi yang berbeda, yaitu di dalam kolom foto kita tidak tambahkan required, agar bisa mengirim data tanpa memasukan gambar.
```php
 $request->validate([
            'foto'     => 'image|mimes:png,jpg,jpeg',
            'judul'    => 'required',
            'sinopsis' => 'required',
            'penulis'  => 'required',
        ]);
```
Setelah itu kita mencari variable $id di dalam Model Komik agar kita bisa mendapatkan data komik hanya dengan $id-nya saja.
```php
 //! get data by id
 $komik = Komik::findOrFail($id);
```
Nah sekarang setelah kita mendapatkan $id-nya kita akan melakukan kondisi dengan if else yang mana kita akan melakukan kondisi jika kita mengirim-kan data dengan gambar dan tanpa gambar.
```php
 if ($request->file('foto')) {
        //! delete gambar old
        Storage::delete('public/komik');

        //! Upload Gambar new
        $foto = $request->file('foto');
        $foto->storeAs('public/komik', $foto->hashName());

        //! update Data komik With gambar
        $komik->update([
            'foto' => $foto->hashName(),
            'judul' => $request->judul,
            'sinopsis' => $request->sinopsis,
            'penulis' => $request->penulis,
        ]);
        } else {
            //! update withaout Gambar
            $komik->update([
                'judul' => $request->judul,
                'sinopsis' => $request->sinopsis,
                'penulis' => $request->penulis,
            ]);
        }
```
Di atas, Jika ada sebuah request dengan nama foto, maka kita akan melakukan beberapa aksi, yaitu.
1. Menghapus Gambar lama
```php
 //! delete gambar old
 Storage::delete('public/komik');
```
2. Menambahkan Gambar baru
```php
//! Upload Gambar new
 $foto = $request->file('foto');
 $foto->storeAs('public/komik', $foto->hashName());
```
3.Update Data komik 
```php
 //! update Data komik with Gambar
        $komik->update([
            'foto' => $foto->hashName(),
            'judul' => $request->judul,
            'sinopsis' => $request->sinopsis,
            'penulis' => $request->penulis,
        ]);
```
Tapi jika Request foto tidak ada, maka kita cukup update data komik tanpa gambar.
```php
 else {
            //! update withaout Gambar
            $komik->update([
                'judul' => $request->judul,
                'sinopsis' => $request->sinopsis,
                'penulis' => $request->penulis,
            ]);
        }
```
Setelah berhasil meng-update Data,  selanjutnya kita return back, yang artinya kita mengembalikan data-nya ke halaman sebelum-nya.
```php
 //! return to page index
 return back()->with('success', 'Data Komik berhasil di update');
```

## Langkah 2 - Membuat View Form untuk update
Oke sekarang setelah kita berhasil membuat controller untuk meng-update data komik, langkah selanjutnya adalah kita membuat tampilan view-nya.

Silahkan temen-temen buat file baru dengan nama update_modal.blade.php di dalam folder resources/views/komik. kemudian masukan kode berikut ini di dalamnya.
```php
@foreach ($komik as $item)

<div class="modal fade" id="modalUpdate{{ $item->id }}" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header bg-success text-white">
          <h1 class="modal-title fs-5" id="staticBackdropLabel">Update Komik</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form action="{{ url('komik/'. $item->id) }}" method="POST" enctype="multipart/form-data">
            @csrf
            @method('PUT')
            <div class="text-center">
                <img src="{{ asset('/storage/komik/'.$item->foto) }}" class="rounded" style="width: 150px">
            </div>
            <div class="form-group mb-3">
                <label class="font-weight-bold">FOTO</label>
                <input type="file" class="form-control @error('foto') is-invalid @enderror" name="foto" ">
                <!-- error message untuk FOTO -->
                @error('foto')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>

            <div class="form-group mb-3">
                <label class="font-weight-bold">JUDUL</label>
                <input type="text" class="form-control @error('judul') is-invalid @enderror" name="judul" value="{{ old('judul', $item->judul) }}" placeholder="Input judul">
                <!-- error message untuk title -->
                @error('judul')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>
  
           <div class="form-group mb-3">
                <label class="font-weight-bold">SINOPSIS</label>
                <input type="text" class="form-control @error('sinopsis') is-invalid @enderror" name="sinopsis" value="{{ old('sinopsis', $item->sinopsis) }}" placeholder="Input sinopsis"> 
               <!-- error message untuk title -->
                @error('sinopsis')
                    <div class="invalid-feedback">
                        {{ $message }}
                    </div>
                @enderror
            </div>
  
            <div class="form-group mb-3">
                <label class="font-weight-bold">PENULIS</label>
                <input type="text" class="form-control @error('penulis') is-invalid @enderror" name="penulis" value="{{ old('penulis', $item->penulis) }}" placeholder="Input penulis">
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
 @endforeach
```
Dari penambahan kode di atas, kita menampilkan form update data-nya dengan cara melakukan foreach dari variable $komik. Kemudian kita panggil data-nya agar tampil di view.
1. Menampilkan Foto
```php
<img src="{{ asset('/storage/komik/'.$item->foto) }}" class="rounded"
style="width: 150px">
```
2.  Menampilkan Judul 
```php
{{ old('judul', $item->judul) }}
```
3.  Menampilkan Sinopsis
```php
{{ old('sinopsis', $item->sinopsis) }}
```
4.  Menampilkan Penulis
```php
{{ old('penulis', $item->penulis) }}
```

Jika temen-temen perhatikan di form kita menambahkan satu baris kode yaitu method put. Ini menandakan bahwa form tersebut adalah form edit data.
```php
@method('PUT')
```

## Langkah 3 - Menambahkan include 
Nah agar kita bisa menampilkan halaman update, kita harus menambahkan direktif @include ke dalam file index.blade.php, silahkan temen-temen buka folder resources/views/komik dan buka file index.blade.php kemudian tambahkan  kode-nya menjadi seperti berikut ini.
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
	        @include('komik.update_modal)
        {{-- ? End --}}
      </main>
@endsection
```
Nah diatas kita telah menambahkan satu include lagi, yang fungsinya yaitu untuk menampilkan halaman update di halaman index.
```php
        {{-- ? memanggil form modal --}}
	        @include('komik.create_modal')
	        @include('komik.show_modal')
	        @include('komik.update_modal')
        {{-- ? End --}}
```

## Langkah 4 - Uji Coba meng-update data komik
Nah sekarang setelah temen-temen menambahkan include di file index.blade.php, kita akan mencobanya dengan meng-klik button Edit di data temen-temen, dan jika berhasil maka akan menampilkan hasil seperti berikut ini.
![Alt text](/tutorial-laravel-11-7/edit_modal.png)

Nah sekarang temen temen coba edit data di dalam form, kemudian klik button Save, dan  jika berhasil maka akan menampilkan hasil seperti berikut ini.![Alt text](/tutorial-laravel-11-7/berhasil_update.png)

# Kesimpulan 
Pada blog kali ini temen-temen semua udah belajar membuat proses update dan edit, Nah gimana seru bukann?

Pada blog selanjutnya temen-temen  akan belajar cara delete data dari database.

Terimakasi.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-6-menampilkan-komik-menggunakan-id">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-8-membuat-proses-delete-data">Next</a>
  </ul>
</div>