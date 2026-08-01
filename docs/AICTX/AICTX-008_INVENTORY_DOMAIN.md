---
document:
  id: AICTX-008
  title: Inventory Domain
  version: 1.0.0
  status: Locked
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan aturan bisnis, konsep domain, dan proses pengelolaan persediaan pada BusinessOS. Dokumen ini menjadi acuan implementasi Inventory Domain, Inventory Service, Stock Engine, serta integrasi dengan Purchasing, Sales, POS, dan Finance.

dependencies:
  - 001_PROJECT_CONTEXT.md
  - 002_PRODUCT_REQUIREMENTS.md
  - 003_SYSTEM_ARCHITECTURE.md
  - 004_TECH_STACK.md
  - 005_DATABASE_DESIGN.md
  - 006_AUTHENTICATION_AND_RBAC.md
  - 007_FINANCE_MODULE.md

next_documents:
  - 009_POS_DOMAIN.md
---

# 1. Executive Summary

Inventory Domain merupakan satu-satunya domain yang bertanggung jawab terhadap perubahan kuantitas dan nilai persediaan.

Seluruh modul lain tidak diperbolehkan mengubah stok secara langsung.

Perubahan stok hanya dapat terjadi melalui Inventory Transaction yang diproses oleh Inventory Domain.

Inventory Domain menjadi Source of Truth untuk seluruh informasi persediaan.

---

# 2. Inventory Principles

## IP-001

Inventory Domain merupakan Source of Truth untuk seluruh data stok.

---

## IP-002

Tidak ada modul yang diperbolehkan mengubah stok secara langsung selain Inventory Domain.

---

## IP-003

Seluruh perubahan stok harus memiliki Business Event sebagai pemicunya.

---

## IP-004

Setiap perubahan stok harus dapat ditelusuri hingga BusinessTransaction yang menjadi asalnya.

---

## IP-005

Inventory Domain harus mendukung arsitektur Offline-First.

---

## IP-006

Nilai persediaan dan kuantitas persediaan merupakan dua konsep yang berbeda, tetapi saling berkaitan.

---

## IP-007

Seluruh mutasi stok wajib menghasilkan jejak audit (Audit Trail).

---

# 3. Inventory Concepts

Inventory Domain mengelola beberapa konsep utama berikut.

## Product

Representasi barang atau jasa yang ditawarkan oleh Organization.

Inventory Domain hanya mengelola produk yang memiliki karakteristik persediaan.

---

## Product Catalog

Katalog identitas produk.

Menyimpan informasi seperti:

- SKU
- Barcode
- Nama Produk
- Kategori
- Satuan
- Varian
- Gambar
- Status

Product Catalog tidak menyimpan saldo stok.

---

## Inventory Item

Representasi produk yang dikelola sebagai persediaan.

Inventory Item menghubungkan Product Catalog dengan data stok dan valuasi.

---

## Stock

Jumlah fisik suatu Inventory Item pada lokasi tertentu.

---

## Stock Movement

Catatan seluruh perubahan kuantitas stok.

Stock Movement bersifat historis dan tidak dapat diubah setelah diposting.

---

## Stock Valuation

Representasi nilai finansial dari persediaan.

Nilai persediaan dihitung berdasarkan metode valuasi yang ditetapkan oleh Organization.

---

## Warehouse

Lokasi logis penyimpanan persediaan.

Satu Branch dapat memiliki satu atau lebih Warehouse.

---

## Storage Location

Lokasi yang lebih spesifik di dalam Warehouse.

Contoh:

- Rak A1
- Rak A2
- Freezer
- Gudang Belakang
- Etalase

---

# 4. Inventory First Principle

Seluruh perubahan stok harus melalui Inventory Domain.

Tidak diperbolehkan ada modul lain yang:

- menambah stok,
- mengurangi stok,
- mengubah saldo stok,
- mengubah nilai persediaan,

secara langsung.

Seluruh perubahan dilakukan melalui Inventory Transaction yang diproses oleh Inventory Domain.

---# 5. Inventory Transaction Model

Inventory Domain tidak mengubah saldo stok secara langsung.

Seluruh perubahan stok direpresentasikan sebagai Inventory Transaction yang menghasilkan Stock Movement.

Inventory Domain bertanggung jawab memperbarui Current Stock berdasarkan Stock Movement yang telah divalidasi.

---

## Inventory Transaction Flow

Business Event

↓

Inventory Transaction

↓

Stock Movement

↓

Inventory Engine

↓

Current Stock

↓

Inventory Reports

---

## Principles

### ITM-001

Stock Movement merupakan sumber kebenaran (Source of Truth) seluruh mutasi persediaan.

---

### ITM-002

Current Stock merupakan hasil akumulasi Stock Movement yang dipelihara oleh Inventory Engine untuk kebutuhan performa.

---

### ITM-003

Seluruh mutasi stok wajib memiliki referensi Business Transaction.

---

### ITM-004

Stock Movement bersifat immutable setelah diposting.

---

### ITM-005

Perubahan histori hanya dapat dilakukan melalui transaksi koreksi atau pembalik (Reversal).

---

# 6. Stock Movement Types

BusinessOS mendukung jenis mutasi berikut.

## Purchase Receipt

Barang diterima dari pemasok.

Effect:

Stock +

---

## Sales Issue

Barang dijual.

Effect:

Stock -

---

## Sales Return

Barang dikembalikan pelanggan.

Effect:

Stock +

---

## Purchase Return

Barang dikembalikan kepada pemasok.

Effect:

Stock -

---

## Stock Adjustment Increase

Penyesuaian stok bertambah.

Effect:

Stock +

---

## Stock Adjustment Decrease

Penyesuaian stok berkurang.

Effect:

Stock -

---

## Stock Transfer Out

Barang keluar dari Warehouse asal.

Effect:

Stock -

---

## Stock Transfer In

Barang masuk ke Warehouse tujuan.

Effect:

Stock +

---

## Stock Opname

Penyesuaian hasil perhitungan fisik.

Effect:

Stock + / -

---

## Initial Stock

Saldo awal persediaan.

Effect:

Stock +

---

# 7. Inventory Engine

Inventory Engine merupakan Domain Service.

Inventory Engine bukan Entity.

Inventory Engine bukan Repository.

Inventory Engine bertanggung jawab terhadap:

- validasi mutasi,
- pembaruan Current Stock,
- pencatatan histori mutasi,
- sinkronisasi stok,
- integrasi dengan Finance Domain.

---

## Responsibilities

- Memvalidasi mutasi.
- Menghasilkan Stock Movement.
- Memperbarui Current Stock.
- Memicu Accounting Engine apabila diperlukan.
- Menghasilkan Audit Log.

---# 8. Warehouse and Storage Management

Inventory Domain mendukung pengelolaan persediaan pada berbagai lokasi penyimpanan.

Lokasi penyimpanan dibagi menjadi dua tingkat:

- Warehouse
- Storage Location

Warehouse merepresentasikan area penyimpanan utama.

Storage Location merepresentasikan posisi fisik yang lebih spesifik di dalam Warehouse.

---

## Warehouse

### Purpose

Warehouse merupakan lokasi logis penyimpanan persediaan.

Satu Branch dapat memiliki satu atau lebih Warehouse.

Contoh:

- Gudang Utama
- Gudang Produksi
- Gudang Retur
- Gudang Transit
- Toko Depan

---

### Business Rules

- Warehouse harus dimiliki oleh satu Organization.
- Warehouse dapat dikaitkan dengan satu Branch.
- Warehouse dapat diaktifkan atau dinonaktifkan.
- Warehouse yang masih memiliki stok tidak dapat dihapus.

---

## Storage Location

Storage Location merupakan lokasi penyimpanan di dalam Warehouse.

Contoh:

- Rak A1
- Rak A2
- Rak B1
- Freezer
- Etalase
- Area Karantina

---

### Business Rules

- Storage Location harus berada di dalam satu Warehouse.
- Satu Warehouse dapat memiliki banyak Storage Location.
- Perpindahan antar Storage Location dicatat sebagai Stock Movement.

---

# 9. Stock Valuation

Stock Valuation menentukan nilai finansial dari persediaan.

Nilai persediaan dikelola oleh Inventory Domain dan digunakan oleh Finance Domain untuk penyusunan laporan keuangan.

---

## Supported Valuation Methods

Versi pertama BusinessOS mendukung:

### Moving Average Cost (Default)

Nilai persediaan dihitung menggunakan rata-rata bergerak.

Metode ini dipilih sebagai standar karena:

- lebih sederhana,
- sesuai untuk sebagian besar UMKM,
- efisien untuk transaksi harian,
- didukung dengan baik oleh arsitektur Offline-First.

---

## Future Roadmap

Arsitektur harus memungkinkan penambahan metode berikut tanpa mengubah model data utama:

- FIFO (First In, First Out)
- FEFO (First Expired, First Out)
- Standard Cost
- Specific Identification

---

# 10. Inventory Validation Rules

Seluruh transaksi persediaan wajib melalui proses validasi.

## IVR-001

Inventory Item harus aktif.

---

## IVR-002

Warehouse harus aktif.

---

## IVR-003

Storage Location harus valid.

---

## IVR-004

Quantity harus lebih besar dari nol, kecuali untuk transaksi koreksi yang sah.

---

## IVR-005

Stock tidak boleh menjadi negatif, kecuali apabila Organization mengaktifkan kebijakan Negative Stock.

---

## IVR-006

Seluruh mutasi wajib memiliki Business Event sebagai asal transaksi.

---

## IVR-007

Seluruh mutasi harus dapat ditelusuri hingga pengguna yang membuatnya.

---

## IVR-008

Apabila mutasi memengaruhi nilai persediaan, Inventory Engine wajib memberi tahu Accounting Engine untuk menghasilkan pencatatan akuntansi yang sesuai.

---

# 11. Inventory Reporting

Inventory Domain menyediakan data untuk laporan operasional persediaan.

Laporan dihasilkan berdasarkan Current Stock dan Stock Movement.

---

## Standard Reports

- Stock On Hand
- Stock Card (Kartu Stok)
- Stock Movement History
- Low Stock Report
- Dead Stock Report
- Inventory Valuation Report
- Warehouse Stock Report
- Storage Location Report

---

## Reporting Principles

### IR-001

Seluruh laporan harus dapat difilter berdasarkan:

- Organization
- Branch
- Warehouse
- Storage Location
- Product
- Category
- Period

---

### IR-002

Laporan harus mendukung ekspor ke format yang didukung sistem (misalnya Excel dan PDF).

---

### IR-003

Data laporan harus konsisten dengan Current Stock dan histori Stock Movement yang telah diposting.

---# 12. Integration Rules

Inventory Domain tidak bekerja secara terpisah.

Seluruh perubahan persediaan berasal dari Business Event yang terjadi pada domain lain atau dari proses inventarisasi yang sah.

---

## Inventory Integration Flow

```text
Business Event
        │
        ▼
Inventory Domain
        │
        ▼
Inventory Engine
        │
        ├──────────────┐
        ▼              ▼
Current Stock   Stock Movement
        │              │
        └──────┬───────┘
               ▼
      Accounting Engine
               │
               ▼
      Financial Reporting
```

---

## Domain Integration

### Purchasing Domain

Business Event:

- Purchase Receipt
- Purchase Return

Inventory Effect:

- Menambah stok.
- Mengurangi stok karena retur ke pemasok.

---

### POS / Sales Domain

Business Event:

- Sales Completed
- Sales Return

Inventory Effect:

- Mengurangi stok saat penjualan.
- Menambah stok saat retur pelanggan.

---

### Finance Domain

Inventory Domain tidak mengubah jurnal akuntansi secara langsung.

Inventory Engine hanya mengirim Business Event yang diperlukan kepada Accounting Engine.

Accounting Engine bertanggung jawab menghasilkan Journal, Journal Entry, dan General Ledger.

---

### Master Data

Inventory Domain menggunakan Master Data berikut:

- Product Catalog
- Product Category
- Unit of Measure
- Brand
- Supplier
- Organization
- Branch
- Warehouse
- Storage Location

Master Data tidak dikelola oleh Inventory Domain, tetapi menjadi dependensi yang wajib tersedia.

---

# 13. Performance Guidelines

Inventory Domain harus tetap responsif pada perangkat Android kelas menengah maupun perangkat dengan spesifikasi terbatas.

---

## PG-001

Current Stock digunakan untuk pembacaan cepat.

---

## PG-002

Stock Movement digunakan sebagai histori dan sumber audit.

---

## PG-003

Perubahan stok dilakukan secara transaksional agar Current Stock dan Stock Movement selalu konsisten.

---

## PG-004

Sinkronisasi dilakukan secara bertahap (incremental synchronization).

---

## PG-005

Query harus memanfaatkan indeks yang sesuai untuk Product, Warehouse, Branch, Organization, dan Status.

---

# 14. References

Dokumen ini mengacu pada:

- AICTX-001 Project Context
- AICTX-002 Product Requirements
- AICTX-003 System Architecture
- AICTX-004 Tech Stack
- AICTX-005 Database Design
- AICTX-006 Authentication and RBAC
- AICTX-007 Finance Module

Seluruh perubahan pada Inventory Domain harus tetap konsisten dengan dokumen-dokumen tersebut.

---

# 15. Revision History

| Version | Date | Description |
|----------|------|-------------|
| 1.0.0 | Initial Draft | Initial Inventory Domain Specification |
| 1.1.0 | Current | Enterprise Inventory Architecture Revision |

---

# 16. Approval

Status dokumen mengikuti tahapan berikut:

Draft

↓

Review

↓

Architecture Review

↓

Locked

Inventory Domain dinyatakan **Locked** setelah seluruh aturan bisnis, aturan mutasi stok, metode valuasi, dan integrasi dengan Finance Domain disetujui.

---

# End of Document