---
document:
  id: AICTX-012
  title: CRM Domain
  version: 1.0.0
  status: Locked
  owner: BusinessOS
  classification: AI Context Document
  priority: High

purpose:
  Mendefinisikan aturan bisnis, ruang lingkup, dan transaksi pada CRM Domain sebagai pusat pengelolaan hubungan bisnis (Business Relationship). CRM Domain bertanggung jawab mengelola Customer, Supplier, Contact, dan informasi hubungan bisnis lainnya sebagai Data Owner sesuai AICTX-011.

dependencies:
  - 001_PROJECT_CONTEXT.md
  - 003_SYSTEM_ARCHITECTURE.md
  - 005_DATABASE_DESIGN.md
  - 006_AUTHENTICATION_AND_RBAC.md
  - 011_MASTER_DATA_OWNERSHIP.md

next_documents:
  - 013_TREASURY_DOMAIN.md
---

# 1. Executive Summary

CRM Domain merupakan pusat pengelolaan hubungan bisnis (Business Relationship) dalam BusinessOS.

CRM Domain bertindak sebagai Data Owner untuk Master Data Customer, Supplier, Contact, dan Master Data hubungan bisnis lainnya.

Domain lain menggunakan Master Data tersebut sebagai referensi sesuai AICTX-011.

---

# 2. Scope

CRM Domain mencakup:

- Customer Management
- Supplier Management
- Contact Management
- Customer Group
- Supplier Group
- Business Relationship History (Roadmap)

CRM Domain tidak mengelola transaksi penjualan maupun pembelian.

---

# 3. Design Principles

## CRM-001

CRM Domain merupakan Data Owner untuk seluruh Master Data hubungan bisnis.

---

## CRM-002

Seluruh transaksi penjualan menggunakan Customer sebagai referensi.

---

## CRM-003

Seluruh transaksi pembelian menggunakan Supplier sebagai referensi.

---

## CRM-004

Perubahan Master Data Customer maupun Supplier hanya dilakukan melalui CRM Domain.

---

## CRM-005

CRM Domain tidak mengelola stok, kas, maupun jurnal akuntansi.

---

## CRM-006

Seluruh perubahan Master Data wajib tercatat pada Audit Trail sesuai AICTX-005 dan AICTX-006.

---

# 4. Master Data

CRM Domain menjadi Data Owner untuk:

- Customer
- Supplier
- Contact
- Customer Group
- Supplier Group

Spesifikasi kepemilikan mengacu pada AICTX-011 Master Data Ownership.
# 5. Master Data

CRM Domain bertanggung jawab terhadap seluruh Master Data hubungan bisnis.

---

## 5.1 Customer

Customer merupakan individu atau organisasi yang melakukan pembelian produk maupun jasa dari Organization.

### Core Fields

- customerCode
- customerName
- customerType
- customerGroupId
- phoneNumber
- email
- address
- taxNumber (opsional)
- status

### Business Rules

- Customer Code harus unik dalam Organization.
- Customer dapat digunakan pada seluruh transaksi penjualan.
- Customer dapat dinonaktifkan tanpa menghapus riwayat transaksi.
- Customer tidak boleh dihapus apabila masih memiliki transaksi aktif.

---

## 5.2 Supplier

Supplier merupakan individu atau organisasi yang menyediakan barang atau jasa kepada Organization.

### Core Fields

- supplierCode
- supplierName
- supplierGroupId
- contactPerson
- phoneNumber
- email
- address
- taxNumber (opsional)
- paymentTermId
- status

### Business Rules

- Supplier Code harus unik dalam Organization.
- Supplier dapat digunakan pada seluruh transaksi pembelian.
- Supplier dapat dinonaktifkan tanpa menghapus riwayat transaksi.
- Supplier tidak boleh dihapus apabila masih memiliki transaksi aktif.

---

## 5.3 Contact

Contact merupakan individu yang menjadi penghubung antara Organization dengan Customer atau Supplier.

### Core Fields

- fullName
- companyId
- companyType
- position
- phoneNumber
- email
- notes
- status

### Business Rules

- Satu Customer dapat memiliki lebih dari satu Contact.
- Satu Supplier dapat memiliki lebih dari satu Contact.
- Contact dapat dinonaktifkan tanpa menghapus riwayat komunikasi.

---

## 5.4 Customer Group

Customer Group digunakan untuk mengelompokkan Customer berdasarkan karakteristik bisnis.

### Contoh

- Retail
- Grosir
- Member
- VIP
- Corporate

### Business Rules

- Customer hanya dapat memiliki satu Customer Group aktif pada satu waktu.
- Group digunakan sebagai referensi harga, promosi, dan laporan.

---

## 5.5 Supplier Group

Supplier Group digunakan untuk mengelompokkan Supplier berdasarkan karakteristik bisnis.

### Contoh

- Distributor
- Pabrik
- Importir
- Jasa
- Lokal

### Business Rules

- Supplier hanya dapat memiliki satu Supplier Group aktif pada satu waktu.
- Group digunakan untuk analisis pembelian dan evaluasi pemasok.
# 6. Transaksi CRM

CRM Domain mengelola transaksi yang berkaitan dengan pengelolaan hubungan bisnis.

CRM Domain tidak mengelola transaksi penjualan maupun pembelian.

---

## 6.1 Registrasi Customer

Registrasi Customer digunakan untuk menambahkan Customer baru ke dalam BusinessOS.

### Core Fields

- registrationNumber
- registrationDate
- customerId
- registeredBy
- status

### Business Rules

- Customer Code dibuat sesuai kebijakan Organization.
- Customer dapat dibuat secara manual atau dari hasil konversi Lead (Roadmap).
- Registrasi Customer menghasilkan Customer yang dapat digunakan oleh POS dan Finance.

---

## 6.2 Registrasi Supplier

Registrasi Supplier digunakan untuk menambahkan Supplier baru.

### Core Fields

- registrationNumber
- registrationDate
- supplierId
- registeredBy
- status

### Business Rules

- Supplier Code dibuat sesuai kebijakan Organization.
- Supplier dapat langsung digunakan pada transaksi Procurement setelah status aktif.

---

## 6.3 Perubahan Data Master

Perubahan terhadap Customer maupun Supplier dilakukan melalui transaksi perubahan data.

### Business Rules

- Seluruh perubahan wajib tercatat pada Audit Trail.
- Riwayat perubahan harus dapat ditelusuri.
- Perubahan dapat memerlukan Approval sesuai kebijakan Organization.

---

## 6.4 Perubahan Status

CRM Domain mendukung perubahan status Customer maupun Supplier.

Contoh:

- Active
- Inactive
- Suspended

### Business Rules

- Status Inactive tidak menghapus riwayat transaksi.
- Status Suspended mencegah penggunaan pada transaksi baru.
- Status hanya dapat diubah oleh pengguna yang memiliki Permission.

---

# 7. Workflow

Seluruh transaksi CRM mengikuti Unified State Machine BusinessOS.

Lifecycle standar:

Draft

↓

Submitted

↓

Approved

↓

Completed

---

## Workflow Rules

### WF-001

Perubahan hanya dapat dilakukan melalui transisi status yang diizinkan.

---

### WF-002

Setiap perubahan status menghasilkan Audit Trail.

---

### WF-003

Apabila Approval diaktifkan oleh Organization, transaksi tidak dapat diselesaikan sebelum seluruh Approval terpenuhi.

---

# 8. Integrasi Domain

CRM Domain menyediakan Master Data hubungan bisnis untuk domain lain.

---

## POS Domain

Menggunakan:

- Customer
- Customer Group

---

## Procurement Domain

Menggunakan:

- Supplier
- Supplier Group

---

## Finance Domain

Menggunakan:

- Customer
- Supplier

Sebagai referensi transaksi keuangan.

---

## Reporting

Menggunakan seluruh Master Data CRM untuk analisis pelanggan, pemasok, dan hubungan bisnis.
# 9. Performance Guidelines

CRM Domain harus mampu menangani Master Data dalam jumlah besar tanpa mengurangi responsivitas aplikasi.

---

## PG-001

Daftar Customer dan Supplier harus mendukung pagination atau lazy loading.

---

## PG-002

Pencarian harus mendukung pencarian berdasarkan:

- Kode
- Nama
- Nomor Telepon
- Email

---

## PG-003

Sinkronisasi Master Data dilakukan secara incremental sesuai AICTX-003 System Architecture.

---

## PG-004

Perubahan Master Data harus segera tersedia bagi domain lain setelah sinkronisasi berhasil.

---

# 10. Security Guidelines

CRM Domain mengikuti AICTX-006 Authentication & RBAC.

---

## SEC-001

Pengguna hanya dapat mengakses Customer dan Supplier sesuai Organization.

---

## SEC-002

Hak membuat, mengubah, menghapus, dan mengubah status ditentukan oleh Role dan Permission.

---

## SEC-003

Seluruh perubahan Master Data harus tercatat pada Audit Trail.

---

# 11. Reports

CRM Domain menyediakan data untuk Reporting Engine.

Contoh laporan:

- Daftar Customer
- Daftar Supplier
- Customer per Group
- Supplier per Group
- Customer Aktif
- Supplier Aktif
- Customer Tidak Aktif
- Supplier Tidak Aktif

CRM Domain tidak menghasilkan laporan keuangan.

---

# 12. References

Dokumen ini mengacu pada:

- AICTX-003 System Architecture
- AICTX-005 Database Design
- AICTX-006 Authentication & RBAC
- AICTX-011 Master Data Ownership

---

# 13. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial CRM Domain Specification |

---

# 14. Approval

Status dokumen mengikuti tahapan:

Draft

↓

Review

↓

Architecture Review

↓

Locked

CRM Domain dinyatakan Locked setelah seluruh Master Data dan workflow disetujui.

---

# End of Document