---
document:
  id: AICTX-010
  title: Procurement Domain
  version: 1.0.0
  status: Draft
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan aturan bisnis, konsep domain, workflow pengadaan, dan integrasi Procurement Domain dengan Inventory Domain, Finance Domain, serta Supplier Master Data. Procurement Domain bertanggung jawab mengelola seluruh proses pengadaan barang dan jasa mulai dari permintaan hingga penyelesaian pembayaran.

dependencies:
  - 01_PROJECT_CONTEXT.md
  - 02_PRODUCT_REQUIREMENTS.md
  - 03_SYSTEM_ARCHITECTURE.md
  - 04_TECH_STACK.md
  - 05_DATABASE_DESIGN.md
  - 06_AUTHENTICATION_AND_RBAC.md
  - 07_FINANCE_MODULE.md
  - 08_INVENTORY_DOMAIN.md
  - 09_POS_DOMAIN.md

next_documents:
  - 011_CRM_DOMAIN.md
---

# 1. Executive Summary

Procurement Domain mengelola seluruh siklus pengadaan barang dan jasa.

Procurement Domain menggunakan Master Data yang dikelola oleh domain lain, seperti Supplier, Product, Branch, dan Organization. Kepemilikan (ownership) Master Data tetap berada pada domain pemiliknya sesuai prinsip **Single Source of Truth**.

Procurement Domain tidak mengubah stok maupun jurnal akuntansi secara langsung.

Setiap proses pengadaan direpresentasikan sebagai Business Document yang mengikuti Business State Machine.

Procurement Domain menghasilkan Business Event yang diproses oleh Inventory Domain dan Accounting Engine.

---

# 2. Procurement Principles

## PRC-001

Seluruh proses pengadaan harus berbasis Business Document.

---

## PRC-002

Setiap dokumen mengikuti Unified State Machine.

---

## PRC-003

Procurement Domain tidak mengubah Current Stock secara langsung.

---

## PRC-004

Procurement Domain tidak membuat Journal secara langsung.

---

## PRC-005

Seluruh aktivitas harus dapat diaudit.

---

## PRC-006

Procurement Domain harus mendukung Offline-First.

---

## PRC-007

Seluruh perubahan harus menghasilkan Business Event.

---

# 3. Core Concepts

Procurement Domain terdiri atas beberapa Business Document.

## Purchase Request

Permintaan kebutuhan barang atau jasa yang dibuat oleh pengguna.

Belum menjadi komitmen pembelian.

---

## Purchase Order

Dokumen resmi pemesanan kepada Supplier.

Purchase Order merupakan komitmen pembelian.

---

## Goods Receipt

Dokumen penerimaan barang.

Goods Receipt menjadi dasar bagi Inventory Domain untuk melakukan penambahan stok.

---

## Supplier Invoice

Dokumen tagihan dari Supplier.

Supplier Invoice menjadi dasar pencatatan kewajiban kepada pemasok.

---

## Purchase Payment

Dokumen pembayaran kepada Supplier.

Pembayaran dilakukan melalui Treasury Domain sesuai aturan Finance Domain.

---

# 4. Procurement Workflow

Alur standar pengadaan adalah sebagai berikut.

Purchase Request

↓

Approval

↓

Purchase Order

↓

Supplier

↓

Goods Receipt

↓

Supplier Invoice

↓

Purchase Payment

↓

Completed

---

## Workflow Principles

### PW-001

Purchase Request dapat ditolak atau direvisi sebelum disetujui.

---

### PW-002

Purchase Order hanya dapat dibuat dari Purchase Request yang telah disetujui, kecuali Organization mengaktifkan kebijakan pembelian langsung.

---

### PW-003

Goods Receipt hanya dapat dibuat berdasarkan Purchase Order yang masih aktif.

---

### PW-004

Supplier Invoice harus mengacu pada Purchase Order dan/atau Goods Receipt sesuai kebijakan Organization.

---

### PW-005

Purchase Payment hanya dapat dilakukan setelah proses validasi yang diwajibkan oleh Organization terpenuhi.

---# 5. Master Data

Procurement Domain menggunakan Master Data yang dikelola oleh domain lain.

Procurement tidak menjadi pemilik (owner) Master Data tersebut, tetapi menggunakannya sebagai referensi dalam proses pengadaan.

---

## Supplier

Supplier merupakan pihak yang menyediakan barang atau jasa kepada Organization.

> **Data Ownership**
>
> Master Data Supplier dikelola oleh **CRM Domain** sebagai **Single Source of Truth**.
>
> Procurement Domain menggunakan Master Data Supplier sebagai referensi dalam seluruh proses transaksi pembelian dan tidak menjadi pemilik data Supplier.

### Core Fields

- supplierCode
- supplierName
- contactPerson
- phoneNumber
- email
- address
- taxNumber (opsional)
- paymentTermId
- status

### Business Rules

- Supplier harus aktif sebelum dapat digunakan dalam transaksi.
- Supplier dapat memasok satu atau lebih produk.
- Supplier dapat digunakan oleh satu atau lebih Branch sesuai kebijakan Organization.

---

## Payment Term

Payment Term mendefinisikan jangka waktu pembayaran kepada Supplier.

### Contoh

- Tunai
- COD
- 7 Hari
- 14 Hari
- 30 Hari
- 60 Hari

---

## Supplier Price List (Opsional)

Organization dapat menyimpan daftar harga dari Supplier.

Daftar harga ini digunakan sebagai referensi saat membuat Purchase Order.

Penggunaan Supplier Price List bersifat opsional dan dapat diaktifkan sesuai kebutuhan bisnis.

---

# 6. Transaksi Procurement

Procurement Domain terdiri atas beberapa jenis transaksi yang saling berkaitan.

---

## 6.1 Permintaan Pembelian (Purchase Request)

Permintaan Pembelian merupakan usulan kebutuhan barang atau jasa.

Permintaan ini belum menjadi komitmen pembelian.

### Core Fields

- requestNumber
- requestDate
- requesterId
- branchId
- status
- notes

### Business Rules

- Dapat berisi satu atau lebih item.
- Dapat memerlukan Approval sesuai kebijakan Organization.
- Dapat dibatalkan sebelum disetujui.

---

## 6.2 Pesanan Pembelian (Purchase Order)

Pesanan Pembelian merupakan komitmen resmi kepada Supplier.

### Core Fields

- purchaseOrderNumber
- supplierId
- orderDate
- expectedDeliveryDate
- paymentTermId
- status

### Business Rules

- Dibuat berdasarkan Permintaan Pembelian atau secara langsung jika diizinkan.
- Satu Purchase Order dapat dipenuhi melalui beberapa kali penerimaan barang (Partial Delivery).
- Tidak dapat diubah setelah diposting, kecuali melalui proses koreksi yang sah.

---

## 6.3 Penerimaan Barang (Goods Receipt)

Penerimaan Barang mencatat barang yang benar-benar diterima dari Supplier.

Goods Receipt menjadi dasar penambahan stok oleh Inventory Domain.

### Core Fields

- receiptNumber
- purchaseOrderId
- receiptDate
- warehouseId
- receivedBy
- status

### Business Rules

- Hanya dapat dibuat dari Purchase Order yang aktif.
- Mendukung penerimaan sebagian (Partial Receipt).
- Barang yang ditolak dicatat sebagai bagian dari proses retur atau penolakan sesuai kebijakan Organization.

---

## 6.4 Tagihan Supplier (Supplier Invoice)

Tagihan Supplier merupakan dokumen yang diterima dari Supplier sebagai dasar kewajiban pembayaran.

### Core Fields

- invoiceNumber
- supplierInvoiceNumber
- invoiceDate
- dueDate
- supplierId
- status

### Business Rules

- Harus mengacu pada Purchase Order, Goods Receipt, atau keduanya sesuai kebijakan Organization.
- Menjadi dasar pencatatan utang usaha (Accounts Payable).
- Tidak dapat dibayar lebih dari nilai tagihan yang masih terbuka.

---

## 6.5 Pembayaran Pembelian (Purchase Payment)

Pembayaran Pembelian mencatat penyelesaian kewajiban kepada Supplier.

### Core Fields

- paymentNumber
- paymentDate
- supplierId
- paymentMethod
- amount
- status

### Business Rules

- Pembayaran diproses melalui Treasury Domain.
- Dapat dilakukan secara penuh atau bertahap (Partial Payment).
- Seluruh pembayaran harus memiliki referensi ke Supplier Invoice yang dibayar.# 7. Validasi Transaksi

Setiap transaksi Procurement harus melalui proses validasi sesuai aturan bisnis sebelum dapat diproses ke tahap berikutnya.

Validasi dilakukan secara bertahap sesuai status transaksi.

---

## Validation Principles

### VAL-001

Seluruh transaksi wajib memiliki Organization dan Branch yang valid.

---

### VAL-002

Supplier harus berstatus aktif.

---

### VAL-003

Produk yang dibeli harus masih aktif.

---

### VAL-004

Jumlah dan harga tidak boleh bernilai negatif.

---

### VAL-005

Perubahan setelah transaksi diposting hanya dapat dilakukan melalui transaksi koreksi yang sah.

---

# 8. Three-Way Matching

Three-Way Matching merupakan mekanisme validasi sebelum Supplier Invoice dapat diproses menjadi kewajiban pembayaran.

Tujuannya adalah memastikan Organization hanya membayar barang atau jasa yang benar-benar telah dipesan dan diterima.

---

## Matching Documents

Three-Way Matching membandingkan:

- Pesanan Pembelian (Purchase Order)
- Penerimaan Barang (Goods Receipt)
- Tagihan Supplier (Supplier Invoice)

---

## Validation Rules

### TWM-001

Jumlah barang pada Supplier Invoice tidak boleh melebihi jumlah yang diterima, kecuali kebijakan Organization mengizinkan toleransi.

---

### TWM-002

Supplier Invoice harus berasal dari Supplier yang sama dengan Purchase Order.

---

### TWM-003

Nilai tagihan harus sesuai dengan Purchase Order atau berada dalam batas toleransi yang ditetapkan.

---

### TWM-004

Apabila validasi gagal, Supplier Invoice tidak dapat diproses ke tahap pembayaran hingga masalah diselesaikan.

---

# 9. Workflow Transaksi

Seluruh transaksi Procurement mengikuti State Machine yang telah ditetapkan.

---

## Lifecycle

Draft

↓

Submitted

↓

Approved

↓

Posted

↓

Completed

---

## Alternate States

Draft

↓

Cancelled

---

Submitted

↓

Rejected

---

Posted

↓

Corrected

---

Completed

↓

Closed

---

## Workflow Rules

### WF-001

Perubahan status hanya boleh mengikuti transisi yang diizinkan.

---

### WF-002

Setiap perubahan status harus dicatat pada Audit Log.

---

### WF-003

Setiap transaksi memiliki pengguna (Actor) yang bertanggung jawab terhadap perubahan status.

---

### WF-004

Transaksi yang telah Completed tidak dapat diubah secara langsung.

---

# 10. Integrasi Domain

Procurement Domain berfungsi sebagai penghasil transaksi yang memicu proses pada domain lain.

---

## Inventory Domain

Goods Receipt menghasilkan Business Event yang diproses oleh Inventory Domain untuk menambah stok.

Retur kepada Supplier akan menghasilkan pengurangan stok sesuai aturan Inventory.

---

## Finance Domain

Supplier Invoice menjadi dasar pencatatan kewajiban (Accounts Payable).

Accounting Engine bertanggung jawab menghasilkan Journal dan General Ledger.

---

## Treasury Domain

Purchase Payment diproses melalui Treasury Domain sesuai metode pembayaran yang digunakan.

---

## Master Data

Procurement menggunakan Master Data yang dimiliki oleh domain lain sebagai referensi.

| Master Data | Owner Domain |
|-------------|--------------|
| Supplier | CRM Domain |
| Product | Inventory Domain |
| Product Category | Inventory Domain |
| Warehouse | Inventory Domain |
| Branch | Foundation |
| Organization | Foundation |
| Payment Term | Procurement Domain |

Procurement Domain tidak menjadi pemilik Master Data tersebut, kecuali Master Data yang secara eksplisit didefinisikan berada dalam ruang lingkup Procurement.
# 11. Performance Guidelines

Procurement Domain harus tetap responsif pada perangkat Android kelas menengah maupun perangkat dengan spesifikasi terbatas.

---

## PG-001

Seluruh transaksi Procurement harus dapat dibuat dalam mode offline sesuai kebijakan Organization.

---

## PG-002

Sinkronisasi dilakukan secara bertahap (Incremental Synchronization) untuk mengurangi penggunaan jaringan.

---

## PG-003

Apabila terjadi konflik sinkronisasi, penyelesaian konflik mengikuti kebijakan Conflict Resolution yang telah ditetapkan pada System Architecture.

---

## PG-004

Proses validasi dilakukan sedekat mungkin dengan sumber data untuk meminimalkan transaksi yang tidak valid.

---

## PG-005

Daftar Supplier, Produk, dan referensi lain harus menggunakan mekanisme pagination atau lazy loading apabila jumlah data besar.

---

# 12. Security Guidelines

Seluruh transaksi Procurement wajib mengikuti kebijakan Authentication dan RBAC.

---

## SEC-001

Pengguna hanya dapat mengakses transaksi sesuai Organization dan Branch yang dimilikinya.

---

## SEC-002

Hak membuat, menyetujui, mengubah, dan membatalkan transaksi ditentukan oleh Role dan Permission.

---

## SEC-003

Approval wajib tercatat pada Audit Trail.

---

## SEC-004

Setiap transaksi harus menyimpan informasi pengguna yang membuat dan terakhir memperbarui transaksi.

---

# 13. Reports

Procurement Domain harus menyediakan data yang dapat digunakan oleh Reporting Engine.

Contoh laporan:

- Laporan Pembelian
- Laporan Supplier
- Laporan Penerimaan Barang
- Laporan Tagihan Supplier
- Laporan Pembayaran Supplier
- Analisis Pembelian per Supplier
- Analisis Pembelian per Produk
- Analisis Ketepatan Pengiriman Supplier

Laporan merupakan hasil pengolahan data dan tidak menjadi sumber kebenaran utama (Source of Truth).

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
- AICTX-008 Inventory Domain
- AICTX-009 POS Domain

Seluruh perubahan pada Procurement Domain harus tetap konsisten dengan dokumen-dokumen tersebut.

---

# 15. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial Procurement Domain Specification |
| 1.1.0 | Current | Enterprise Procurement Architecture Revision |

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

Procurement Domain dinyatakan **Locked** setelah seluruh aturan transaksi, validasi, workflow, integrasi, keamanan, dan performa disetujui.

---

# End of Document