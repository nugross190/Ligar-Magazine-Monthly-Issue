# Terbitan — `issues/`

Setiap berkas `.html` di folder ini adalah satu edisi majalah yang sudah
diterbitkan — hasil tombol **Unduh Majalah** di [`../editor.html`](../editor.html),
ditaruh di sini lalu di-push. Halaman arsip ([`../index.html`](../index.html))
menemukannya secara otomatis lewat GitHub Contents API; tidak ada daftar yang
perlu diperbarui manual di tempat lain.

## Konvensi nama berkas

```
YYYY-MM-slug-judul.html
```

Contoh: `2026-08-ligar-edisi-agustus.html`. Tombol **Unduh Majalah** sudah
menghasilkan nama dengan pola ini secara otomatis (bulan-tahun sekarang +
judul di halaman sampul) — biasanya tidak perlu ganti nama manual, cukup
taruh berkas hasil unduhan langsung di folder ini.

Berkas yang tidak cocok polanya tetap tampil di arsip — judulnya memakai nama
berkas apa adanya — jadi tidak akan "hilang", hanya tidak dapat label bulan
yang rapi.

## Menerbitkan edisi baru

1. Susun edisi di [`editor.html`](../editor.html), isi semua halaman.
2. Klik **Unduh Majalah** — berkas terunduh dengan nama yang sudah sesuai
   konvensi di atas.
3. Taruh berkas itu di folder ini, commit, push ke `claude/main`.
4. Selesai. Halaman arsip menampilkannya otomatis di kunjungan berikutnya —
   tidak ada berkas lain yang perlu disunting.

## Catatan

- Halaman arsip memanggil GitHub Contents API tanpa otentikasi (batas 60
  permintaan/jam per pengunjung) — cukup untuk skala majalah sekolah, bukan
  untuk trafik tinggi. Kalau limitnya kena, arsip menampilkan tautan langsung
  ke folder ini di GitHub sebagai cadangan, bukan halaman kosong.
- Ini hanya berfungsi lewat situs yang di-hosting (GitHub Pages atau
  sejenisnya) — pencarian daftar edisi butuh jaringan. Membuka
  `editor.html` dengan dobel-klik (`file://`) tidak memakai folder ini sama
  sekali; itu murni alat susun, terlepas dari arsip.
- Setiap edisi menyematkan proyeknya sendiri (lihat komentar di
  `editor.html`) — jadi berkas di sini juga berfungsi sebagai cadangan.
  Kalau perlu mengedit ulang edisi lama, buka `editor.html` lalu **Buka
  Proyek…** dan pilih berkas edisi itu langsung dari folder ini.
