---
document:
  id: AICTX-009
  title: POS Domain
  version: 1.0.0
  status: loked
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan aturan bisnis, konsep domain, alur transaksi penjualan, serta integrasi Point of Sale (POS) dengan Inventory Domain, Finance Domain, dan Customer Domain. POS Domain bertanggung jawab mengorkestrasi proses penjualan dari awal hingga transaksi selesai.

dependencies:
  - 01_PROJECT_CONTEXT.md
  - 02_PRODUCT_REQUIREMENTS.md
  - 03_SYSTEM_ARCHITECTURE.md
  - 04_TECH_STACK.md
  - 05_DATABASE_DESIGN.md
  - 06_AUTHENTICATION_AND_RBAC.md
  - 07_FINANCE_MODULE.md
  - 08_INVENTORY_DOMAIN.md

next_documents:
  - 10_PURCHASING_DOMAIN.md
---

# 1. Executive Summary

POS Domain merupakan domain yang mengelola proses penjualan kepada pelanggan.

POS Domain tidak mengelola stok maupun akuntansi secara langsung.

POS Domain menghasilkan Business Event yang diproses oleh Inventory Domain dan Accounting Engine.

POS Domain menjadi titik interaksi utama antara pengguna, pelanggan, dan proses penjualan.

---

# 2. POS Principles

## PP-001

POS Domain bertanggung jawab terhadap proses penjualan, bukan pengelolaan stok.

---

## PP-002

Seluruh transaksi penjualan harus direpresentasikan sebagai Business Transaction.

---

## PP-003

POS Domain tidak boleh mengubah Current Stock secara langsung.

---

## PP-004

POS Domain tidak boleh membuat Journal atau General Ledger secara langsung.

---

## PP-005

Seluruh transaksi harus dapat ditelusuri hingga pengguna (Cashier atau Owner) yang memprosesnya.

---

## PP-006

POS Domain harus mendukung arsitektur Offline-First.

---

## PP-007

Setiap transaksi harus memiliki status yang jelas dan dapat diaudit.

---

# 3. Core Concepts

## Sales Session

Sesi kerja kasir sejak membuka hingga menutup POS.

Sales Session menjadi ruang lingkup seluruh transaksi penjualan yang dilakukan oleh kasir.

---

## Cart

Representasi sementara dari barang yang dipilih pelanggan sebelum pembayaran.

Cart bukan merupakan transaksi final.

---

## Sales Transaction

Representasi resmi transaksi penjualan.

Sales Transaction menjadi Business Transaction yang akan diproses lebih lanjut oleh Inventory Domain dan Accounting Engine.

---

## Payment

Representasi metode pembayaran yang digunakan pelanggan.

POS Domain mendukung berbagai metode pembayaran tanpa bergantung pada penyedia tertentu.

---

## Receipt

Dokumen bukti transaksi yang dihasilkan setelah transaksi berhasil diselesaikan.

Receipt dapat dicetak, dibagikan secara digital, atau dikirim melalui media yang didukung sistem.

---

# 4. Sales Workflow

Alur standar transaksi penjualan adalah sebagai berikut.

Customer

↓

Sales Session

↓

Cart

↓

Checkout

↓

Payment

↓

Sales Transaction

↓

Inventory Domain

↓

Accounting Engine

↓

Receipt

---

## Workflow Principles

### SW-001

Cart dapat diubah sebelum proses Checkout.

---

### SW-002

Setelah Checkout berhasil, Cart berubah menjadi Sales Transaction.

---

### SW-003

Sales Transaction yang telah selesai tidak dapat diedit secara langsung.

---

### SW-004

Perubahan setelah transaksi selesai dilakukan melalui proses Return, Refund, atau Correction sesuai aturan bisnis.

---# 5. Cart Management

Cart merupakan representasi sementara dari transaksi penjualan yang sedang diproses.

Cart menjadi ruang kerja (Business Workspace) sebelum Checkout dilakukan.

Cart tidak menghasilkan perubahan pada Inventory maupun Finance hingga proses Checkout berhasil.

---

## Cart Lifecycle

Create Cart

↓

Add Item

↓

Modify Item

↓

Apply Promotion

↓

Apply Discount

↓

Select Customer

↓

Calculate Tax

↓

Validate Cart

↓

Checkout

↓

Sales Transaction

---

## Cart Principles

### CART-001

Satu Cart hanya dimiliki oleh satu Sales Session.

---

### CART-002

Cart dapat diubah selama belum Checkout.

---

### CART-003

Checkout mengubah Cart menjadi Sales Transaction.

---

### CART-004

Setelah Checkout berhasil, Cart menjadi Read Only.

---

### CART-005

Cart yang dibatalkan tidak menghasilkan Business Transaction.

---

# 6. Cart Item

Setiap Cart terdiri dari satu atau lebih Cart Item.

Cart Item menyimpan informasi transaksi sementara.

---

## Core Fields

- productId
- productName
- variantId
- quantity
- unitPrice
- discount
- tax
- subtotal
- notes

---

## Validation Rules

### CI-001

Quantity harus lebih besar dari nol.

---

### CI-002

Product harus aktif.

---

### CI-003

Product harus tersedia untuk dijual.

---

### CI-004

Harga tidak boleh negatif.

---

### CI-005

Diskon tidak boleh melebihi batas yang ditentukan oleh kebijakan Organization.

---

# 7. Checkout Process

Checkout merupakan proses konversi Cart menjadi Sales Transaction.

Checkout hanya dapat dilakukan apabila seluruh validasi berhasil.

---

## Checkout Flow

Validate Cart

↓

Validate Customer

↓

Validate Payment

↓

Create Sales Transaction

↓

Inventory Domain

↓

Accounting Engine

↓

Generate Receipt

↓

Complete

---

## Checkout Rules

### CHK-001

Cart wajib memiliki minimal satu Cart Item.

---

### CHK-002

Nominal transaksi harus lebih besar dari nol.

---

### CHK-003

Metode pembayaran wajib dipilih.

---

### CHK-004

Pengguna harus memiliki Permission untuk melakukan Checkout.

---

### CHK-005

Apabila proses Checkout gagal, Cart tetap dipertahankan agar dapat diperbaiki atau dicoba kembali.

---

### CHK-006

Checkout harus bersifat atomic.

Seluruh proses harus berhasil seluruhnya atau dibatalkan seluruhnya (all-or-nothing).

---

# 8. Sales Transaction

Sales Transaction merupakan hasil akhir dari Checkout.

Sales Transaction menjadi Business Transaction resmi yang diproses oleh Inventory Domain dan Accounting Engine.

---

## Business Rules

### ST-001

Sales Transaction bersifat immutable setelah berhasil diposting.

---

### ST-002

Perubahan dilakukan melalui Return, Refund, atau Correction.

---

### ST-003

Setiap Sales Transaction wajib memiliki Sales Session.

---

### ST-004

Setiap Sales Transaction wajib memiliki Branch.

---

### ST-005

Setiap Sales Transaction wajib memiliki Organization.

---# 9. Payment Processing

Payment Processing merupakan proses penyelesaian kewajiban pembayaran pelanggan terhadap Sales Transaction.

Payment tidak mengubah stok secara langsung.

Payment menghasilkan Business Event yang diproses oleh Treasury dan Accounting Engine.

---

## Supported Payment Methods

BusinessOS mendukung berbagai metode pembayaran.

Contoh:

- Cash
- Debit Card
- Credit Card
- Bank Transfer
- QRIS
- E-Wallet
- Gift Voucher
- Store Credit

Arsitektur harus memungkinkan penambahan metode pembayaran baru tanpa mengubah domain inti.

---

## Split Payment

Satu Sales Transaction dapat diselesaikan menggunakan lebih dari satu metode pembayaran.

Contoh:

- Cash + QRIS
- Cash + Debit
- Voucher + Cash

---

## Payment Rules

### PAY-001

Total pembayaran harus sama dengan nilai transaksi.

---

### PAY-002

Apabila pembayaran melebihi nilai transaksi, sistem menghasilkan Change Amount sesuai metode pembayaran yang mendukung.

---

### PAY-003

Metode pembayaran harus aktif dan diizinkan oleh Organization.

---

### PAY-004

Seluruh pembayaran menghasilkan Business Event yang dapat diaudit.

---

### PAY-005

Pembayaran tidak boleh mengubah Current Stock secara langsung.

---

# 10. Receipt Management

Receipt merupakan bukti resmi transaksi penjualan.

Receipt dihasilkan setelah Checkout dan Payment berhasil.

---

## Receipt Content

Minimal memuat:

- Nomor Transaksi
- Tanggal & Waktu
- Branch
- Cashier
- Customer (opsional)
- Daftar Item
- Quantity
- Harga
- Diskon
- Pajak
- Total
- Metode Pembayaran
- Change Amount
- QR / Barcode transaksi (opsional)

---

## Receipt Rules

### RC-001

Receipt memiliki nomor unik.

---

### RC-002

Receipt dapat dicetak ulang tanpa mengubah transaksi.

---

### RC-003

Cetak ulang harus tercatat pada Audit Log.

---

### RC-004

Receipt dapat dibagikan secara digital sesuai media yang didukung sistem.

---

# 11. Return and Refund

Return dan Refund merupakan proses koreksi transaksi penjualan yang telah selesai.

Perubahan tidak dilakukan dengan mengedit Sales Transaction.

---

## Return Workflow

Sales Transaction

↓

Return Request

↓

Validation

↓

Inventory Domain

↓

Accounting Engine

↓

Refund

↓

Completed

---

## Rules

### RR-001

Return wajib mengacu pada Sales Transaction yang sah.

---

### RR-002

Jumlah barang yang diretur tidak boleh melebihi jumlah yang dijual.

---

### RR-003

Inventory Domain bertanggung jawab memperbarui stok hasil retur.

---

### RR-004

Accounting Engine menghasilkan transaksi koreksi yang diperlukan.

---

### RR-005

Seluruh Return dan Refund wajib memiliki alasan (Reason Code) dan pengguna yang memprosesnya.

---

# 12. Transaction Lifecycle

Seluruh Sales Transaction mengikuti siklus hidup berikut.

Draft

↓

Active Cart

↓

On Hold (opsional)

↓

Checkout

↓

Waiting Payment

↓

Paid

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

Waiting Payment

↓

Expired

---

Paid

↓

Refunded

---

Completed

↓

Returned

---

## Lifecycle Rules

### TL-001

Perpindahan status hanya boleh mengikuti transisi yang telah ditentukan.

---

### TL-002

Status tidak dapat dilompati.

---

### TL-003

Setiap perubahan status harus dicatat pada Audit Log.

---

### TL-004

Transaksi yang telah Completed tidak dapat diedit.

Perubahan dilakukan melalui Return, Refund, atau Correction.

---# 13. Promotion and Discount

Promotion merupakan mekanisme untuk memberikan insentif penjualan tanpa mengubah aturan dasar transaksi.

Seluruh Promotion dan Discount harus dapat diaudit dan ditelusuri.

---

## Supported Promotion Types

BusinessOS mendukung berbagai jenis promosi, antara lain:

- Percentage Discount
- Fixed Amount Discount
- Buy X Get Y
- Bundle Package
- Happy Hour
- Member Discount
- Voucher / Coupon
- Cashback (Roadmap)

Arsitektur harus memungkinkan penambahan jenis promosi baru tanpa mengubah domain inti.

---

## Promotion Rules

### PROMO-001

Promotion hanya berlaku selama periode aktif.

---

### PROMO-002

Promotion dapat dibatasi berdasarkan:

- Organization
- Branch
- Product
- Product Category
- Customer Group
- Sales Channel
- Tanggal dan Jam

---

### PROMO-003

Apabila terdapat lebih dari satu Promotion yang memenuhi syarat, sistem mengikuti Promotion Policy yang ditetapkan Organization.

---

### PROMO-004

Setiap Promotion yang diterapkan harus tercatat pada Sales Transaction.

---

## Discount Rules

### DISC-001

Discount dapat diberikan pada:

- Item
- Cart
- Customer
- Promotion

---

### DISC-002

Discount Manual hanya dapat diberikan oleh pengguna yang memiliki Permission.

---

### DISC-003

Apabila Discount melebihi batas yang ditentukan Organization, sistem harus meminta Approval sesuai kebijakan yang berlaku.

---

# 14. Domain Integration Rules

POS Domain berfungsi sebagai orchestrator dan tidak mengambil alih tanggung jawab domain lain.

---

## Inventory Domain

POS menghasilkan Business Event penjualan.

Inventory Domain bertanggung jawab mengurangi atau menambah stok sesuai transaksi.

---

## Finance Domain

POS tidak membuat jurnal akuntansi.

Accounting Engine bertanggung jawab menghasilkan Journal, Journal Entry, dan General Ledger.

---

## Treasury Domain

POS hanya merekam metode pembayaran yang dipilih pelanggan.

Treasury Domain bertanggung jawab terhadap perubahan saldo kas, bank, atau media pembayaran lainnya.

---

## Customer Domain

POS menggunakan data Customer untuk:

- Membership
- Loyalty
- Riwayat Pembelian
- Harga Khusus
- Promotion

POS tidak mengelola Master Data Customer.

---

# 15. Performance Guidelines

POS harus tetap responsif pada perangkat Android kelas menengah maupun perangkat dengan spesifikasi terbatas.

---

## PG-001

Cart harus dapat digunakan sepenuhnya dalam mode offline.

---

## PG-002

Checkout harus tetap tersedia selama data penting telah tersinkronisasi sebelumnya dan aturan bisnis mengizinkan transaksi offline.

---

## PG-003

Sinkronisasi dilakukan secara bertahap (incremental synchronization) untuk mengurangi penggunaan jaringan.

---

## PG-004

Proses Checkout harus selesai dalam waktu sesingkat mungkin dengan meminimalkan operasi yang tidak diperlukan pada perangkat.

---

## PG-005

POS harus mampu melanjutkan transaksi apabila aplikasi ditutup atau perangkat dihidupkan kembali sebelum Checkout selesai.

---

# 16. References

Dokumen ini mengacu pada:

- AICTX-001 Project Context
- AICTX-002 Product Requirements
- AICTX-003 System Architecture
- AICTX-004 Tech Stack
- AICTX-005 Database Design
- AICTX-006 Authentication and RBAC
- AICTX-007 Finance Module
- AICTX-008 Inventory Domain

Seluruh perubahan pada POS Domain harus tetap konsisten dengan dokumen-dokumen tersebut.

---

# 17. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial POS Domain Specification |
| 1.1.0 | Current | Enterprise POS Architecture Revision |

---

# 18. Approval

Status dokumen mengikuti tahapan berikut:

Draft

↓

Review

↓

Architecture Review

↓

Locked

POS Domain dinyatakan **Locked** setelah seluruh aturan penjualan, pembayaran, promosi, integrasi domain, dan performa disetujui.

---

# End of Document