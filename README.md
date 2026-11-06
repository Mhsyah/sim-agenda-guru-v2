# Agenda Guru v0.2 · @yhsyah

Aplikasi web personal untuk satu guru: perencanaan Kurikulum Merdeka (CP/TP), kalender pendidikan,
data siswa, jadwal & jurnal mengajar, penilaian UH/ATS/AAS dengan bobot yang dapat dikonfigurasi, rekap, laporan, dan backup.

- **Backend:** CodeIgniter 4.7 (PHP 8.2+)
- **Database:** MariaDB/MySQL (MySQLi) **atau** PostgreSQL (Postgre) — skema & test suite diuji pada keduanya
- **UI:** Tailwind CSS 4 (dikompilasi, tanpa CDN), font di-host sendiri, JS vanilla ±10 KB, tema terang/gelap/sistem

Pemasangan di hosting: lihat **[PANDUAN-HOSTING.md](PANDUAN-HOSTING.md)**.

## Pengembangan lokal (Laragon)

```bash
composer install
npm install
npm run build:css            # atau: npm run watch:css
cp .env.example .env         # isi koneksi database, CI_ENVIRONMENT = development
php spark migrate
php spark db:seed ReferenceSeeder
```
Atau cukup buka `http://localhost/agenda-guru-v02/install` dan ikuti wizard.

### Menjalankan di Laragon
- Alamat **`http://localhost/agenda-guru-v02/`** langsung berfungsi.
- Alamat cantik **`http://agenda-guru-v02.test/`** baru aktif setelah Laragon membuat virtual host:
  klik **Reload** di Laragon (atau Stop → Start All) dan izinkan permintaan Administrator agar Laragon
  menambahkan `agenda-guru-v02.test` ke file `hosts`. Laragon otomatis memakai folder `public/` sebagai document root.

## Pengujian

```bash
vendor/bin/phpunit -c phpunit.xml                          # MariaDB/MySQL
php -d extension=pgsql vendor/bin/phpunit -c phpunit-pgsql.xml   # PostgreSQL
```
Test memakai database `agenda_guru_v02_test` dengan awalan tabel `db_` (memastikan dukungan DBPrefix).

## Paket rilis

```bash
php scripts/build-release.php   # → build/agenda-guru-v0.2.zip
```

## Catatan desain penting
- Flag boolean disimpan sebagai `SMALLINT` 0/1 agar sama di MariaDB & PostgreSQL.
- Query memakai Query Builder CI4; satu-satunya kode khusus vendor (reset sequence PostgreSQL setelah restore) diisolasi di `BackupService::resetSequences()`.
- Nilai kosong = “Belum diinput” (tidak disimpan sebagai 0). Rekap **Final** hanya bila bobot aktif = 100% dan semua komponen lengkap; selain itu **Sementara**.
- Audit log (UTC) mencatat login, perubahan nilai, bobot, impor, arsip, backup/restore.
