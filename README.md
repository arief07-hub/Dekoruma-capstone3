# Analisis Sinkronisasi Pengiriman dan Perakitan Furnitur Dekoruma

Capstone Project Modul 3, Purwadhika Digital Technology School
**Muhammad Arief Setiawan**

Saya menganalisis 300.000 pesanan furnitur Dekoruma untuk menjawab satu hal: **seberapa buruk
sinkronisasi jadwal pengiriman dan perakitan, dan apa penyebabnya.**

---

## Latar belakang

Dekoruma menjual furnitur lewat aplikasi, website, dan 38 toko fisik (*Dekoruma Experience
Center*).

Furnitur tidak seperti barang kecil. Satu pesanan tidak selesai dalam satu kali kedatangan, tapi
**dua**. Truk mengantar barangnya, lalu tukang datang merakitnya. Dua kedatangan berarti dua
jadwal yang harus diselaraskan, dan di situlah letak rapuhnya.

Kalau keduanya tidak sinkron, pelanggan menunggu berhari-hari dengan kardus menumpuk di rumah.
Waktu tukang akhirnya datang pun, perakitan masih bisa gagal karena komponen kurang atau panel
rusak.

---

## Pertanyaan yang saya jawab

1. Berapa **rata-rata selisih hari** antara barang tiba dan tukang datang merakit?
2. **Kategori furnitur apa** yang paling sering gagal dirakit?
3. **Seberapa berantakan** pencatatan kategori furnitur, dan bagaimana saya menstandarisasinya?
4. Berapa pesanan yang **tanggal perakitannya mendahului** tanggal barang tiba?
5. Berapa **nilai biaya perakitan yang tidak wajar**, dan dari mana asalnya?

---

## Temuan

### 1. Pelanggan menunggu rata-rata 7 hari, dan jadwalnya bukan telat tapi acak

Saya hitung waktu tunggu yang benar-benar dirasakan pelanggan, dari bayar sampai barang
terpasang. Hasilnya **2 sampai 12 hari, rata-rata 7,01 hari**. Ada **31.047 pesanan atau 16,7%**
yang menunggu 10 hari ke atas.

Sebarannya berbentuk segitiga. Saya uji apakah itu kebetulan.

**Terlihat pada tabel** angka teramati hampir sama persis dengan angka kalau dua tahap
dijadwalkan acak:

| Hari | Teramati | Kalau dua tahap acak |
|---|---|---|
| 2 | 5.152 | 5.158 |
| 5 | 20.581 | 20.634 |
| **7** | **31.285** | 30.951 |
| 10 | 15.438 | 15.475 |
| 12 | 5.226 | 5.158 |

Rata-rata melesetnya cuma **0,75%** dari bobot teoretis segitiga (1,2,3,4,5,6,5,4,3,2,1 dari 36).
Korelasi lama pengiriman dengan selisih perakitan **−0,0007**.

Jadi kedua jadwal disusun sendiri-sendiri dan tidak saling melihat sama sekali. Masalahnya bukan
telat, tapi tidak ada koordinasi.

Yang memenuhi target selisih ≤1 hari cuma **33,2%**, yaitu 61.656 dari 185.705. Selisih
rata-ratanya **2,51 hari**, median 3, rentangnya 0 sampai 5 hari. Tiap nilai dihuni sekitar
30.951 pesanan dengan simpangan di bawah 1%. Sebaran serata itu cuma muncul kalau tanggal dipilih
tanpa aturan.

### 2. Satu dari lima perakitan gagal, dan tidak ada yang bisa disalahkan

**19,93%** perakitan gagal, yaitu 37.918 dari 190.275 pesanan yang benar-benar dirakit.
**59,7%** karena komponen kurang, **40,3%** karena panel rusak.

Saya uji satu per satu setiap tersangka yang masuk akal. Tidak ada yang terbukti.

**Terlihat pada tabel** seluruh p-value jauh di atas 0,05:

| Yang saya uji | Metode | p-value | Kesimpulan |
|---|---|---|---|
| Kegagalan antar **kategori** | Chi-square | 0,5588 | tidak beda nyata |
| Kegagalan antar **lokasi** | Chi-square | 0,2268 | tidak beda nyata |
| Kegagalan antar **toko** (38) | Chi-square | 0,2491 | tidak beda nyata |
| Kegagalan antar **saluran** | Chi-square | 0,1075 | tidak beda nyata |
| Kegagalan antar **bulan** | Chi-square | 0,7172 | tidak beda nyata |
| Kegagalan antar **tukang** (999) | Chi-square | 0,0835 | tidak beda nyata |

Beda antar kategori cuma **0,80 poin persen**, dari 19,60% sampai 20,40%.

Soal 999 tukang, kegagalan terendahnya 9,1% dan tertinggi 32,7%. Sekilas itu kelihatan seperti
beda keterampilan. Tapi simpangan bakunya **0,0299**, sementara kalau murni kebetulan seharusnya
**0,0289**. Rasionya **1,034**, jadi sebaran nyatanya cuma 3,4% lebih lebar daripada kebetulan.
Tukang yang jatuh di luar rentang 95% ada **56 dari 999**, padahal yang diharapkan 50.

Menurut saya penyebabnya sistem, bukan orang.

### 3. Sebagian "human error" ternyata bukan manusia

**4.570 pesanan** tercatat dirakit sebelum barangnya sampai. Semuanya berstatus `Success`. Saya
cek jamnya, dan jam `delivery_date` sama persis dengan jam `installation_date` di **190.275 dari
190.275** baris.

**1.893 nilai biaya perakitan** ada di luar daftar tarif resmi. Kalau ini salah ketik kasir,
nilainya pasti bermacam-macam. Yang ada justru cuma dua nilai yang berulang ratusan kali, yaitu
**−150.000 sebanyak 960 kali** dan **99.900.000 sebanyak 933 kali**. Rentang antara 250.000 sampai
99.900.000 kosong sempurna.

Dua-duanya terlalu rapi untuk kesalahan orang. Menurut saya keduanya berasal dari sistem.

### 4. Standarisasi kategori mengubah peringkat tanpa mengubah satu pesanan pun

Kolom kategori berisi **11 label untuk 8 kategori sebenarnya**. Yang saya satukan: `Office Chair`
jadi `Kursi Kerja`, `Dining Table` jadi `Meja Makan`, `lmri` jadi `Lemari`, `sofabed` jadi
`Sofa Bed`. `Wardrobe` dan `Sofa` sengaja saya biarkan terpisah karena memang barang berbeda.

Yang terdampak **395 SKU dan 78.502 pesanan**, atau 26,2% dari seluruh pesanan.

Sebelum saya standarkan, `Sofa` kelihatan jadi kategori terbesar dengan 42.998 pesanan.
Sesudahnya yang terbesar **`Kursi Kerja` dengan 65.922**, karena separuhnya tadi tercatat sebagai
`Office Chair`. Jadi kategori terbesar salah tunjuk sebelum datanya dibersihkan.

---

## Cara saya membersihkan data

### Prinsipnya: tandai, jangan hapus

**Tidak ada satu baris pun yang saya hapus dari 300.000 pesanan.** Yang saya kosongkan cuma nilai
di kolom yang bermasalah, jadi kolom lain di baris itu masih bisa dipakai.

| Anomali | Jumlah | Yang saya lakukan |
|---|---|---|
| Kategori tidak standar | 4 label, 78.502 pesanan | disatukan jadi 8 kategori |
| `installer_id` dan `installation_date` kosong | 109.725 | **dibiarkan**, datanya memang tidak berlaku |
| Tanggal perakitan mendahului pengiriman | 4.570 | ditandai, `gap_hari` dikosongkan |
| Biaya negatif dan ekstrem | 1.893 | ditandai, nilainya dikosongkan |
| Kolom tanggal masih teks | 3 kolom | diubah jadi tipe tanggal |

**Kenapa saya tidak pakai `dropna()`?** 109.725 baris yang `installer_id`-nya kosong itu **sah**.
Semuanya berstatus `No Assembly`, jadi barangnya memang tidak dirakit. Kalau saya hapus, saya
membuang 36,58% data yang sebenarnya benar.

**Kenapa 4.570 tanggal terbalik tidak saya hapus?** Karena semuanya berstatus `Success`. Saya
sudah coba hapus, dan tingkat kegagalan naik dari **19,93% jadi 20,42%** padahal tidak ada satu
perakitan pun yang berubah jadi gagal. Naiknya cuma karena pembaginya berkurang.

**Kenapa 1.893 biaya anomali juga tidak saya hapus?** Sudah saya cek dampaknya. Tingkat kegagalan
cuma bergeser 0,0097 poin persen dan rata-rata biayanya identik, jadi menghapus tidak memberi
keuntungan apa pun. Sementara baris-baris itu masih punya produk, toko, tukang, dan status
perakitan yang sah. Yang salah cuma kolom biayanya.

### Pembagi berbeda untuk pertanyaan berbeda

Ini keputusan yang paling menentukan benar tidaknya angka saya. Tiap perhitungan saya beri
pembagi yang sesuai dengan populasi yang benar-benar berlaku untuknya.

| Keperluan | Pembagi | Alasan |
|---|---|---|
| Porsi kategori dan saluran | **300.000** | semua pesanan punya kategori dan saluran |
| Tingkat kegagalan | **190.275** | cuma yang dirakit yang bisa gagal |
| Selisih hari dan waktu tunggu | **185.705** | 190.275 dikurangi 4.570 tanggal terbalik |
| Biaya jasa | **298.107** | 300.000 dikurangi 1.893 biaya tidak sah |

Kalau tingkat kegagalan saya bagi dengan 300.000, hasilnya **12,64%**. Menyusut lebih dari
sepertiga, cuma karena 109.725 pesanan yang tidak pernah dirakit ikut terhitung sebagai "tidak
gagal".

---

## Rekomendasi

| | Rekomendasi | Goal | Target terukur |
|---|---|---|---|
| **R1** | Satukan penjadwalan truk dan tukang dalam satu proses | Logistics Sync | selisih ≤1 hari **33,2% jadi 60%** (6 bln), lalu **75%** (12 bln) |
| **R2** | Periksa kelengkapan komponen sebelum barang keluar gudang | Supplier QC | kegagalan **19,93% jadi 15%** (12 bln), sekitar 9.300 lebih sedikit |
| **R3** | Catat `supplier_id` di tiap SKU | Supplier QC | 100% SKU baru (3 bln), seluruh 1.500 (6 bln) |
| **R4** | Pasang validasi urutan tanggal waktu input | Data Cleansing | tanggal mustahil **4.570 jadi 0** (3 bln) |
| **R5** | Telusuri dua nilai biaya baku, lalu batasi nilai yang sah | Data Cleansing | biaya tidak sah **1.893 jadi 0** (3 bln) |

Saya hitung dampaknya kalau aturan selisih ≤1 hari dipasang, tanpa mengubah lama pengiriman sama
sekali:

| Ukuran | Sekarang | Sesudah |
|---|---|---|
| Rata-rata waktu tunggu | 7,01 hari | **5,01 hari** |
| Waktu tunggu terlama | 12 hari | **8 hari** |
| Menunggu ≥10 hari | 31.047 | **0** |

Rekomendasi ini **tidak minta tambahan truk atau tukang**. Perakitan di hari yang sama sudah
terjadi **30.907 kali** tanpa aturan apa pun, jadi kapasitasnya sudah ada.

### Satu hal yang saya nyatakan terus terang

Selisih hari dan kegagalan perakitan **tidak berhubungan**. Mann-Whitney p = **0,6072**.
Memperbaiki jadwal tidak akan menurunkan kegagalan. Keduanya masalah terpisah dengan rekomendasi
masing-masing, dan saya tidak menggabungkannya cuma supaya ceritanya lebih rapi.

---

## Batas data

Ada tiga hal yang tidak bisa dijawab analisis ini. Menurut saya menyebutkannya lebih berguna
daripada memaksakan jawaban.

**1. Perilaku pelanggan lintas saluran tidak terukur.** Tidak ada `customer_id`, alamat, maupun
kota pelanggan. Yang tercatat cuma di mana transaksi dilakukan, bukan siapa yang membeli.

**2. Identitas pemasok tidak tercatat.** Jadi pemasok dengan `Missing Parts` tertinggi tidak bisa
saya identifikasi. Kategori produk juga tidak bisa jadi penggantinya, karena beda antar kategori
terbukti tidak bermakna.

**3. Biaya retur dan kepuasan pelanggan tidak tercatat.** Karena itu saya **tidak menyebut satu pun
angka rupiah kerugian**. Yang saya ukur cuma jumlah pesanan, jumlah hari, tingkat kegagalan, dan
jumlah catatan tidak sah.

---

## Data

Datanya tiga tabel yang saling terhubung. `orders` tabel fakta yang mencatat kejadian, dua lainnya
tabel dimensi yang cuma menerangkan.

| Tabel | Baris | Isi | Kunci |
|---|---|---|---|
| `dekoruma_orders.csv` | 300.000 | catatan pesanan dan perakitan | `order_id` |
| `dekoruma_products.csv` | 1.500 | daftar produk dan kategorinya | `sku_id` |
| `dekoruma_stores.csv` | 40 | daftar saluran penjualan | `store_id` |

Kolom turunan yang saya buat: `lead_hari` (pesan sampai barang tiba), `gap_hari` (barang tiba
sampai selesai dirakit), `tunggu_hari` (jumlah keduanya), `gagal`, `bulan`, `lokasi`,
`tanggal_valid`, `biaya_valid`.

---

Tools yang saya pakai: Python, pandas, NumPy, Matplotlib, Seaborn, SciPy.

---

## Catatan

Dataset ini disediakan Purwadhika untuk keperluan belajar. Sebagian anomali di dalamnya sengaja
ditanam sebagai latihan pembersihan data, jadi angka-angka di sini **tidak menggambarkan kinerja
Dekoruma yang sebenarnya**.