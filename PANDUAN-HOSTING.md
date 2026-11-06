# Panduan Pemasangan di Hosting — Agenda Guru v0.2

Paket `agenda-guru-v0.2.zip` sudah berisi semua yang dibutuhkan (framework, dependensi, CSS terkompilasi).
**Hosting tidak perlu Composer, Node.js, atau akses SSH.**

## 1. Persyaratan hosting

| Kebutuhan | Keterangan |
|---|---|
| PHP | **8.2 atau lebih baru** (diuji pada PHP 8.3) |
| Ekstensi PHP | `intl`, `mbstring`, `json`, `mysqli` (MariaDB/MySQL) **atau** `pgsql` (PostgreSQL) |
| Web server | Apache atau LiteSpeed dengan `mod_rewrite` dan `.htaccess` aktif (standar cPanel/DirectAdmin) |
| Database | MariaDB 10.4+ / MySQL 5.7+ / MySQL 8, atau PostgreSQL 12+ |
| SSL | Sangat disarankan (Let's Encrypt/AutoSSL gratis di kebanyakan hosting) |
| OPcache | Aktifkan (Select PHP Version → Extensions → `opcache`). Tanpa OPcache halaman bisa 3–10× lebih lambat. Statusnya terlihat di menu **Status Sistem** (admin). |

Di cPanel, versi PHP & ekstensi diatur lewat **Select PHP Version** / **MultiPHP Manager**. Pastikan `intl` tercentang.

## 2. Buat database

cPanel → **MySQL® Databases**:
1. Buat database, mis. `namauser_agenda`.
2. Buat user database dengan kata sandi kuat.
3. Tambahkan user ke database dengan **ALL PRIVILEGES**.

## 3. Unggah aplikasi — pilih salah satu cara

### Cara A (paling mudah): unggah ke `public_html` atau subfolder
1. File Manager → masuk `public_html` (atau buat subfolder, mis. `public_html/agenda`).
2. Unggah `agenda-guru-v0.2.zip`, klik **Extract**.
3. Pindahkan **isi** folder `agenda-guru-v0.2/` ke lokasi tujuan (sehingga `app/`, `public/`, `vendor/`, `.htaccess` berada langsung di `public_html/agenda/`).
4. Pastikan file tersembunyi `.htaccess` ikut terpindah (aktifkan "Show Hidden Files").

`.htaccess` di folder utama otomatis mengarahkan pengunjung ke `public/` dan **memblokir** akses ke `app/`, `vendor/`, `writable/`, dan `.env`.

### Cara B (lebih aman, bila hosting mengizinkan atur Document Root)
1. Ekstrak ke luar `public_html`, mis. `/home/namauser/agenda-guru/`.
2. Arahkan domain/subdomain ke `/home/namauser/agenda-guru/public` (cPanel → Domains → Document Root).

## 4. Izin folder
Folder `writable/` (beserta `cache`, `logs`, `session`, `uploads`, `backups`) harus dapat ditulis: **755** (atau 775 bila diperlukan). File tidak perlu 777.

## 5. Jalankan wizard instalasi
Buka `https://domain-anda/agenda/install` (atau `https://domain-anda/` lalu Anda otomatis diarahkan).

1. **Langkah 1** — pemeriksaan server & koneksi database. Isi URL aplikasi (pakai `https://`), host (biasanya `localhost`), nama database, user, dan kata sandi. Wizard menguji koneksi lalu menulis `.env`.
   *Jika `.env` tidak bisa ditulis*, wizard menampilkan isinya — buat file `.env` di folder utama aplikasi lewat File Manager dan tempelkan.
2. **Langkah 2** — tabel dibuat otomatis, lalu buat akun guru utama. Centang “data contoh” hanya untuk mencoba.

Setelah selesai, wizard terkunci (`writable/installed.lock`). Segera jalankan wizard setelah unggah agar tidak didahului orang lain.

## 6. Setelah instalasi
- Login → **Pengaturan → Profil** lengkapi data sekolah dan unggah **logo sekolah** (PNG/JPG/WebP, maks. 1 MB).
- Buat **Tahun Ajaran** (semester Ganjil/Genap dibuat otomatis), **Kelas**, **Mapel**, lalu impor **Siswa** dari CSV.
- **Pengaturan → Backup & Restore**: buat backup berkala dan unduh salinannya.

## 7. Pemecahan masalah

| Gejala | Solusi |
|---|---|
| **500 Internal Server Error** | Periksa versi PHP ≥ 8.2 dan ekstensi `intl`. Lihat `writable/logs/`. Bila hosting melarang `Options` di `.htaccess`, hapus baris `Options -Indexes` di `.htaccess` dan `public/.htaccess`. |
| Halaman tampil tanpa gaya (CSS) | Pastikan `app.baseURL` di `.env` sama persis dengan URL yang dibuka (termasuk `https` dan subfolder, diakhiri `/`). |
| 404 untuk semua halaman kecuali beranda | `mod_rewrite` belum aktif, atau aktifkan `RewriteBase /subfolder/` di `public/.htaccess`. |
| “The framework needs the following extension(s)…” | Aktifkan ekstensi yang disebut (biasanya `intl`) di Select PHP Version. |
| Login terus kembali ke halaman login | Folder `writable/session` tidak dapat ditulis, atau `cookie.secure = true` padahal situs belum HTTPS. |
| Ingin memasang ulang | Hapus `writable/installed.lock` (dan kosongkan database bila perlu), lalu buka `/install`. |

## 8. Pindah hosting / ganti database
Buat backup di aplikasi lama (format JSON), pasang aplikasi di tempat baru (boleh MariaDB ↔ PostgreSQL), lalu **Pengaturan → Backup & Restore → Pulihkan dari file**. Logo sekolah tidak ikut backup — unggah ulang di Profil.
