# Firebase Mini Storage

Mini file manager berbasis Firebase Authentication dan Cloud Storage for Firebase.

## Fitur
- Login admin dengan email/password.
- Upload banyak file, maksimum 50 MB per file.
- Daftar file dengan pagination (100 file per halaman).
- Pencarian nama file, preview, salin URL publik, dan hapus.
- File dapat dibaca publik melalui URL; daftar, upload, dan hapus dibatasi untuk admin.

## 1. Siapkan Firebase
1. Buat project di https://console.firebase.google.com/.
2. Tambahkan aplikasi Web (`</>`) dan salin Firebase Web App config.
3. Authentication > Sign-in method: aktifkan Email/Password.
4. Authentication > Users > Add user: buat akun admin secara manual. Jangan aktifkan registrasi publik.
5. Storage: buat bucket.
6. Perhatikan billing: dokumentasi Firebase saat ini menyatakan Cloud Storage for Firebase memerlukan paket Blaze (pay-as-you-go). Cek harga dan lokasi bucket sebelum mengaktifkan.

## 2. Isi konfigurasi web
Buka `index.html`, cari `const firebaseConfig`, lalu ganti seluruh nilai `GANTI_...` dengan konfigurasi dari Project settings > General > Your apps. `storageBucket` harus persis nama bucket di Firebase Console (bucket baru umumnya berakhiran `.firebasestorage.app`).

## 3. Pasang Storage Rules
Firebase Console > Storage > Rules. Salin `storage.rules`, ganti `GANTI_DENGAN_UID_ADMIN` dengan UID akun admin dari Authentication > Users, lalu Publish.

Aturan ini: `get` publik; `list` hanya admin; `create` hanya admin dan maksimum 50 MB; `delete` hanya admin; `update` ditolak. Jika punya beberapa admin, ganti kondisi UID dengan `request.auth.uid in ["UID_1", "UID_2"]`.

## 4. Jalankan
Jangan buka `index.html` dengan `file://`; gunakan web server lokal atau static hosting. Contoh Python: `python -m http.server 8000`, lalu buka http://localhost:8000. Bisa juga deploy ke Firebase Hosting, Netlify, atau layanan static hosting lain.

## 5. Pakai
Login dengan akun admin, upload file, lalu tekan Salin URL. URL tersebut dapat digunakan di artikel/website lain. Siapa pun yang memiliki URL dapat membaca file. File dihapus melalui tombol Hapus.

## Keamanan dan batasan
- Firebase Web config/API key bukan secret; keamanan ditentukan oleh Authentication dan Storage Rules.
- Jangan pernah memasukkan service-account key atau private key Firebase Admin SDK ke browser.
- Download URL mengandung token akses, perlakukan sebagai link publik.
- Listing file hanya untuk admin, sehingga folder tidak dapat dijelajahi publik melalui aplikasi ini.
- Contoh ini cocok untuk mini storage sederhana. Terapkan validasi tipe file dan batas kuota tambahan jika dipakai banyak pengguna.

## Deploy dengan Firebase CLI (opsional)
Install Firebase CLI (`npm install -g firebase-tools`), login (`firebase login`), lalu dari folder project jalankan `firebase use --add` untuk memilih project. Setelah `firebase.json` dan UID admin sudah diatur, jalankan `firebase deploy --only hosting,storage`. Pastikan file `index.html` sudah berisi config Web App sebelum deploy.
