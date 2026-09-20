**Nama : M. Daffa Annafis Rambe**
/ **NIM : 250180071**
/ **Kelas : A2**

# Sistem Manajemen Data Informasi Produk (Product Data Management System)

Aplikasi web berbasis PHP native yang dirancang untuk mengelola dan memvisualisasikan data komoditas produk gudang. Proyek ini mengimplementasikan konsep modularisasi **3-Tier Layered Architecture** (Data Layer, Processing Layer, dan Presentation Layer) untuk memisahkan struktur data, logika bisnis, dan antarmuka pengguna.

---

## Arsitektur Sistem

Proyek ini terbagi menjadi 3 komponen utama:

### 1. Data Layer (`products.php`)
Menyediakan data komoditas produk menggunakan *multidimensional associative array*. Setiap entri memiliki skema:
- `id` (int): Identifikasi unik produk.
- `nama` (string): Nama produk.
- `kategori` (string): Kategori produk.
- `harga` (float): Harga satuan barang.
- `stok` (int): Jumlah stok unit di gudang.
- `deskripsi` (string): Keterangan singkat produk.

### 2. Processing Layer (`functions.php`)
Mengisolasi seluruh logika fungsional agar tidak bercampur dengan kode presentasi:
- **`hitungTotalNilaiStok(array $products): float`**: Mengakumulasikan total aset gudang berdasarkan rumus:
  $$\text{Total Nilai} = \sum_{i=1}^{n} (\text{Harga}_i \times \text{Stok}_i)$$
- **`getAlertStokClass(int $stok): string`**: Menerapkan evaluasi kondisional. Apabila `stok < 3`, fungsi mengembalikan penanda baris kritis (indikator merah) sebagai sinyal *restock*.
- **`formatRupiah(float $angka): string`**: Memformat angka ke representasi mata uang Rupiah (`Rp x.xxx.xxx`).

### 3. Presentation Layer (`index.php`)
- Memuat dependensi data dan fungsi menggunakan `require_once`.
- Merender ringkasan metrik gudang (*Dashboard Cards*).
- Menampilkan data tabel secara dinamis menggunakan perulangan `foreach`.

---

## Panduan Menjalankan Proyek

### Prasyarat
- **PHP** versi 7.4 atau lebih baru terpasang di komputer.
- Web browser (Chrome, Firefox, Edge, dll).

### Langkah Instalasi & Eksekusi

1. **Clone atau Simpan Berkas**:
   Pastikan seluruh berkas (`products.php`, `functions.php`, dan `index.php`) berada dalam satu direktori kerja.

2. **Jalankan PHP Built-in Server**:
   Buka terminal/PowerShell di direktori tersebut, lalu jalankan:
   ```bash
   php -S localhost:8000

3. **Buka Aplikasi: Akses URL berikut melalui browser:**
http://localhost:8000

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

1. products.php (Data Layer)
<?php
// products.php - Data Layer

$products = [
    [
        'id' => 1,
        'nama' => 'Laptop Ultra Slim 14"',
        'kategori' => 'Elektronik',
        'harga' => 8500000,
        'stok' => 5,
        'deskripsi' => 'Laptop ringan dengan prosesor generasi terbaru.'
    ],
    [
        'id' => 2,
        'nama' => 'Mouse Wireless Silent',
        'kategori' => 'Aksesoris',
        'harga' => 150000,
        'stok' => 2, // Stok Kritis (< 3)
        'deskripsi' => 'Mouse ergonomis tanpa kabel dengan klik senyap.'
    ],
    [
        'id' => 3,
        'nama' => 'Keyboard Mechanical TKL',
        'kategori' => 'Aksesoris',
        'harga' => 650000,
        'stok' => 1, // Stok Kritis (< 3)
        'deskripsi' => 'Keyboard switch tactile untuk kenyamanan mengetik.'
    ],
    [
        'id' => 4,
        'nama' => 'Monitor 24" IPS 100Hz',
        'kategori' => 'Elektronik',
        'harga' => 1750000,
        'stok' => 8,
        'deskripsi' => 'Layar jernih resolusi Full HD sudut pandang luas.'
    ]
];

2. functions.php (Processing Layer)
<?php
// functions.php - Processing Layer

/**
 * Menghitung total nilai aset barang di gudang.
 */
function hitungTotalNilaiStok(array $products): float {
    $total = 0;
    foreach ($products as $item) {
        $total += ($item['harga'] * $item['stok']);
    }
    return $total;
}

/**
 * Mengembalikan class CSS berdasarkan batas kritis stok (< 3).
 */
function getAlertStokClass(int $stok): string {
    return ($stok < 3) ? 'row-critical' : 'row-normal';
}

/**
 * Format angka ke mata uang Rupiah.
 */
function formatRupiah(float $angka): string {
    return 'Rp ' . number_format($angka, 0, ',', '.');
}

3. index.php (Presentation Layer)
    <h1>📦 Sistem Inventaris Produk</h1>
    
    <div class="card">
        <h3>Total Nilai Aset Gudang: <strong><?= formatRupiah($totalNilaiGudang); ?></strong></h3>
        <p>Total Jenis Komoditas: <?= count($products); ?> Item</p>
    </div>

    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Nama Produk</th>
                <th>Kategori</th>
                <th>Harga Satuan</th>
                <th>Stok</th>
                <th>Status</th>
                <th>Subtotal</th>
                <th>Deskripsi</th>
            </tr>
        </thead>
        <tbody>
            <?php foreach ($products as $product): ?>
                <?php 
                    $isKritis = $product['stok'] < 3;
                    $rowClass = getAlertStokClass($product['stok']);
                    $subtotal = $product['harga'] * $product['stok'];
                ?>
                <tr class="<?= $rowClass; ?>">
                    <td><?= htmlspecialchars($product['id']); ?></td>
                    <td><?= htmlspecialchars($product['nama']); ?></td>
                    <td><?= htmlspecialchars($product['kategori']); ?></td>
                    <td><?= formatRupiah($product['harga']); ?></td>
                    <td><?= $product['stok']; ?> unit</td>
                    <td>
                        <?php if ($isKritis): ?>
                            <span class="badge-danger">Stok Kritis!</span>
                        <?php else: ?>
                            <span>Tersedia</span>
                        <?php endif; ?>
                    </td>
                    <td><?= formatRupiah($subtotal); ?></td>
                    <td><?= htmlspecialchars($product['deskripsi']); ?></td>
                </tr>
            <?php endforeach; ?>
        </tbody>
    </table>

</body>
</html>

**Keuntungan Desain (Best Practices)**
- Maintainability: Jika ingin mengganti sumber data (misal ke database MySQL), hanya file products.php yang perlu diubah.
- Reusability: Fungsi hitungTotalNilaiStok() dapat digunakan berulang kali di halaman laporan lain tanpa perlu menulis ulang logika perkalian dan penjumlahan.
- Clean Code: Tidak ada tag HTML yang tercampur di dalam kalkulasi logika bisnis, dan tidak ada kalkulasi berat yang ditulis di dalam file view.
