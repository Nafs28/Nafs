**Nama : M. Daffa Annafis Rambe**
/ **NIM : 250180071**
/ **Kelas : A2**

**Filosofi & Pendekatan Desain**
Sistem ini memisahkan tanggung jawab kode ke dalam tiga lapisan (3-Tier Layered Architecture) sederhana tanpa framework, sehingga kode bersih, mudah dirawat, dan siap dikembangkan lebih lanjut:

Data Layer (products.php): Berperan sebagai penyimpan state data mentah (single source of truth). Tidak memiliki logika kalkulasi maupun elemen HTML.
Processing Layer (functions.php): Berisi pure functions dan logika bisnis. Mengolah data mentah menjadi metrik (total aset) serta menentukan aturan status stok.
Presentation Layer (index.php): Bertindak sebagai controller dan view, memuat berkas dependensi menggunakan require_once, lalu menyusun tampilan tabel HTML interaktif berbasis logika perulangan foreach.

A. Data Layer (products.php)
Bentuk Data: Array multidimensi asosiatif.
Skema Atribut:
- id (integer/string): Pengenal unik produk.
- nama (string): Nama komoditas.
- kategori (string): Klasifikasi barang (contoh: Elektronik, Pangan, Pakaian).
- harga (float/integer): Nilai harga satuan.
- stok (integer): Kuantitas stok fisik di gudang.
- deskripsi (string): Rincian singkat spesifikasi produk.

B. Processing Layer (functions.php)
Fungsi Utama:
hitungTotalNilaiStok(array $products): float
- Algoritma: Iterasi seluruh elemen array dan menghitung akumulasi perkalian (harga × stok).
getStatusStok(int $stok): array / getRowColorByStock(int $stok): string
- Logika Kondisional: Jika 
stok < 3, kembalikan class CSS peringatan kritis (misal: warna merah muda / table-danger) dan label status "Kritis". Jika ≥ 3, kembalikan status normal.
formatRupiah(float $angka): string (Fungsi Bantu):
- Standardisasi output mata uang ke format Indonesia (Rp ...).

C. Presentation Layer (index.php)
- Alur Rendering:
Menghitung ringkasan (KPI Cards): Total jenis produk, total akumulasi nilai aset, dan jumlah produk stok kritis.
Merender tabel HTML: Setiap baris produk dievaluasi menggunakan fungsi logika stok untuk menentukan warna latar belakang baris secara dinamis.
