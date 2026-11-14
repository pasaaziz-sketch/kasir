# Panduan memasang Kasir & Stok di domain sendiri

Website ini memakai **Firebase** (layanan Google) untuk tiga hal: login (email + password), database toko, dan hosting website beserta domain Anda. Paket gratis Firebase (**Spark**) sudah cukup untuk memulai. Tidak perlu server sendiri.

Perkiraan waktu: 30–60 menit, ditambah waktu tunggu aktivasi domain (beberapa jam, paling lama sekitar 24 jam).

## Isi paket

| File / folder | Fungsi |
| --- | --- |
| `public/` | Website (ini yang diunggah ke hosting) |
| `public/firebase-config.js` | **Wajib diisi** dengan kode proyek Firebase Anda (langkah 2) |
| `firestore.rules` | Aturan keamanan database: siapa boleh membaca dan mengubah apa |
| `firestore.indexes.json` | Indeks database untuk laporan per kasir |
| `firebase.json`, `.firebaserc` | Pengaturan untuk perintah `firebase deploy` |
| `data-awal.json` | Data dari aplikasi lama: 8 produk (termasuk foto), 7 transaksi, 3 pengeluaran, dan pengaturan toko KAMPUNG JEMBAR |

---

## Langkah 1 — Buat proyek Firebase

1. Buka **https://console.firebase.google.com** dan masuk dengan akun Google.
2. Klik **Create a project** (Buat proyek). Beri nama, misalnya `kasir-kampung-jembar`.
3. Google Analytics boleh dimatikan. Klik **Create project**.
4. Catat **Project ID**-nya (contoh: `kasir-kampung-jembar`). ID ini dipakai di langkah 5.

## Langkah 2 — Daftarkan website dan salin konfigurasinya

1. Di halaman proyek, klik ikon **`</>`** (Web) untuk menambah aplikasi web. Beri nama `Kasir`. Kotak "Firebase Hosting" tidak perlu dicentang.
2. Firebase menampilkan kode berisi `const firebaseConfig = { apiKey: ..., authDomain: ..., ... }`.
3. Buka file **`public/firebase-config.js`**, lalu ganti setiap nilai contoh (`GANTI_...`, `ISI_...`) dengan nilai dari Firebase. Hasilnya kira-kira seperti ini:

   ```js
   export const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "kasir-kampung-jembar.firebaseapp.com",
     projectId: "kasir-kampung-jembar",
     storageBucket: "kasir-kampung-jembar.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abc123"
   };
   ```

   Nilai-nilai ini memang dipakai di browser, jadi aman dicantumkan. Data toko dilindungi oleh aturan keamanan di langkah 5.

## Langkah 3 — Aktifkan login email & password

1. Menu kiri: **Build › Authentication**, lalu **Get started**.
2. Tab **Sign-in method**, pilih **Email/Password**, aktifkan baris pertama (Email/Password), lalu **Save**.

## Langkah 4 — Buat database

1. Menu kiri: **Build › Firestore Database**, lalu **Create database**.
2. Lokasi: pilih **asia-southeast2 (Jakarta)** supaya cepat dari Indonesia. Lokasi tidak bisa diubah setelah dibuat.
3. Pilih **Start in production mode**, lalu **Create**.

## Langkah 5 — Unggah website, aturan keamanan, dan indeks

Cara ini memakai komputer (Windows atau Mac), cukup sekali.

1. Pasang **Node.js** (versi LTS) dari https://nodejs.org.
2. Buka **Command Prompt** (Windows) atau **Terminal** (Mac), lalu jalankan:

   ```
   npm install -g firebase-tools
   firebase login
   ```

   Browser akan terbuka untuk login dengan akun Google yang sama.
3. Buka file **`.firebaserc`**, ganti `ISI_PROJECT_ID` dengan Project ID dari langkah 1.
4. Di Command Prompt/Terminal, masuk ke folder paket ini (folder yang berisi `firebase.json`), lalu jalankan:

   ```
   firebase deploy
   ```

   Perintah ini sekaligus mengunggah website, aturan keamanan, dan indeks. Di akhir akan muncul alamat seperti **`https://kasir-kampung-jembar.web.app`**. Website Anda sudah online.

> **Tanpa komputer / tidak bisa memakai perintah di atas?** Aturan keamanan bisa dipasang manual: buka **Firestore Database › Rules**, hapus isinya, tempel seluruh isi file `firestore.rules`, lalu **Publish**. Untuk hosting, perintah `firebase deploy` tetap diperlukan. Cara lain: unggah folder `public` ke layanan seperti Netlify (fitur *Netlify Drop*, cukup seret folder), lalu daftarkan alamat Netlify itu di langkah 8.

## Langkah 6 — Buat akun admin dan pindahkan data lama

1. Buka alamat website Anda. Muncul layar **Buat akun admin**.
2. Isi nama, email, dan password (minimal 6 karakter). Akun pertama ini menjadi **Admin**, dan setelah itu pendaftaran ditutup.
3. Ketuk nama toko di bagian atas untuk membuka **Pengaturan toko**, lalu **Impor data lama**. Pilih file **`data-awal.json`** dari paket ini, lalu **Impor**. Produk, foto, transaksi, pengeluaran, dan pengaturan toko dari aplikasi lama akan masuk.
4. Buka tab **Pengguna**, lalu **+ Tambah pengguna** untuk setiap kasir (nama, email, password awal). Akun dari aplikasi lama (misalnya Dirman) perlu dibuat ulang di sini, karena sistem loginnya berbeda.

## Langkah 7 — Pakai domain sendiri (misalnya `kasir.tokoanda.com`)

1. Beli domain di penyedia domain mana pun (misalnya Niagahoster, Rumahweb, Domainesia, atau Cloudflare), kalau belum punya.
2. Di Firebase: **Build › Hosting**, lalu **Add custom domain**. Ketik domain Anda, misalnya `tokoanda.com` atau `kasir.tokoanda.com`.
3. Firebase menampilkan beberapa **record DNS** (biasanya tipe **TXT** dan **A**). Masuk ke panel penyedia domain Anda, buka pengaturan **DNS**, lalu tambahkan record tersebut persis seperti yang ditampilkan.
4. Kembali ke Firebase dan klik **Verify**. Tunggu sampai statusnya **Connected**. Sertifikat HTTPS (gembok) dibuat otomatis. Prosesnya bisa memakan waktu beberapa jam.

## Langkah 8 — Izinkan login dari domain Anda

1. **Build › Authentication › Settings › Authorized domains**.
2. Klik **Add domain**, lalu masukkan domain Anda (misalnya `kasir.tokoanda.com`). Kalau memakai Netlify, masukkan juga alamat Netlify-nya.

Tanpa langkah ini, login dari domain sendiri akan ditolak. Alamat `.web.app` sudah diizinkan otomatis.

## Langkah 9 — Pasang di HP kasir

1. Buka alamat website di **Chrome** Android.
2. Ketuk **⋮**, lalu **Instal aplikasi** atau **Tambahkan ke layar utama**.
3. Login dengan email dan password yang dibuat admin.

---

## Yang perlu diketahui

**Peran dan keamanan.** Admin bisa mengakses semua fitur. Kasir hanya bisa berjualan, melihat stok tanpa harga modal, mencatat pengeluaran, dan melihat transaksi serta laporan kas miliknya sendiri. Batasan ini dijaga oleh `firestore.rules` di server Google, jadi tetap berlaku walaupun ada yang mengutak-atik browser.

**Internet putus.** Kasir tetap bisa berjualan. Transaksi disimpan di HP dan dikirim otomatis saat internet kembali. Indikator di kanan atas menampilkan **Online** atau **Offline**. Login pertama kali dan pembuatan akun baru tetap perlu internet.

**Mencetak.** Laporan A4 dibuka sebagai PDF di tab baru. Untuk struk, nota, dan laporan kas, lihat bagian *Printer struk Bluetooth* di bawah.

## Printer struk Bluetooth

Ketuk ikon **printer** di bagian atas, di samping nama Anda. Pengaturannya disimpan per perangkat, jadi setiap HP kasir bisa memakai printernya sendiri. Ada tiga cara mencetak:

| Cara | Cocok untuk | Yang perlu disiapkan |
| --- | --- | --- |
| **Bluetooth langsung** | Printer thermal 58/80 mm yang mendukung Bluetooth Low Energy (BLE). Kebanyakan printer struk portabel sekarang punya BLE. | Chrome di Android, Windows, atau Mac. Website harus dibuka lewat `https://`, yang otomatis berlaku di Firebase Hosting. |
| **Lewat aplikasi RawBT** | **Semua** printer Bluetooth di Android, termasuk printer lama yang hanya Bluetooth klasik dan tidak muncul di pilihan langsung | Pasang **RawBT** dari Play Store, sambungkan printer di aplikasi itu sekali, lalu pilih mode ini di website. |
| **Dialog print** | Printer biasa, Wi-Fi, atau USB | Tidak ada. Memakai jendela print bawaan perangkat. |

**Menyambungkan printer Bluetooth langsung:**
1. Nyalakan printer dan Bluetooth HP. Printer tidak perlu dipasangkan (pair) di pengaturan HP.
2. Ketuk ikon printer, pilih **Bluetooth langsung**, lalu **Cari printer Bluetooth**.
3. Pilih nama printer dari daftar. Status berubah menjadi **Terhubung**, dan ikon printer di atas mendapat titik hijau.
4. Ketuk **Tes cetak**. Baris angka `1234567890…` harus pas satu baris. Kalau terpotong atau terlalu pendek, ubah **Lebar kertas**.
5. Opsional: centang **Cetak struk otomatis setelah pembayaran**, supaya struk langsung keluar setiap transaksi.

Kalau koneksi terputus (printer mati atau di luar jangkauan), website mencoba menyambung lagi saat Anda mencetak. Kalau tidak berhasil, ulangi langkah 2.

**Printer tidak muncul di daftar?** Kemungkinan printer Anda hanya mendukung Bluetooth klasik. Pakai mode **RawBT**. iPhone/iPad belum mendukung Bluetooth dari browser, jadi gunakan mode **Dialog print** dengan printer AirPrint.

**Lupa password.** Di layar login, isi email lalu ketuk **Lupa password?**. Link atur ulang dikirim ke email tersebut. Admin juga bisa mengirimnya dari tab Pengguna › Ubah.

**Menghapus kasir.** Menghapus atau menonaktifkan kasir di tab Pengguna langsung mencabut aksesnya ke data. Untuk menghapus akun login-nya sepenuhnya, buka Firebase **Authentication › Users** dan hapus email tersebut.

**Memperbarui website.** Ubah file di folder `public`, lalu jalankan lagi `firebase deploy --only hosting`.

**Biaya.** Paket gratis Spark punya batas harian untuk jumlah baca/tulis database. Untuk toko kecil–menengah biasanya cukup. Pemakaian bisa dipantau di menu **Usage and billing**. Batas terbaru ada di https://firebase.google.com/pricing.
