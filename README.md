# Garasi-Standaran
Manajemen Bengkel Standaran

# Garasi Standaran — Sistem Manajemen Bengkel

> Aplikasi web manajemen bengkel berbasis satu file HTML yang berjalan sepenuhnya di browser tanpa server, database, atau instalasi tambahan.

---

## Daftar Isi

- [Gambaran Umum](#gambaran-umum)
- [Fitur Utama](#fitur-utama)
- [Cara Penggunaan](#cara-penggunaan)
- [Struktur Halaman (Modul)](#struktur-halaman-modul)
- [Alur Data Antar Modul](#alur-data-antar-modul)
- [Sistem Penyimpanan Data](#sistem-penyimpanan-data)
- [Autentikasi Admin](#autentikasi-admin)
- [Komponen UI](#komponen-ui)
- [Desain & Tema Visual](#desain--tema-visual)
- [Tipografi](#tipografi)
- [Struktur Kode](#struktur-kode)
- [Fungsi JavaScript Utama](#fungsi-javascript-utama)
- [Peringatan & Notifikasi](#peringatan--notifikasi)
- [Keterbatasan & Catatan](#keterbatasan--catatan)

---

## Gambaran Umum

**Garasi Standaran** adalah sistem manajemen bengkel lengkap yang dikemas dalam satu file HTML tunggal. Tidak memerlukan koneksi internet setelah dimuat (kecuali Google Fonts), tidak memerlukan server backend, dan tidak memerlukan database eksternal — semua data tersimpan di `localStorage` browser pengguna.

| Atribut         | Detail                                      |
|-----------------|---------------------------------------------|
| Nama File       | `garasi_standaran.html`                       |
| Bahasa Antarmuka | Indonesia                                  |
| Teknologi       | HTML5, CSS3, Vanilla JavaScript (ES6+)      |
| Penyimpanan     | `localStorage` browser (prefix `bpro_`)    |
| Dependensi      | Google Fonts (Outfit + JetBrains Mono)      |
| Ukuran File     | ± 4.400+ baris kode                         |
| Backend/Server  | Tidak diperlukan                            |
| Framework CSS   | Tidak ada (CSS murni dengan variabel)       |

---

## Fitur Utama

### Manajemen Inventori
- Pencatatan **barang masuk** (pembelian dari supplier) lengkap dengan harga beli, harga jual, ongkos kirim, dan keterangan
- Pencatatan **barang keluar** (pemakaian/penjualan sparepart)
- **Stok barang** otomatis terupdate dari setiap transaksi masuk dan keluar
- Manajemen **inventori/alat kerja bengkel** (dibedakan dari stok sparepart biasa melalui kode `INV`)
- **Peringatan stok menipis** otomatis ketika stok ≤ nilai minimum yang ditentukan
- Tampilan stok dengan progress bar visual per item

### Keuangan
- **Cashflow** otomatis teragregasi dari semua transaksi (masuk, keluar, operasional)
- Pencatatan **biaya operasional** manual (listrik, sewa, gaji, dll.)
- **Laporan bulanan** lengkap: pemasukan, pengeluaran stok, biaya operasional, laba/rugi bersih
- Format mata uang Rupiah (Rp) mengikuti locale `id-ID`

### Dashboard
- Ringkasan statistik: total jenis barang, jumlah item menipis, nilai total stok, aktivitas terbaru
- Grid kategori barang
- Daftar alert stok kritis (hingga 8 item, dengan link ke modul stok)

### Kenyamanan Pengguna
- **Autocomplete** pada form: kode barang, nama barang, kategori, supplier — mengambil saran dari data yang sudah ada
- **Filter multi-kriteria** di setiap tabel: pencarian teks, filter kategori, filter bulan
- **Split view** (daftar + panel detail) di modul masuk, keluar, stok, dan alat
- **Toast notification** untuk konfirmasi aksi (tambah, edit, hapus, login/logout)
- Tekan `Escape` untuk menutup semua modal yang sedang terbuka
- Sidebar dapat dibuka/ditutup (toggle ikon ↔ label)
- Breadcrumb navigasi di topbar

---

## Cara Penggunaan

### 1. Buka File
Cukup buka `garasi_standaran.html` langsung di browser modern (Chrome, Firefox, Edge, Safari). Tidak diperlukan server lokal.

### 2. Login Admin
Semua operasi **tulis** (tambah, edit, hapus data) memerlukan login admin.

- Klik tombol **"Login Admin"** di topbar atau sidebar
- Masukkan kredensial default:
  - **Username:** `admin`
  - **Password:** `bengkel123`
- Setelah login, status disimpan di `localStorage` sehingga tetap aktif saat halaman di-refresh

> ⚠️ Kredensial disimpan di `localStorage`. Untuk keamanan lebih, ganti kredensial melalui fitur pengaturan (jika tersedia di versi mendatang).

### 3. Mulai Mencatat Data
Urutan yang disarankan:
1. Buka menu **Barang Masuk** → catat pembelian sparepart dari supplier
2. Data otomatis muncul di **Stok Barang** dan **Cashflow**
3. Saat sparepart dipakai → catat di **Barang Keluar**
4. Catat biaya rutin di **Operasional Bengkel**
5. Pantau kondisi keuangan di **Laporan** dan **Cashflow**

---

## Struktur Halaman (Modul)

Aplikasi terdiri dari **8 halaman/modul** utama yang dirender secara client-side (SPA tanpa routing URL):

### 1. 🏠 Dashboard (`page-dashboard`)
Halaman utama yang ditampilkan pertama kali.

**Komponen:**
- **4 Stat Card:**
  - Total Jenis Barang (gabungan stok + alat)
  - Stok Menipis (jumlah item ≤ stok minimum)
  - Nilai Total Stok (harga beli × qty semua item)
  - Aktivitas Terbaru (transaksi terakhir)
- **Grid Kategori:** menampilkan semua kategori barang beserta jumlah jenisnya; klik untuk masuk ke halaman Stok
- **Alert Stok Menipis:** daftar hingga 8 item kritis dengan progress bar visual, kode barang, dan satuan; klik item untuk langsung membuka halaman Stok dan menyorot item terkait

---

### 2. 📥 Barang Masuk (`page-masuk`)
Modul pencatatan pembelian/penerimaan barang dari supplier.

**Fitur tabel:**
| Kolom      | Keterangan                              |
|------------|-----------------------------------------|
| Tanggal    | Tanggal transaksi                       |
| Kode       | Kode barang (misal: SP001, INV001)      |
| Nama Barang| Nama lengkap sparepart                  |
| Kategori   | Kategori barang                         |
| Qty        | Jumlah barang masuk                     |
| Harga Beli | Harga beli per satuan                   |
| Total      | Qty × Harga Beli                        |
| Supplier   | Nama toko/distributor                   |
| Aksi       | Tombol Edit & Hapus (butuh login)       |

**Filter:** pencarian teks, filter kategori (dinamis), filter bulan

**Panel Detail (kanan):** menampilkan semua field termasuk ongkos kirim dan keterangan alur data

**Form Input (`modal-masuk`):**
- Tanggal (default hari ini)
- Kode barang (autocomplete dari data existing)
- Nama barang (autocomplete)
- Kategori (autocomplete + buat baru)
- Satuan (dropdown: Pcs, Set, Liter, Botol, Meter, Roll, Pasang, Unit, Lusin)
- Qty / Jumlah
- Harga Beli / Satuan (Rp)
- Harga Jual / Satuan (Rp)
- Stok Minimum (default: 5; digunakan untuk trigger alert)
- Supplier (autocomplete)
- Ongkos Kirim/Terima (Rp) — jika diisi, akan mengurangi kas di Cashflow
- Keterangan
- Info alur otomatis: menampilkan ke modul mana data akan disimpan

> **Logika kode INV:** Jika kode diawali `INV`, barang diarahkan ke modul **Alat Kerja/Inventori Bengkel**, bukan Stok Barang.

---

### 3. 📤 Barang Keluar (`page-keluar`)
Modul pencatatan pemakaian atau penjualan sparepart.

**Fitur tabel:**
| Kolom      | Keterangan                                |
|------------|-------------------------------------------|
| Tanggal    | Tanggal transaksi                         |
| Kode       | Kode barang                               |
| Nama Barang| Nama sparepart                            |
| Kategori   | Kategori barang                           |
| Qty        | Jumlah barang keluar                      |
| Harga Jual | Harga jual per satuan                     |
| Total      | Qty × Harga Jual                          |
| Keterangan | Catatan tambahan                          |
| Aksi       | Tombol Edit & Hapus (butuh login)         |

**Filter:** sama dengan Barang Masuk

**Form Input (`modal-keluar`):** tanggal, kode, nama, kategori, satuan, qty, harga jual, keterangan

---

### 4. 📦 Stok Barang (`page-stok`)
Tampilan agregat stok sparepart saat ini (bukan alat/inventori bengkel).

**Data stok** dihitung dari:
- Setiap transaksi **Barang Masuk** (menambah stok) yang kodenya tidak diawali `INV`
- Setiap transaksi **Barang Keluar** (mengurangi stok)

**Fitur tabel:**
| Kolom       | Keterangan                                       |
|-------------|--------------------------------------------------|
| Kode        | Kode barang                                      |
| Nama        | Nama barang                                      |
| Kategori    | Kategori                                         |
| Stok        | Jumlah stok saat ini                             |
| Min         | Stok minimum (batas alert)                       |
| Satuan      | Satuan barang                                    |
| Harga Beli  | Harga beli per satuan                            |
| Harga Jual  | Harga jual per satuan                            |
| Nilai Stok  | Stok × Harga Beli                                |
| Status      | Badge: `HABIS` (merah), `Menipis` (kuning), `Aman` (hijau) |
| Aksi        | Tombol Edit & Hapus                              |

**Panel Detail (kanan):** menampilkan progress bar stok, info supplier, dan keterangan

**Edit Stok (`modal-edit-stok`):** memungkinkan koreksi langsung pada data stok (kode, nama, kategori, satuan, harga beli, harga jual, stok minimum)

---

### 5. 🔨 Alat Kerja / Inventori Bengkel (`page-alat`)
Modul khusus untuk peralatan bengkel (kunci, kompresor, dongkrak, dll.) yang dibedakan dari sparepart. Data masuk ke sini jika kode barang diawali `INV`.

**Struktur dan fitur** serupa dengan Stok Barang, namun data terpisah di localStorage key `bpro_alat`.

---

### 6. 💰 Cashflow (`page-cashflow`)
Laporan arus kas otomatis yang teragregasi dari tiga sumber:

| Sumber          | Tipe        | Keterangan                                    |
|-----------------|-------------|-----------------------------------------------|
| Barang Keluar   | Pemasukan   | Total penjualan (qty × harga jual)            |
| Barang Masuk    | Pengeluaran | Total pembelian + ongkos kirim (jika ada)     |
| Operasional     | Pengeluaran | Biaya listrik, sewa, gaji, dll.               |

**Fitur tabel:**
| Kolom      | Keterangan                                      |
|------------|-------------------------------------------------|
| Tanggal    | Tanggal transaksi                               |
| Jenis      | Badge warna: Pemasukan (hijau) / Pengeluaran (merah) |
| Sumber     | Asal transaksi (Penjualan / Pembelian Stok / Operasional) |
| Deskripsi  | Nama barang atau deskripsi biaya                |
| Nominal    | Jumlah dalam Rupiah                             |
| Saldo      | Saldo kumulatif berjalan                        |

**Filter:** pencarian teks, filter jenis (pemasukan/pengeluaran), filter bulan

**Footer tabel:** menampilkan jumlah transaksi dan **saldo akhir** periode

---

### 7. 🔧 Operasional Bengkel (`page-operasional`)
Pencatatan biaya operasional harian yang tidak terkait langsung dengan stok barang.

**Contoh biaya:** listrik, air, sewa tempat, gaji karyawan, servis peralatan, dll.

**Stat card ringkasan (bulan ini):**
- Total Biaya Bulan Ini
- Jenis Biaya (jumlah kategori unik)
- Jumlah Transaksi

**Form Input (`modal-ops`):** tanggal, kategori (autocomplete), deskripsi, nominal (Rp)

---

### 8. 📊 Laporan (`page-laporan`)
Ringkasan bulanan keuangan bengkel.

**Stat Card (4 kartu):**
- **Pemasukan** — dari penjualan (barang keluar)
- **Pengeluaran Stok** — total pembelian barang
- **Biaya Operasional** — total biaya operasional
- **Laba / Rugi Bersih** — Pemasukan − (Pengeluaran Stok + Operasional)

**Tabel detail:**
- Daftar transaksi Barang Masuk bulan terpilih
- Daftar transaksi Barang Keluar bulan terpilih
- Daftar biaya Operasional bulan terpilih

**Filter:** pilih bulan (input type month)

---

## Alur Data Antar Modul

```
BARANG MASUK
  ├─► Kode diawali INV?
  │     ├─ Ya  → ALAT KERJA (bpro_alat)
  │     └─ Tidak → STOK BARANG (bpro_stok)
  ├─► CASHFLOW (pengeluaran: harga beli × qty + ongkir)
  └─► LAPORAN (pengeluaran stok)

BARANG KELUAR
  ├─► STOK BARANG (kurangi stok)
  ├─► CASHFLOW (pemasukan: harga jual × qty)
  └─► LAPORAN (pemasukan)

OPERASIONAL BENGKEL
  ├─► CASHFLOW (pengeluaran operasional)
  └─► LAPORAN (biaya operasional)

DASHBOARD
  └─► Membaca dari: STOK BARANG + ALAT KERJA + BARANG MASUK + BARANG KELUAR
```

---

## Sistem Penyimpanan Data

Semua data disimpan di **`localStorage`** browser menggunakan wrapper `S` (objek JavaScript internal):

```javascript
const S = {
  get: (k, def) => JSON.parse(localStorage.getItem('bpro_' + k)) ?? def,
  set: (k, v)   => localStorage.setItem('bpro_' + k, JSON.stringify(v))
};
```

### Key localStorage yang Digunakan

| Key                | Tipe    | Isi                                              |
|--------------------|---------|--------------------------------------------------|
| `bpro_masuk`       | Array   | Semua transaksi barang masuk                     |
| `bpro_keluar`      | Array   | Semua transaksi barang keluar                    |
| `bpro_stok`        | Array   | Data agregat stok barang sparepart               |
| `bpro_alat`        | Array   | Data inventori/alat kerja bengkel                |
| `bpro_ops`         | Array   | Entri biaya operasional                          |
| `bpro_cashflow`    | Array   | Entri cashflow teragregasi                       |
| `bpro_session`     | Boolean | Status login admin (`true`/`false`)              |
| `bpro_admin_user`  | String  | Username admin (default: `"admin"`)              |
| `bpro_admin_pass`  | String  | Password admin (default: `"bengkel123"`)         |

### Struktur Data Transaksi Masuk

```json
{
  "id":       "lz4abc12",
  "tgl":      "2025-03-13",
  "kode":     "SP001",
  "nama":     "Kampas Rem Honda Beat",
  "kat":      "Rem",
  "satuan":   "Pcs",
  "qty":      10,
  "hbeli":    25000,
  "hjual":    35000,
  "stokMin":  5,
  "supplier": "Toko Sinar Jaya",
  "ongkir":   10000,
  "ket":      "Cocok untuk Honda Beat, Scoopy"
}
```

### ID Unik
ID setiap entri dibuat dengan fungsi:
```javascript
function uid() {
  return Date.now().toString(36) + Math.random().toString(36).slice(2, 6);
}
```
Menghasilkan string pendek unik berbasis timestamp + random (contoh: `lz4abc12`).

---

## Autentikasi Admin

Sistem login sederhana berbasis `localStorage`:

- **Kredensial default:** `admin` / `bengkel123`
- Sesi login disimpan di `bpro_session` — tetap aktif setelah refresh halaman
- Logout otomatis membersihkan flag sesi
- **Seluruh operasi CRUD** (tambah, edit, hapus) di semua modul memerlukan status login
- Jika pengguna mencoba aksi tanpa login, muncul toast error dan modal login terbuka otomatis
- Tekan `Enter` di field password untuk submit form login

> **Catatan Keamanan:** Autentikasi ini bersifat client-side dan tidak aman untuk lingkungan produksi multi-pengguna. Kredensial tersimpan di localStorage dalam format JSON plain text.

---

## Komponen UI

### Sidebar
- **Lebar terbuka:** 240px — menampilkan ikon + label menu
- **Lebar tertutup:** 58px — hanya ikon
- Toggle via tombol `☰` di topbar
- Saat tertutup dan sub-menu diklik, sidebar otomatis terbuka terlebih dahulu
- Memiliki sub-menu dropdown untuk grup **Inventori** (Masuk, Keluar, Stok, Alat) dan **Keuangan** (Cashflow, Operasional, Laporan)
- Footer menampilkan versi aplikasi
- Tekstur titik-titik halus (dot pattern) sebagai latar

### Topbar
- Sticky di bagian atas halaman
- Backdrop blur (`blur(20px)`) untuk efek frosted glass
- Komponen kanan: tanggal hari ini, **alert stok menipis** (beranimasi pulse merah jika ada item kritis), tombol Login/Logout

### Alert Dropdown (Stok Menipis)
- Muncul dari tombol alert di topbar
- Menampilkan daftar semua item dengan stok ≤ minimum
- Diurutkan dari stok paling kritis (paling sedikit)
- Item berwarna merah (HABIS) atau kuning (Menipis)
- Klik item → navigasi langsung ke Stok Barang dan highlight item

### Modal
Semua form menggunakan modal overlay dengan:
- Animasi masuk `modalIn` (scale + translateY dengan cubic-bezier spring effect)
- Header sticky + footer sticky di dalam modal scrollable
- Tutup dengan: tombol ✕, tombol Batal, klik area luar modal, atau tekan `Escape`

### Split View
Digunakan di modul Masuk, Keluar, Stok, dan Alat:
- **Kiri:** tabel dengan toolbar filter
- **Kanan:** panel detail item yang dipilih (atau placeholder jika belum ada yang dipilih)

### Toast Notification
- Muncul di pojok kanan bawah layar
- Tiga tipe: `success` (hijau), `error` (merah), `info` (biru)
- Auto-hilang setelah ±3 detik dengan animasi fade out

### Badge Status
| Badge    | Warna  | Konteks                          |
|----------|--------|----------------------------------|
| Aksen    | Lime   | Status aktif, nilai positif      |
| Merah    | Merah  | Habis, error, pengeluaran        |
| Kuning   | Kuning | Menipis, perhatian               |
| Biru     | Biru   | Info, cashflow masuk             |
| Abu-abu  | Gelap  | Netral                           |

---

## Desain & Tema Visual

Aplikasi menggunakan tema **dark mode** eksklusif dengan palet warna neon-on-dark:

### Palet Warna (CSS Variables)

| Variable      | Nilai HEX / RGBA              | Penggunaan                              |
|---------------|-------------------------------|-----------------------------------------|
| `--bg`        | `#080B12`                     | Background utama halaman                |
| `--bg2`       | `#0D1017`                     | Background sidebar                      |
| `--bg3`       | `#111520`                     | Background input field                  |
| `--surface`   | `#161B28`                     | Card, modal                             |
| `--surface2`  | `#1C2235`                     | Hover state, row highlight              |
| `--surface3`  | `#222840`                     | Border accent, scrollbar                |
| `--border`    | `rgba(255,255,255,0.06)`      | Border halus                            |
| `--border2`   | `rgba(255,255,255,0.12)`      | Border sedang                           |
| `--border3`   | `rgba(255,255,255,0.18)`      | Border aktif                            |
| `--text`      | `#E2E6F0`                     | Teks utama                              |
| `--text2`     | `#7C85A0`                     | Teks sekunder / label                   |
| `--text3`     | `#404660`                     | Teks tersier / placeholder              |
| `--accent`    | `#9fef00`                     | **Warna utama — Lime Neon**             |
| `--accent-d`  | `#7bc800`                     | Accent hover / darker                   |
| `--red`       | `#EF4444`                     | Error, hapus, habis, pengeluaran        |
| `--yellow`    | `#F59E0B`                     | Peringatan, menipis                     |
| `--blue`      | `#3B82F6`                     | Info, pemasukan                         |
| `--green`     | `#22C55E`                     | Sukses, stok aman                       |
| `--purple`    | `#A855F7`                     | Dekorasi                                |

### Dimensi & Spasi

| Variable           | Nilai  | Keterangan                     |
|--------------------|--------|--------------------------------|
| `--sidebar-closed` | 58px   | Lebar sidebar tertutup         |
| `--sidebar-open`   | 240px  | Lebar sidebar terbuka          |
| `--topbar-h`       | 56px   | Tinggi topbar                  |
| `--radius`         | 10px   | Border radius elemen kecil     |
| `--radius-lg`      | 14px   | Border radius elemen besar     |
| `--transition`     | `0.25s cubic-bezier(0.4,0,0.2,1)` | Transisi standar  |

---

## Tipografi

| Font               | Weight         | Penggunaan                                          |
|--------------------|----------------|-----------------------------------------------------|
| **Outfit**         | 300–800        | Font utama UI — semua teks antarmuka                |
| **JetBrains Mono** | 400, 500       | Angka monospace — harga, stok, kode barang, tanggal |

Keduanya dimuat dari Google Fonts CDN.

---

## Struktur Kode

File HTML tunggal dibagi menjadi tiga bagian utama:

```
bengkel-pro-v2.html
├── <head>
│   ├── Meta tags (charset, viewport, title)
│   └── Link Google Fonts
│
├── <body>
│   ├── .sidebar — navigasi kiri (termasuk sub-menu Inventori & Keuangan)
│   ├── .main
│   │   ├── .topbar — header sticky
│   │   └── .content — container semua halaman
│   │       ├── #page-dashboard
│   │       ├── #page-masuk
│   │       ├── #page-keluar
│   │       ├── #page-stok
│   │       ├── #page-alat
│   │       ├── #page-cashflow
│   │       ├── #page-operasional
│   │       └── #page-laporan
│   │
│   ├── Modal Overlays (6 modal)
│   │   ├── #modal-masuk      — form barang masuk
│   │   ├── #modal-keluar     — form barang keluar
│   │   ├── #modal-ops        — form operasional
│   │   ├── #modal-edit-stok  — form edit stok
│   │   ├── #modal-hapus      — konfirmasi hapus
│   │   └── #modal-login      — form login admin
│   │
│   ├── #toast-wrap           — container toast notifikasi
│   │
│   └── <script>              — semua logika JavaScript (~2000+ baris)
│       ├── State aplikasi
│       ├── Storage wrapper (S)
│       ├── Toggle sidebar & sub-menu
│       ├── Navigasi (goPage)
│       ├── Login/Logout
│       ├── Helper functions (uid, rp, hariIni, fmtTgl)
│       ├── Autocomplete (kode, nama, kategori, supplier)
│       ├── CRUD Barang Masuk
│       ├── CRUD Barang Keluar
│       ├── CRUD Stok Barang
│       ├── CRUD Alat Kerja
│       ├── CRUD Cashflow
│       ├── CRUD Operasional
│       ├── Render Laporan
│       ├── Render Dashboard
│       ├── Alert sistem (stok menipis)
│       ├── Toast notification
│       └── Init()
│
└── <style> (±1000+ baris CSS)
    ├── CSS Variables
    ├── Reset
    ├── Sidebar
    ├── Topbar & Alert Dropdown
    ├── Content & Pages
    ├── Stat Cards
    ├── Tables & Toolbar
    ├── Split View & Detail Panel
    ├── Badges & Buttons
    ├── Modals
    ├── Forms & Autocomplete
    ├── Toast
    └── Coming Soon placeholder
```

---

## Fungsi JavaScript Utama

### Navigasi & UI

| Fungsi                        | Keterangan                                              |
|-------------------------------|---------------------------------------------------------|
| `toggleSidebar()`             | Buka/tutup sidebar, update class CSS                    |
| `toggleSub(subId, arrowId)`   | Buka/tutup sub-menu; buka sidebar dulu jika tertutup    |
| `goPage(pageId, el, title, parent)` | Pindah halaman, update breadcrumb, render modul  |
| `setDates()`                  | Isi elemen tanggal topbar & dashboard                   |

### Autentikasi

| Fungsi           | Keterangan                                        |
|------------------|---------------------------------------------------|
| `openLogin()`    | Buka modal login atau konfirmasi logout           |
| `closeLogin()`   | Tutup modal login                                 |
| `doLogin()`      | Validasi kredensial, set session, update UI       |
| `doLogout()`     | Hapus session, update UI                          |
| `updateLoginUI()`| Sinkronkan label tombol & badge dengan status login |

### Helper

| Fungsi      | Keterangan                                          |
|-------------|-----------------------------------------------------|
| `uid()`     | Generate ID unik (base36 timestamp + random)        |
| `rp(val)`   | Format angka ke string Rupiah (`Rp 1.000.000`)      |
| `hariIni()` | Return tanggal hari ini format `YYYY-MM-DD`         |
| `fmtTgl(str)` | Format `YYYY-MM-DD` ke tampilan Indonesia (`13 Mar 2025`) |

### Autocomplete

| Fungsi               | Keterangan                                      |
|----------------------|-------------------------------------------------|
| `acKode(val)`        | Saran kode barang dari semua data               |
| `pilihKode(kode)`    | Pilih kode → auto-fill semua field terkait      |
| `acNama(val)`        | Saran nama barang                               |
| `pilihNama(kode)`    | Pilih nama → panggil `pilihKode`                |
| `acKategori(val)`    | Saran kategori dari data existing               |
| `pilihKategori(val)` | Pilih kategori → update info alur data          |
| `acSupplier(val)`    | Saran supplier dari data masuk                  |
| `pilihSupplier(val)` | Pilih supplier → isi field supplier             |
| `updateAlurInfo()`   | Update teks info alur di form berdasarkan kode  |

### CRUD & Render (per modul)

| Fungsi                   | Modul          | Aksi                                      |
|--------------------------|----------------|-------------------------------------------|
| `bukaFormMasuk(id)`      | Barang Masuk   | Buka form tambah (id=null) atau edit      |
| `simpanBarangMasuk()`    | Barang Masuk   | Validasi & simpan ke localStorage         |
| `renderMasuk()`          | Barang Masuk   | Render tabel dengan filter aktif          |
| `selectMasuk(id)`        | Barang Masuk   | Tampilkan panel detail                    |
| `hapusMasukSelected()`   | Barang Masuk   | Buka modal konfirmasi hapus               |
| `konfirmasiHapusMasuk()` | Barang Masuk   | Eksekusi hapus, update stok & cashflow    |
| `renderKeluar()`         | Barang Keluar  | Render tabel keluar                       |
| `simpanBarangKeluar()`   | Barang Keluar  | Simpan, kurangi stok, tambah cashflow     |
| `renderStok()`           | Stok Barang    | Render tabel stok dengan status           |
| `selectStok(id)`         | Stok Barang    | Highlight item stok                       |
| `simpanEditStok()`       | Stok Barang    | Simpan perubahan data stok                |
| `renderAlat()`           | Alat Kerja     | Render tabel alat/inventori               |
| `renderCashflow()`       | Cashflow       | Agregasi & render tabel cashflow          |
| `renderOps()`            | Operasional    | Render tabel biaya operasional            |
| `simpanOps()`            | Operasional    | Simpan biaya, tambah ke cashflow          |
| `renderLaporan()`        | Laporan        | Hitung & render ringkasan bulanan         |
| `renderDashboard()`      | Dashboard      | Render semua widget dashboard             |

### Notifikasi & Alert

| Fungsi                          | Keterangan                                       |
|---------------------------------|--------------------------------------------------|
| `showToast(msg, type)`          | Tampilkan toast (success/error/info), auto-hilang |
| `toggleAlertDropdown()`         | Buka/tutup dropdown alert stok menipis           |
| `_renderAlertDropdown(stok)`    | Render isi dropdown berdasarkan data stok kritis |
| `bukaStokDariAlert(stokId)`     | Navigasi ke stok dan highlight item              |
| `renderAlertList(stok)`         | Render card alert di dashboard                   |
| `renderCatGrid(stok, alat)`     | Render grid kategori di dashboard                |

---

## Peringatan & Notifikasi

### Alert Stok Menipis
- Diaktifkan otomatis ketika **stok item ≤ nilai `stokMin`**
- Tombol alert di topbar beranimasi **pulse merah** jika ada item kritis
- **Merah (HABIS):** stok = 0 atau negatif
- **Kuning (Menipis):** stok > 0 tetapi ≤ stokMin

### Toast Notification

```
✅  success  — border kiri: lime neon
❌  error    — border kiri: merah
ℹ️  info     — border kiri: biru
```

Durasi tampil: ±2,8 detik sebelum fade out, hilang total pada detik ke-3,2.

---

## Keterbatasan & Catatan

1. **Data terikat browser:** Data tersimpan di `localStorage` browser yang digunakan. Membuka di browser/perangkat lain akan menghasilkan data kosong. Tidak ada sinkronisasi cloud.

2. **Kapasitas localStorage:** Browser umumnya membatasi localStorage ~5MB per origin. Untuk penggunaan intensif jangka panjang, data perlu dibersihkan atau diarsipkan secara manual.

3. **Tidak ada backup otomatis:** Jika cache/localStorage browser dihapus, semua data hilang permanen. Disarankan melakukan export data secara berkala (fitur export belum tersedia di v2).

4. **Keamanan login client-side:** Sistem autentikasi hanya untuk mencegah akses tidak disengaja, bukan keamanan tingkat enterprise. Kredensial tersimpan sebagai plain text di localStorage.

5. **Single user:** Tidak dirancang untuk penggunaan multi-pengguna simultan.

6. **Halaman "Coming Soon":** Beberapa item menu mungkin menampilkan placeholder `coming-soon` untuk fitur yang belum diimplementasikan di versi ini (contoh: Pengaturan).

7. **Ketergantungan Google Fonts:** Tanpa koneksi internet, font Outfit dan JetBrains Mono tidak akan dimuat (browser akan fallback ke font sistem).

8. **Tidak responsif penuh:** Desain dioptimalkan untuk layar desktop/laptop. Tampilan pada layar mobile mungkin tidak optimal.

---

## Informasi Versi

| Field      | Detail             |
|------------|--------------------|
| Versi      | v2 (`bengkel-pro-v2.html`) |
| Bahasa     | Indonesia          |
| Lisensi    | —                  |

---

*README ini dibuat berdasarkan analisis kode sumber `bengkel-pro-v2.html` secara menyeluruh.*
