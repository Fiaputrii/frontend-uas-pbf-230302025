## 🖥️ Setup Backend (CodeIgniter)

### 1. Clone Repo Backend
```bash
git clone https://github.com/fasshashaa/Perwalian-Backend.git (nama folder)
```


### 2. Install Dependency CodeIgniter
```bash
composer install
```


### 3. Menjalankan CodeIgniter
```bash
php spark serve
```

### 4. Cek Endpoint


## 🖥️ Setup Frontend (Laravel)

### 1. Melalui terminal/cmd
```bash
composer create-project laravel/laravel (nama-projek)
```


### 2. Melalui Laragon
- Buka Laragon
- Klik kanan Quick app
- Laravel

### 3. Install Dependency Laravel
```bash
Composer install
```


### 4. Menjalankan Laravel
```bash
php artisan serve
```


# 📅 Tahapan Pembuatan Frontend

## Controller
```bash
php artisan make:controller nama_fileController / php artisan make:model nama-file -mcr
```


### Contoh MahasiswaController
```bash
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class MahasiswaController extends Controller
{
    
    /** Base URL API, diambil dari .env */
    protected string $api;

    public function __construct()
    {
        $this->api = rtrim(env('API_URL', 'http://localhost:8080'), '/');
    }

    /**
     * Tampilkan daftar mahasiswa (dengan paging manual).
     */
    public function index(Request $request)
    {
        // Ambil seluruh data dari API
        $res = Http::get("{$this->api}/vmahasiswa");
        if (! $res->successful()) {
            abort(502, 'Gagal mengambil data dari server eksternal.');
        }

        $all = $res->json(); // array of mahasiswa
        // paging sederhana: page? per_page?
        $perPage = 10;
        $page    = max(1, (int) $request->query('page', 1));
        $slice   = array_slice($all, ($page - 1) * $perPage, $perPage);
        $total   = count($all);
        $last    = ceil($total / $perPage);

        return view('dosenwali.mahasiswa.index', [
            'mahasiswa' => $slice,
            'page'      => $page,
            'last'      => $last,
        ]);
    }

    /**
     * Tampilkan detail satu mahasiswa berdasarkan NIM.
     */
    public function show(string $nim)
    {
        $res = Http::get("{$this->api}/vmahasiswa/{$nim}");
        if ($res->status() === 404) {
            abort(404, "Mahasiswa dengan NIM {$nim} tidak ditemukan.");
        }
        if (! $res->successful()) {
            abort(502, 'Gagal mengambil data detail dari server eksternal.');
        }

        $m = $res->json(); // associative array
        return view('mahasiswa.show', compact('m'));
    }
}
```


## View
```bash
php artisan make:view nama_file
```


### Contoh index.blade.php
```bash
@extends('layouts.app')
@section('content')
<h2>Daftar Mahasiswa</h2>

<table>
  <tr>
    <th>NIM</th><th>Nama</th><th>Email</th><th>Detail</th>
  </tr>
  @foreach($mahasiswa as $m)
    <tr>
      <td>{{ $m['nim'] }}</td>
      <td>{{ $m['nama_mahasiswa'] }}</td>
      <td>{{ $m['email'] }}</td>
      <td>
        <a href="{{ route('mahasiswa.show', $m['nim']) }}">Lihat</a>
      </td>
    </tr>
  @endforeach
</table>

{{-- Pagination --}}
<div>
  @if($page > 1)
    <a href="{{ route('mahasiswa.index', ['page'=>$page-1]) }}">« Prev</a>
  @endif
  Halaman {{ $page }} dari {{ $last }}
  @if($page < $last)
    <a href="{{ route('mahasiswa.index', ['page'=>$page+1]) }}">Next »</a>
  @endif
</div>
@endsection
```
