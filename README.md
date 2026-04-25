# Final Task Core Initiative VIX Front End

Aplikasi showcase produk sederhana yang dibangun dengan Vue 2 sebagai bagian dari Final Task Core Initiative VIX Front End. Proyek ini menampilkan produk pakaian dari API eksternal dengan tema dinamis berdasarkan kategori produk.

## Deskripsi

Aplikasi ini mengambil data produk dari Fake Store API dan menampilkan satu produk dalam satu waktu. Pengguna dapat menavigasi antar produk menggunakan tombol "Next Product", yang akan berputar melalui item 1-20. UI secara otomatis menyesuaikan temanya tergantung apakah produk tersebut dari kategori pakaian pria atau wanita.

## Fitur

- Mengambil data produk dari Fake Store API
- Menampilkan informasi produk termasuk gambar, judul, deskripsi, harga, kategori, dan rating
- Tema dinamis berdasarkan kategori produk (pakaian pria vs wanita)
- Navigasi Next Product dengan perulangan index otomatis
- Loading state dengan animasi spinner
- State produk tidak tersedia untuk item non-pakaian
- Transisi fade yang smooth antar perubahan produk
- Layout responsif dengan background pattern

## Tech Stack

- Vue 2
- JavaScript (ES6+)
- HTML5
- CSS3 (Vanilla, tanpa framework)
- Fake Store API

## Cara Menjalankan

1. Clone repository ini
2. Install dependencies:
   ```
   npm install
   ```
3. Jalankan development server:
   ```
   npm run serve
   ```
4. Buka browser dan akses `http://localhost:8080`

### Build untuk production
```
npm run build
```

## Struktur Proyek

- `src/components/ProductDisplay.vue` - Komponen utama yang menangani tampilan produk dan logika API
- `src/components/Loader.vue` - Komponen loading spinner
- `src/assets/style/page.css` - Style global dan CSS variables

## Catatan

Aplikasi ini hanya menampilkan produk dari kategori "clothing". Jika produk tidak sesuai dengan filter ini, akan ditampilkan state unavailable. Index produk secara otomatis reset ke 1 setelah mencapai produk ke-20, menciptakan loop yang berkelanjutan.

UI menggunakan pola state machine untuk mengelola tiga tampilan berbeda: loading, produk tersedia, dan produk tidak tersedia. Warna tema dan background berubah secara dinamis berdasarkan apakah produk termasuk pakaian pria atau wanita.

## Author

Diva Satria
