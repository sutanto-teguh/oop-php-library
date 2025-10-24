# 📘 **MODUL KULIAH**

## **MEMBUAT LIBRARY SEDERHANA BERBASIS OOP MENGGUNAKAN PHP**

**Penulis:** Teguh Sutanto, M.Kom.\
**Tahun:** 2025

------------------------------------------------------------------------

## **KATA PENGANTAR**

Puji syukur penulis panjatkan ke hadirat Tuhan Yang Maha Esa atas segala
rahmat dan karunia-Nya, sehingga modul dengan judul *"Membuat Library
Sederhana Berbasis OOP Menggunakan PHP"* ini dapat tersusun dengan baik.

Modul ini disusun sebagai bahan ajar dan panduan praktikum bagi
mahasiswa untuk memahami konsep *Object-Oriented Programming (OOP)*
dalam bahasa pemrograman PHP melalui pendekatan berbasis proyek
sederhana. Dengan mempelajari modul ini, diharapkan mahasiswa mampu
mengembangkan kemampuan analisis, perancangan, dan implementasi kode
program yang modular, efisien, serta mudah digunakan kembali
(*reusable*).

Penulis menyadari bahwa modul ini masih memiliki keterbatasan. Oleh
karena itu, kritik dan saran yang membangun sangat diharapkan demi
penyempurnaan pada edisi berikutnya. Semoga modul ini dapat memberikan
manfaat dan menjadi referensi yang berguna bagi proses pembelajaran di
bidang pemrograman berbasis OOP.

**Yogyakarta, 2025**\
Penulis,\
**Teguh Sutanto, M.Kom.**

------------------------------------------------------------------------

## **DAFTAR ISI**

1.  Pendahuluan & Konsep OOP di PHP\
2.  Membuat Library Matematika & Formatter\
3.  Membuat Library CRUD Berbasis File (JSON)\
4.  Membuat Library CRUD Berbasis Database (MySQL)\
5.  Latihan & Evaluasi

------------------------------------------------------------------------

## **BAB 1 --- PENDAHULUAN & KONSEP OOP DI PHP**

### 1.1 Apa itu OOP?

*Object-Oriented Programming (OOP)* adalah paradigma pemrograman yang
berfokus pada objek. Setiap objek memiliki **atribut (property)** dan
**perilaku (method)**.

### 1.2 Empat Pilar OOP

1.  **Encapsulation** -- menyembunyikan detail data dari luar class.\
2.  **Inheritance** -- pewarisan perilaku dari class induk ke turunan.\
3.  **Polymorphism** -- satu antarmuka, banyak implementasi.\
4.  **Abstraction** -- menyederhanakan kompleksitas dengan ide umum.

### 1.3 Keuntungan OOP

-   Kode lebih terstruktur dan mudah dikelola.\
-   Dapat digunakan kembali (*reusable*).\
-   Lebih mudah untuk diuji dan dikembangkan.

------------------------------------------------------------------------

## **BAB 2 --- MEMBUAT LIBRARY MATEMATIKA & FORMATTER**

### 2.1 Struktur Folder

    /library-tutorial
    │
    ├── index.php
    └── src/
        ├── Math/
        │   └── Calculator.php
        └── Utils/
            └── Formatter.php

### 2.2 Class Calculator

``` php
<?php
namespace Library\Math;

class Calculator {
    private $result = 0;

    public function add($a, $b) {
        $this->result = $a + $b;
        return $this->result;
    }

    public function subtract($a, $b) {
        $this->result = $a - $b;
        return $this->result;
    }

    public function multiply($a, $b) {
        $this->result = $a * $b;
        return $this->result;
    }

    public function divide($a, $b) {
        if ($b == 0) {
            throw new \Exception("Pembagian dengan nol tidak diperbolehkan.");
        }
        $this->result = $a / $b;
        return $this->result;
    }

    public function getResult() {
        return $this->result;
    }
}
```

### 2.3 Class Formatter

``` php
<?php
namespace Library\Utils;

class Formatter {
    public static function currency($number, $prefix = "Rp ") {
        return $prefix . number_format($number, 0, ',', '.');
    }

    public static function percent($number) {
        return number_format($number, 2) . '%';
    }
}
```

### 2.4 Penggunaan

``` php
<?php
require_once __DIR__ . '/src/Math/Calculator.php';
require_once __DIR__ . '/src/Utils/Formatter.php';

use Library\Math\Calculator;
use Library\Utils\Formatter;

$calc = new Calculator();

$hasil = $calc->add(10, 5);
echo "Hasil Penjumlahan: " . $hasil . PHP_EOL;
echo "Format Mata Uang: " . Formatter::currency($hasil) . PHP_EOL;
```

Output:

    Hasil Penjumlahan: 15
    Format Mata Uang: Rp 15

------------------------------------------------------------------------

## **BAB 3 --- LIBRARY CRUD BERBASIS FILE (JSON)**

### 3.1 Struktur Folder

    /library-tutorial
    │
    ├── index.php
    └── src/
        ├── Entity/
        │   └── Barang.php
        ├── Repository/
        │   └── BarangRepository.php
        └── data/
            └── barang.json

### 3.2 Class Barang

``` php
<?php
namespace Library\Entity;

class Barang {
    private string $nama;
    private float $harga;
    private int $stok;

    public function __construct(string $nama, float $harga, int $stok) {
        $this->nama = $nama;
        $this->harga = $harga;
        $this->stok = $stok;
    }

    public function getNama(): string {
        return $this->nama;
    }

    public function setNama(string $nama): void {
        $this->nama = $nama;
    }

    public function getHarga(): float {
        return $this->harga;
    }

    public function setHarga(float $harga): void {
        $this->harga = $harga;
    }

    public function getStok(): int {
        return $this->stok;
    }

    public function setStok(int $stok): void {
        $this->stok = $stok;
    }

    public function toArray(): array {
        return [
            'nama' => $this->nama,
            'harga' => $this->harga,
            'stok' => $this->stok
        ];
    }
}
```

### 3.3 Repository File JSON

``` php
<?php
namespace Library\Repository;

use Library\Entity\Barang;

class BarangRepository {
    private string $filePath;

    public function __construct(string $filePath) {
        $this->filePath = $filePath;
        if (!file_exists($filePath)) {
            file_put_contents($filePath, json_encode([]));
        }
    }

    public function tambah(Barang $barang): void {
        $data = $this->bacaSemua();
        $data[] = $barang->toArray();
        file_put_contents($this->filePath, json_encode($data, JSON_PRETTY_PRINT));
    }

    public function bacaSemua(): array {
        $content = file_get_contents($this->filePath);
        return json_decode($content, true) ?? [];
    }

    public function update(string $nama, Barang $barangBaru): bool {
        $data = $this->bacaSemua();
        foreach ($data as $key => $item) {
            if ($item['nama'] === $nama) {
                $data[$key] = $barangBaru->toArray();
                file_put_contents($this->filePath, json_encode($data, JSON_PRETTY_PRINT));
                return true;
            }
        }
        return false;
    }

    public function hapus(string $nama): bool {
        $data = $this->bacaSemua();
        foreach ($data as $key => $item) {
            if ($item['nama'] === $nama) {
                unset($data[$key]);
                file_put_contents($this->filePath, json_encode(array_values($data), JSON_PRETTY_PRINT));
                return true;
            }
        }
        return false;
    }
}
```

### 3.4 Uji Coba CRUD JSON

``` php
<?php
require_once __DIR__ . '/src/Entity/Barang.php';
require_once __DIR__ . '/src/Repository/BarangRepository.php';

use Library\Entity\Barang;
use Library\Repository\BarangRepository;

$filePath = __DIR__ . '/src/data/barang.json';
$repo = new BarangRepository($filePath);

$barang1 = new Barang("Panah Tradisional", 150000, 10);
$barang2 = new Barang("Busur Kayu", 300000, 5);

$repo->tambah($barang1);
$repo->tambah($barang2);

print_r($repo->bacaSemua());

$barangBaru = new Barang("Panah Tradisional", 150000, 20);
$repo->update("Panah Tradisional", $barangBaru);

$repo->hapus("Busur Kayu");

print_r($repo->bacaSemua());
```

------------------------------------------------------------------------

## **BAB 4 --- CRUD BERBASIS DATABASE (MYSQL)**

### 4.1 Struktur Tambahan

    /src/config/database.php
    /src/Repository/BarangRepositoryDB.php
    index-db.php

### 4.2 Koneksi Database (PDO)

``` php
<?php
namespace Library\Config;

use PDO;
use PDOException;

class Database {
    private string $host = "localhost";
    private string $dbname = "db_barang";
    private string $username = "root";
    private string $password = "";
    private ?PDO $conn = null;

    public function connect(): ?PDO {
        try {
            $this->conn = new PDO(
                "mysql:host={$this->host};dbname={$this->dbname}",
                $this->username,
                $this->password
            );
            $this->conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        } catch (PDOException $e) {
            echo "Koneksi gagal: " . $e->getMessage();
        }
        return $this->conn;
    }
}
```

### 4.3 Repository Database

``` php
<?php
namespace Library\Repository;

use Library\Entity\Barang;
use PDO;

class BarangRepositoryDB {
    private PDO $conn;

    public function __construct(PDO $connection) {
        $this->conn = $connection;
    }

    public function tambah(Barang $barang): void {
        $stmt = $this->conn->prepare("INSERT INTO barang (nama, harga, stok) VALUES (:nama, :harga, :stok)");
        $stmt->execute([
            ':nama' => $barang->getNama(),
            ':harga' => $barang->getHarga(),
            ':stok' => $barang->getStok()
        ]);
    }

    public function bacaSemua(): array {
        $stmt = $this->conn->query("SELECT * FROM barang");
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    public function update(string $nama, Barang $barangBaru): bool {
        $stmt = $this->conn->prepare("UPDATE barang SET nama=:namaBaru, harga=:harga, stok=:stok WHERE nama=:namaLama");
        return $stmt->execute([
            ':namaBaru' => $barangBaru->getNama(),
            ':harga' => $barangBaru->getHarga(),
            ':stok' => $barangBaru->getStok(),
            ':namaLama' => $nama
        ]);
    }

    public function hapus(string $nama): bool {
        $stmt = $this->conn->prepare("DELETE FROM barang WHERE nama=:nama");
        return $stmt->execute([':nama' => $nama]);
    }
}
```

### 4.4 File Uji Coba

``` php
<?php
require_once __DIR__ . '/src/Entity/Barang.php';
require_once __DIR__ . '/src/Repository/BarangRepositoryDB.php';
require_once __DIR__ . '/src/config/database.php';

use Library\Entity\Barang;
use Library\Repository\BarangRepositoryDB;
use Library\Config\Database;

$db = new Database();
$conn = $db->connect();
$repo = new BarangRepositoryDB($conn);

$barang1 = new Barang("Panah Bambu", 120000, 15);
$barang2 = new Barang("Busur Fiberglass", 400000, 7);

$repo->tambah($barang1);
$repo->tambah($barang2);

print_r($repo->bacaSemua());

$barangBaru = new Barang("Panah Bambu", 130000, 25);
$repo->update("Panah Bambu", $barangBaru);

$repo->hapus("Busur Fiberglass");

print_r($repo->bacaSemua());
```

### 4.5 Struktur Tabel MySQL

``` sql
CREATE DATABASE db_barang;

USE db_barang;

CREATE TABLE barang (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nama VARCHAR(100) NOT NULL,
    harga DOUBLE NOT NULL,
    stok INT NOT NULL
);
```

------------------------------------------------------------------------

## **BAB 5 --- LATIHAN DAN EVALUASI**

### Latihan Mahasiswa

1.  Tambahkan method `cariByNama($nama)` pada `BarangRepositoryDB`.\
2.  Buat tampilan web sederhana untuk menampilkan semua data barang.\
3.  Bandingkan performa antara versi file JSON dan database menggunakan
    `microtime()`.

### Pertanyaan Diskusi

1.  Apa manfaat *encapsulation* dalam menjaga integritas data class?\
2.  Mengapa *repository pattern* penting dalam pemisahan logika bisnis?\
3.  Bagaimana penerapan *abstraction* membantu dalam pergantian media
    penyimpanan?
