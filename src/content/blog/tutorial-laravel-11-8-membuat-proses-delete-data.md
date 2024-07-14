---
title: 'Tutorial Laravel 11 #8 Membuat Proses Delete data Komik'
description: 'Tutorial laravel 11'
pubDate: 'Jul 09 2024'
heroImage: '/thumbnail_laravel_11.png'
---

 Halo temen-temen semuanya, pada blog terakhir Toturial CRUD laravel 11 ini kita akan belajar bagaimana cara proses delete data dari database sekaligus menghapus gambar.

## Langkah 1 - menambahkan method destroy
Sekarang kita akan menambahkan method baru dengan nama destroy, method ini bertugas untuk melakukan hapus data ke dalam database dan juga menghapus gambar di dalam project laravel.

Silahkan temen-temen semua buka file app/Http/Controllers/KomikController.php kemudian tambahkan kode seperti berikut ini.
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
   
   public function destroy(string $id) {
        //! get data by id
        $komik = Komik::findOrFail($id);

        //! delete image
        Storage::delete('public/komik/'. $komik->foto);

        //! delete data komik
        $komik->delete();
  
        //! return to page index
        return back()->with('success', 'Data Komik Berhasil di Hapus');
    }
}
```
Dari penambahan kode di atas kita menambahkan 1 method baru dengan nama destroy dan pada parameternya kita berikan $id, yang nanti nilainya akan diambil dari URL browser.
```php
public function destroy(string $id) 
{
	// ...
}
```
Di method tersebut pertama kita akan mencari terlebih dahulu data berdasarkan $id-nya
```php
  //! get data by id
  $komik = Komik::findOrFail($id);
```
jika sudah ditemukan kita akan menghapus gambarnya terlebih dahulu.
```php
  //! delete image
  Storage::delete('public/komik/'. $komik->foto);
```
Setelah kita menghapus gambarnya, langkah berikutnya kita menghapus data Komik-nya dari database
```php
//! delete data komik
$komik->delete();
```
Kemudian setelah kita berhasil menghapus seluruh data-nya kira return ke halaman index.
```php
//! return to page index
        return back()->with('success', 'Data Komik Berhasil di Hapus');
```
## Langkah 2 - membuat tampilan Popup untuk delete
Nah setelah kita sudah berhasil membuat controller-nya sekarang kita akan membuat tampilan view untuk delete.

Silahkan temen-temen buat file baru dengan nama delete_modal.blade.php di dalam folder resources/views/komik. kemudian masukan kode berikut ini di dalamnya.
```php
@foreach ($komik as $item)
 <div class="modal fade" id="modalDelete{{ $item->id }}" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header bg-danger text-white">
          <h1 class="modal-title fs-5" id="staticBackdropLabel">Delete Komik</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form action="{{ url('komik/'. $item->id) }}" method="POST">
            @csrf
            @method('DELETE')
            <div class="mb-3">
                <p>Are you sure you want to delete Komik {{ $item->judul }}..?</p>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary">Delete</button>
              </div>
          </form>
        </div>
      </div>
    </div>
  </div>
  @endforeach
```
Dari kode di atas, kita membuat sebuah pop up untuk memunculkan tampilan delete dengan menggunakan foreach dari variable $komik, yang kita dapatkan dari controller. kemudian kita tampilkan data yang ingin kita delete.
```php
 <p>Are you sure you want to delete Komik {{ $item->judul }}..?</p>
```

Nah agar kita bisa menampilkan halaman pop up delete-nya, kita harus menambahkan direktif @include ke dalam file index.blade.php, silahkan temen-temen buka folder resources/views/komik dan buka file index.blade.php kemudian tambahkan  kode-nya menjadi seperti berikut ini.
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
	        @include('komik.delete_modal')
        {{-- ? End --}}
      </main>
@endsection
```
Nah diatas kita telah menambahkan satu include lagi, yang fungsinya yaitu untuk menampilkan halaman pop up delete di halaman index.
```php
        {{-- ? memanggil form modal --}}
	        @include('komik.create_modal')
	        @include('komik.show_modal')
	        @include('komik.update_modal')
	        @include('komik.delete_modal')
        {{-- ? End --}}
```

## Langkah 3 - Uji coba delete Data Komik
Silahkan temen-temen klik button Delete di salah satu data yang temen-temen miliki, jika berhasil maka akan menampilkan popup seperti berikut ini.
.![Alt text](/tutorial-laravel-11-8/Popup_delete.png)
Nah jika temen-temen klik button Delete, maka data akan dihapus dan temen-temen akan mendapatkan hasil seperti berikut ini.
.![Alt text](/tutorial-laravel-11-8/delete.png)

# Kesimpulan 
Pada blog kali ini kita telah belajar menambahkan method untuk proses delete data dari database dan menghapus gambar.

Nah pada blog selanjutnya temen-temen akan belajar cara membuat proses login, jadi ntar kita akan membuat fitur login dan logout ke dalam applikasi crud kita jadi tungguin aja yaa. 

Terimakasih.

<div class="link">
  <ul class="left">
    <a href="/blog/tutorial-laravel-11-7-membuat-proses-update-dan-edit-data">Back</a>
  </ul>
  <ul class="right">
    <a href="/blog/tutorial-laravel-11-9-membuat-kolom-baru-dan-membuat-seeder">Next</a>
  </ul>
</div>