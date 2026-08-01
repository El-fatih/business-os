---
document:
  id: AICTX-011
  title: Master Data Ownership
  version: 1.0.0
  status: Locked
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan kepemilikan (Data Owner) seluruh Master Data BusinessOS sebagai Single Source of Truth. Dokumen ini menjadi acuan bagi seluruh domain dalam membuat, mengubah, menghapus, dan menggunakan Master Data sehingga tidak terjadi duplikasi data maupun konflik kepemilikan.

dependencies:
  - 001_PROJECT_CONTEXT.md
  - 002_PRODUCT_REQUIREMENTS.md
  - 003_SYSTEM_ARCHITECTURE.md
  - 005_DATABASE_DESIGN.md

next_documents:
  - 012_CRM_DOMAIN.md
---

# 1. Executive Summary

Master Data merupakan data referensi yang digunakan oleh satu atau lebih domain bisnis.

Setiap Master Data hanya memiliki satu Data Owner.

Domain lain menggunakan Master Data tersebut sebagai referensi dan tidak menjadi pemilik data.

Dokumen ini menjadi Single Source of Truth untuk menentukan kepemilikan Master Data di seluruh BusinessOS.

---

# 2. Data Ownership Principles

## DOP-001

Setiap Master Data hanya memiliki satu Data Owner.

---

## DOP-002

Data Owner bertanggung jawab membuat, mengubah, menghapus, dan menjaga kualitas data.

---

## DOP-003

Domain lain hanya menggunakan Master Data sebagai referensi.

---

## DOP-004

Perubahan struktur Master Data harus dilakukan oleh Data Owner.

---

## DOP-005

Seluruh domain wajib menggunakan Master Data yang sama.

Tidak diperbolehkan membuat duplikasi Master Data pada domain lain.

---

## DOP-006

Seluruh Master Data mendukung Offline-First dan sinkronisasi sesuai System Architecture.

---

## DOP-007

Seluruh Master Data mengikuti BaseEntity sebagaimana didefinisikan pada AICTX-005 Database Design.

---

# 3. Ownership Model

Setiap Master Data memiliki tiga komponen utama:

- Data Owner
- Consumer Domain
- Business Responsibility

---

## Data Owner

Domain yang memiliki tanggung jawab penuh terhadap Master Data.

---

## Consumer Domain

Domain yang menggunakan Master Data sebagai referensi.

---

## Business Responsibility

Menjelaskan tanggung jawab bisnis dari Master Data tersebut.
# 4. Foundation Master Data

Foundation merupakan Master Data yang digunakan oleh seluruh BusinessOS.

| Master Data | Data Owner | Used By |
|-------------|------------|---------|
| Organization | Foundation | All Domains |
| Branch | Foundation | All Domains |
| User | Foundation | All Domains |
| Role | Foundation | All Domains |
| Permission | Foundation | All Domains |
| Role Assignment | Foundation | All Domains |

---

## Organization

### Business Responsibility

Merepresentasikan entitas bisnis yang menggunakan BusinessOS.

### Data Owner

Foundation Domain

### Used By

Seluruh Domain

---

## Branch

### Business Responsibility

Merepresentasikan cabang atau lokasi operasional.

### Data Owner

Foundation Domain

### Used By

Seluruh Domain

---

## User

### Business Responsibility

Merepresentasikan identitas pengguna sistem.

### Data Owner

Foundation Domain

### Used By

Seluruh Domain

---

## Role

### Business Responsibility

Mendefinisikan kelompok hak akses.

### Data Owner

Foundation Domain

### Used By

Seluruh Domain

---

## Permission

### Business Responsibility

Mendefinisikan hak akses granular terhadap fitur sistem.

### Data Owner

Foundation Domain

### Used By

Authentication & RBAC

Seluruh Domain

---

## Role Assignment

### Business Responsibility

Menghubungkan User dengan Role pada ruang lingkup tertentu.

### Data Owner

Foundation Domain

### Used By

Authentication & RBAC

Seluruh Domain

---

# 5. CRM Master Data

CRM Domain bertanggung jawab terhadap seluruh data hubungan bisnis (Business Relationship).

| Master Data | Data Owner | Used By |
|-------------|------------|---------|
| Customer | CRM Domain | POS, Finance, Reporting |
| Supplier | CRM Domain | Procurement, Finance |
| Contact | CRM Domain | CRM |
| Customer Group | CRM Domain | POS, Marketing |
| Supplier Group (Roadmap) | CRM Domain | Procurement |

---

## Customer

### Business Responsibility

Merepresentasikan pelanggan yang melakukan transaksi dengan Organization.

---

## Supplier

### Business Responsibility

Merepresentasikan pemasok barang atau jasa.

Supplier hanya memiliki satu Data Owner yaitu CRM Domain.

Procurement menggunakan Supplier sebagai referensi transaksi pembelian.

---

## Contact

### Business Responsibility

Menyimpan informasi kontak yang berhubungan dengan Customer maupun Supplier.

---

## Customer Group

### Business Responsibility

Mengelompokkan Customer berdasarkan kategori bisnis.

Contoh:

- Retail
- Grosir
- Member
- VIP
- Corporate
# 6. Inventory Master Data

Inventory Domain bertanggung jawab terhadap seluruh Master Data yang berhubungan dengan produk, persediaan, dan penyimpanan.

| Master Data | Data Owner | Used By |
|-------------|------------|---------|
| Product | Inventory Domain | POS, Procurement, Reporting |
| Product Category | Inventory Domain | POS, Procurement |
| Product Variant | Inventory Domain | POS |
| Unit of Measure | Inventory Domain | Inventory, POS, Procurement |
| Warehouse | Inventory Domain | Procurement, Inventory |
| Storage Location | Inventory Domain | Inventory |
| Stock Configuration | Inventory Domain | Inventory |

---

## Product

### Business Responsibility

Merepresentasikan barang atau jasa yang diperdagangkan oleh Organization.

Seluruh perubahan informasi produk dilakukan melalui Inventory Domain.

---

## Product Category

### Business Responsibility

Mengelompokkan Product berdasarkan kategori bisnis.

---

## Product Variant

### Business Responsibility

Merepresentasikan variasi Product.

Contoh:

- Warna
- Ukuran
- Model

---

## Unit of Measure

### Business Responsibility

Menentukan satuan dasar yang digunakan pada transaksi.

Contoh:

- Pcs
- Box
- Kg
- Liter
- Meter

---

## Warehouse

### Business Responsibility

Merepresentasikan lokasi penyimpanan stok.

---

## Storage Location

### Business Responsibility

Merepresentasikan lokasi penyimpanan yang lebih spesifik di dalam Warehouse.

Contoh:

- Rak
- Bin
- Zona

---

## Stock Configuration

### Business Responsibility

Menyimpan konfigurasi operasional persediaan.

Contoh:

- Minimum Stock
- Maximum Stock
- Reorder Point
- Safety Stock

---

# 7. Finance Master Data

Finance Domain bertanggung jawab terhadap Master Data yang berkaitan dengan pencatatan keuangan.

| Master Data | Data Owner | Used By |
|-------------|------------|---------|
| Chart of Accounts | Finance Domain | Finance, Reporting |
| Fiscal Period | Finance Domain | Finance |
| Cost Center | Finance Domain | Finance, Procurement |
| Currency | Finance Domain | Finance |
| Tax Configuration | Finance Domain | Finance, POS, Procurement |

---

## Chart of Accounts

### Business Responsibility

Daftar akun yang digunakan dalam pencatatan akuntansi.

---

## Fiscal Period

### Business Responsibility

Menentukan periode pembukuan yang aktif.

---

## Cost Center

### Business Responsibility

Mengelompokkan biaya berdasarkan unit organisasi atau aktivitas tertentu.

---

## Currency

### Business Responsibility

Mendefinisikan mata uang yang digunakan Organization.

---

## Tax Configuration

### Business Responsibility

Menyimpan konfigurasi perpajakan yang digunakan oleh transaksi bisnis.
# 8. Domain-Specific Master Data

Beberapa Master Data hanya digunakan oleh domain tertentu dan tidak menjadi referensi lintas domain.

| Master Data | Data Owner | Used By |
|-------------|------------|---------|
| Payment Term | Procurement Domain | Procurement |
| Sales Session Configuration | POS Domain | POS |
| POS Terminal | POS Domain | POS |
| Asset Category | Asset Domain | Asset |
| Employee Position | HR Domain | HR |
| Leave Type | HR Domain | HR |
| Payroll Component | HR Domain | HR |

---

## Payment Term

### Business Responsibility

Menentukan syarat dan jangka waktu pembayaran kepada Supplier.

---

## Sales Session Configuration

### Business Responsibility

Menyimpan konfigurasi operasional POS seperti nomor transaksi, pengaturan shift, dan perilaku Checkout.

---

## POS Terminal

### Business Responsibility

Merepresentasikan perangkat atau titik transaksi yang digunakan untuk melakukan penjualan.

---

## Asset Category

### Business Responsibility

Mengelompokkan aset berdasarkan jenis dan karakteristiknya.

---

## Employee Position

### Business Responsibility

Mendefinisikan jabatan atau posisi kerja dalam Organization.

---

## Leave Type

### Business Responsibility

Mendefinisikan jenis cuti yang tersedia bagi karyawan.

---

## Payroll Component

### Business Responsibility

Mendefinisikan komponen penghasilan maupun potongan dalam proses penggajian.

---

# 9. Ownership Rules

## OWN-001

Setiap Master Data hanya memiliki satu Data Owner.

---

## OWN-002

Perubahan struktur Master Data hanya dapat dilakukan oleh Data Owner.

---

## OWN-003

Domain lain tidak diperbolehkan membuat salinan Master Data sebagai sumber kebenaran baru.

---

## OWN-004

Apabila suatu Master Data digunakan oleh lebih dari satu domain, seluruh domain wajib menggunakan Master Data yang sama.

---

## OWN-005

Perubahan pada Master Data harus tetap menjaga kompatibilitas dengan Consumer Domain.

---

# 10. References

Dokumen ini menjadi acuan bagi seluruh domain BusinessOS dalam menentukan kepemilikan Master Data.

Domain lain wajib mengacu pada dokumen ini ketika mendefinisikan Master Data baru.

---

# 11. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial Master Data Ownership Specification |

---

# 12. Approval

Status dokumen mengikuti tahapan berikut:

Draft

↓

Review

↓

Architecture Review

↓

Locked

Dokumen ini dinyatakan **Locked** setelah seluruh Data Owner dan Consumer Domain disetujui.

---

# End of Document