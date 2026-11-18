# E-Antrean BPJS

Aplikasi web berbasis **PHP & MySQL** untuk layanan antrean mandiri dan pendataan peserta BPJS Kesehatan. Peserta dapat mendaftar, login, dan mengambil nomor antrean poli secara online, sementara petugas (admin) dapat mengelola antrean harian serta data anggota.

## Fitur

### Peserta
- Registrasi akun baru menggunakan Nomor BPJS, NIK, nama, faskes tingkat 1, dan kelas rawat
- Login menggunakan Nomor Kartu BPJS dan password
- Kartu digital peserta (nama, no. BPJS, NIK, faskes, kelas rawat)
- Ambil nomor antrean untuk 4 layanan: **Poli Umum (U)**, **Poli Gigi (G)**, **Poli KIA (K)**, dan **Administrasi BPJS (A)**
- Pembatalan antrean selama status masih *Menunggu*
- Live monitor antrean yang sedang dipanggil beserta jam real-time (auto-refresh tiap 30 detik)

### Admin
- Dashboard ringkasan: total antrean hari ini, total anggota terdaftar, tanggal sistem
- Kelola antrean hari ini: **Panggil**, **Selesai**, **Hapus**
- Pendataan anggota BPJS: **Tambah**, **Edit**, **Hapus**, serta pencarian cepat (nama / NIK / no. BPJS)

### Lainnya
- Format nomor antrean per poli, misalnya `U-001`, `G-002`
- Validasi input angka saja untuk NIK dan No. BPJS, serta NIK wajib 16 digit (sisi klien)
- Notifikasi alert yang tertutup otomatis setelah 4 detik

## Teknologi

| Komponen | Keterangan |
|---|---|
| Backend | PHP 8 (MySQLi) |
| Database | MySQL / MariaDB |
| Frontend | Bootstrap 5.3, Bootstrap Icons, JavaScript vanilla |
| Server lokal | XAMPP (Apache + MySQL) |

## Struktur Berkas

```
.
├── index.php          # Beranda: monitor antrean, kartu peserta, form ambil antrean
├── login.php          # Login peserta, daftar baru, login admin (tab)
├── proses_login.php   # Pemrosesan login, registrasi, dan login admin
├── logout.php         # Logout peserta / admin
├── antrean.php        # Proses ambil & batalkan antrean (peserta)
├── admin.php          # Dashboard admin (kelola antrean & anggota)
├── anggota.php        # Proses tambah / edit / hapus anggota (admin)
├── koneksi.php        # Koneksi database & session_start()
├── header.php         # Template header & navbar
├── footer.php         # Template footer & script
├── script.js          # Jam real-time, validasi, search, auto-refresh
├── style.css          # Styling kustom
└── db_bpjs.sql        # Skema dan data awal database
```

## Cara Instalasi

1. **Pasang XAMPP** lalu jalankan modul **Apache** dan **MySQL**.
2. **Salin folder proyek** ke direktori `htdocs`, contoh: `C:\xampp\htdocs\antrean-bpjs\`.
3. **Buat database** lewat phpMyAdmin (`http://localhost/phpmyadmin`) dengan nama `db_bpjs`.
4. **Import** berkas `db_bpjs.sql` ke database tersebut.
5. Pastikan konfigurasi di `koneksi.php` sesuai dengan lingkungan Anda:
   ```php
   $host = "localhost";
   $user = "root";
   $pass = "";
   $db   = "db_bpjs";
   ```
6. Buka di browser: `http://localhost/antrean-bpjs/`

> Koneksi internet dibutuhkan karena Bootstrap dan Bootstrap Icons dimuat dari CDN.

## Akun Default

| Peran | Username / No. BPJS | Password |
|---|---|---|
| Admin | `admin` | `123` |
| Peserta (contoh) | `0001234567890` | `12345` |

Login admin dapat diakses melalui tab **Mode Admin** di halaman login atau tombol **Akses Admin** di navbar.

## Skema Database

**`pengguna`**

| Kolom | Tipe | Keterangan |
|---|---|---|
| id | INT, PK, AI | |
| no_bpjs | VARCHAR(20), UNIQUE | Nomor kartu BPJS |
| nik | VARCHAR(16) | |
| nama_lengkap | VARCHAR(100) | |
| faskes_tingkat_1 | VARCHAR(100) | |
| kelas_rawat | ENUM | `Kelas 1`, `Kelas 2`, `Kelas 3` |
| password | VARCHAR(255) | |

**`antrean`**

| Kolom | Tipe | Keterangan |
|---|---|---|
| id | INT, PK, AI | |
| id_pengguna | INT, FK → `pengguna.id` | `ON DELETE CASCADE` |
| nomor_antrean | VARCHAR(10) | Contoh: `U-001` |
| poli_tujuan | VARCHAR(50) | |
| keluhan | TEXT | |
| tanggal | DATE | |
| status | ENUM | `Menunggu`, `Dipanggil`, `Selesai` |

## Alur Penggunaan

1. Peserta mendaftar atau login, lalu memilih poli dan mengisi keluhan singkat.
2. Sistem menerbitkan nomor antrean dengan status **Menunggu**.
3. Admin menekan **Panggil** sehingga status menjadi **Dipanggil** dan nomor tampil di monitor beranda.
4. Setelah dilayani, admin menekan **Selesai**.
