# Pasang gratis: GitHub Pages + Firebase

Cara ini tidak perlu membeli hosting dan tidak perlu menjalankan perintah apa pun. Semuanya lewat browser.

- **GitHub Pages** menampilkan website. Alamatnya menjadi `https://NAMAANDA.github.io/kasir/`.
- **Firebase** (paket gratis Spark) menyimpan login dan data toko.

Alamatnya sudah `https://`, jadi printer Bluetooth dan tombol cetak langsung bisa dipakai.

> **Penting:** repositori GitHub gratis bersifat **publik**, artinya kode website bisa dilihat orang. Itu aman, karena data toko tersimpan di Firebase dan dijaga aturan keamanan. Tapi **jangan pernah mengunggah `data-awal.json`** ke GitHub, karena file itu berisi data toko Anda. File itu cukup dipilih dari HP/komputer saat impor di langkah 7.

---

## Langkah 1 — Siapkan file

1. Unduh dan ekstrak `kasir-stok-website.zip`.
2. Buka folder **`public`**. Hanya isi folder inilah yang diunggah ke GitHub:
   `index.html`, `firebase-config.js`, `manifest.json`, `sw.js`, `icon.svg`, `icon-192.png`, `icon-512.png`.

## Langkah 2 — Buat proyek Firebase

1. Buka **https://console.firebase.google.com** › **Create a project**, misalnya `kasir-kampung-jembar`.
2. Klik ikon **`</>`** (Web) untuk mendaftarkan aplikasi web. Salin isi `firebaseConfig` yang muncul.
3. Buka `public/firebase-config.js` dengan Notepad, lalu ganti nilai `GANTI_...` / `ISI_...` dengan yang dari Firebase. Simpan.

## Langkah 3 — Aktifkan login dan database

1. **Build › Authentication › Get started › Sign-in method › Email/Password** › aktifkan › **Save**.
2. **Build › Firestore Database › Create database** › lokasi **asia-southeast2 (Jakarta)** › **Start in production mode**.

## Langkah 4 — Pasang aturan keamanan (tanpa komputer)

1. **Firestore Database › Rules**.
2. Hapus semua isinya, tempel **seluruh** isi file `firestore.rules` dari paket, lalu klik **Publish**.

## Langkah 5 — Buat dua indeks (untuk laporan per kasir)

**Firestore Database › Indexes › Composite › Create index**, lalu buat dua indeks ini:

| Collection ID | Field 1 | Field 2 | Query scope |
| --- | --- | --- | --- |
| `transaksi` | `kasirId` — Ascending | `waktu` — Descending | Collection |
| `pengeluaran` | `olehId` — Ascending | `waktu` — Descending | Collection |

Pembuatan indeks butuh beberapa menit.

## Langkah 6 — Unggah ke GitHub Pages

1. Daftar atau masuk di **https://github.com** (gratis).
2. Klik **+** (kanan atas) › **New repository**. Nama: `kasir`. Pilih **Public**. Klik **Create repository**.
3. Di halaman repositori, klik **uploading an existing file** (atau **Add file › Upload files**).
4. Seret **isi** folder `public` (7 file di atas, bukan foldernya) ke halaman itu. Klik **Commit changes**.
5. Buka **Settings › Pages**. Di **Source** pilih **Deploy from a branch**, branch **main**, folder **/ (root)**, lalu **Save**.
6. Tunggu 1–2 menit. Alamat website muncul di bagian atas halaman Pages, misalnya `https://NAMAANDA.github.io/kasir/`.

## Langkah 7 — Izinkan login dari GitHub, lalu mulai pakai

1. Di Firebase: **Authentication › Settings › Authorized domains › Add domain**, lalu isi `NAMAANDA.github.io` (tanpa `https://` dan tanpa `/kasir`).
2. Buka alamat website. Buat **akun admin**.
3. Ketuk nama toko › **Impor data lama**, lalu pilih `data-awal.json` dari perangkat Anda.
4. Tab **Pengguna** › tambahkan akun kasir.
5. Di HP kasir: buka alamat website di Chrome › ⋮ › **Instal aplikasi**.

---

## Mengubah website nanti

Buka repositori di GitHub › klik file yang mau diganti › ikon pensil (edit) atau **Add file › Upload files** untuk menimpa. Setelah **Commit changes**, website ikut berubah dalam 1–2 menit.

## Biaya

- GitHub Pages: gratis untuk repositori publik.
- Firebase Spark: gratis, tanpa kartu kredit. Ada batas pemakaian harian yang biasanya cukup untuk toko kecil–menengah. Pemakaian bisa dipantau di menu **Usage and billing**.
- Domain sendiri (mis. `tokoanda.com`) **tidak wajib**. Alamat `github.io` sudah bisa dipakai selamanya.
