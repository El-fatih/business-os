---
document:
  id: AICTX-013
  title: Treasury Domain
  version: 1.0.0
  status: Draft
  owner: BusinessOS
  classification: AI Context Document
  priority: High

purpose:
  Mendefinisikan aturan bisnis, ruang lingkup, transaksi, dan integrasi Treasury Domain sebagai pengelola kas dan rekening organisasi. Treasury Domain bertanggung jawab atas seluruh pergerakan dana aktual (cash movement) dan menjadi penghubung antara operasional bisnis dengan Finance Domain.

dependencies:
  - 03_SYSTEM_ARCHITECTURE.md
  - 05_DATABASE_DESIGN.md
  - 06_AUTHENTICATION_AND_RBAC.md
  - 07_FINANCE_MODULE.md
  - 011_MASTER_DATA_OWNERSHIP.md

next_documents:
  - 014_SETTINGS_CONFIGURATION.md
---

# 1. Executive Summary

Treasury Domain bertanggung jawab mengelola seluruh kas, rekening bank, dan pergerakan dana aktual milik Organization.

Treasury Domain mencatat penerimaan, pengeluaran, transfer, dan rekonsiliasi saldo.

Treasury Domain tidak bertanggung jawab menghasilkan jurnal akuntansi. Pencatatan akuntansi dilakukan oleh Finance Domain sesuai aturan yang berlaku.

---

# 2. Scope

Treasury Domain mencakup:

- Cash Account Management
- Bank Account Management
- Cash In
- Cash Out
- Cash Transfer
- Bank Transfer
- Balance Reconciliation
- Cash Movement History

Treasury Domain tidak mengelola Chart of Accounts maupun General Ledger.

---

# 3. Design Principles

## TRS-001

Treasury Domain mengelola saldo aktual kas dan rekening.

---

## TRS-002

Setiap pergerakan dana harus berasal dari transaksi yang sah.

---

## TRS-003

Seluruh transaksi Treasury harus dapat ditelusuri melalui Audit Trail.

---

## TRS-004

Perubahan saldo dilakukan melalui transaksi Treasury, bukan melalui perubahan nilai saldo secara langsung.

---

## TRS-005

Treasury Domain tidak menghasilkan jurnal akuntansi secara langsung.

Accounting Engine bertanggung jawab menghasilkan jurnal berdasarkan Business Event yang diterima.

---

## TRS-006

Seluruh transaksi mengikuti Unified State Machine BusinessOS.

---

# 4. Master Data

Treasury Domain menjadi Data Owner untuk Master Data berikut:

- Cash Account
- Bank Account
- Payment Method

Spesifikasi kepemilikan mengacu pada AICTX-011 Master Data Ownership.
# 5. Master Data

Treasury Domain bertanggung jawab terhadap Master Data yang berkaitan dengan penyimpanan dan pergerakan dana.

---

## 5.1 Cash Account

Cash Account merepresentasikan kas fisik yang dimiliki Organization.

### Contoh

- Kas Utama
- Kas Operasional
- Kas Kecil
- Kas Cabang

### Core Fields

- accountCode
- accountName
- branchId
- currency
- openingBalance
- status

### Business Rules

- Setiap Cash Account dimiliki oleh satu Organization.
- Cash Account dapat dibatasi untuk Branch tertentu.
- Saldo tidak dapat diubah secara langsung.
- Perubahan saldo hanya melalui transaksi Treasury.

---

## 5.2 Bank Account

Bank Account merepresentasikan rekening bank milik Organization.

### Core Fields

- accountCode
- accountName
- bankName
- accountNumber
- accountHolder
- branchId
- currency
- openingBalance
- status

### Business Rules

- Rekening harus unik dalam Organization.
- Satu Branch dapat memiliki lebih dari satu rekening.
- Saldo berubah hanya melalui transaksi Treasury.

---

## 5.3 Digital Wallet

Digital Wallet merepresentasikan akun dompet digital milik Organization.

### Contoh

- GoPay
- OVO
- DANA
- ShopeePay
- LinkAja

### Core Fields

- accountCode
- accountName
- providerName
- accountIdentifier
- branchId
- currency
- openingBalance
- status

### Business Rules

- Digital Wallet diperlakukan sebagai akun penyimpanan dana.
- Saldo hanya berubah melalui transaksi Treasury.
- Satu Organization dapat memiliki lebih dari satu Digital Wallet.

---

## 5.4 Payment Method

Payment Method mendefinisikan metode pembayaran yang dapat digunakan pada transaksi.

### Contoh

- Tunai
- Transfer Bank
- QRIS
- Debit
- Kredit
- GoPay
- OVO
- DANA
- ShopeePay
- LinkAja

### Business Rules

- Payment Method dapat diaktifkan atau dinonaktifkan.
- Satu Payment Method dapat dihubungkan dengan satu atau lebih Financial Account sesuai konfigurasi Organization.

---

# 6. Transaksi Treasury

Treasury Domain mengelola seluruh transaksi yang memengaruhi saldo akun keuangan.

---

## 6.1 Kas Masuk (Cash In)

Mencatat penerimaan dana ke Cash Account, Bank Account, atau Digital Wallet.

### Core Fields

- transactionNumber
- transactionDate
- financialAccountId
- amount
- paymentMethodId
- referenceNumber
- notes
- status

### Business Rules

- Harus memiliki sumber transaksi yang jelas.
- Menambah saldo akun tujuan.
- Menghasilkan Business Event untuk Finance Domain.

---

## 6.2 Kas Keluar (Cash Out)

Mencatat pengeluaran dana dari Cash Account, Bank Account, atau Digital Wallet.

### Core Fields

- transactionNumber
- transactionDate
- financialAccountId
- amount
- paymentMethodId
- referenceNumber
- notes
- status

### Business Rules

- Tidak boleh menyebabkan saldo negatif kecuali diizinkan oleh kebijakan Organization.
- Mengurangi saldo akun sumber.
- Menghasilkan Business Event untuk Finance Domain.
# 7. Transfer Dana

Treasury Domain mendukung perpindahan dana antar Financial Account.

Transfer dana dapat dilakukan antar:

- Cash Account
- Bank Account
- Digital Wallet

Seluruh transfer harus menjaga konsistensi saldo.

---

## 7.1 Internal Transfer

Internal Transfer memindahkan dana antar Financial Account milik Organization.

### Contoh

- Kas → Bank
- Bank → Kas
- Bank A → Bank B
- Kas Cabang → Kas Pusat
- Bank → Digital Wallet
- Digital Wallet → Bank
- Digital Wallet → Kas

### Core Fields

- transferNumber
- transferDate
- sourceAccountId
- destinationAccountId
- amount
- referenceNumber
- notes
- status

### Business Rules

- Source Account dan Destination Account tidak boleh sama.
- Saldo Source Account harus mencukupi, kecuali Overdraft diizinkan.
- Pengurangan dan penambahan saldo dilakukan sebagai satu transaksi atomik (Atomic Transaction).
- Apabila salah satu proses gagal, seluruh transaksi dibatalkan (Rollback).

---

# 8. Rekonsiliasi Saldo

Rekonsiliasi digunakan untuk mencocokkan saldo sistem dengan saldo aktual.

---

## 8.1 Cash Reconciliation

Digunakan untuk menghitung selisih kas fisik.

### Core Fields

- reconciliationNumber
- financialAccountId
- countedBalance
- systemBalance
- difference
- notes
- status

### Business Rules

- Tidak mengubah saldo secara langsung.
- Selisih harus diselesaikan melalui transaksi Adjustment yang sah.
- Seluruh selisih wajib tercatat pada Audit Trail.

---

## 8.2 Bank Reconciliation

Digunakan untuk mencocokkan saldo sistem dengan mutasi rekening.

### Business Rules

- Mendukung proses rekonsiliasi berkala.
- Selisih tidak boleh langsung mengubah saldo.
- Penyesuaian dilakukan melalui transaksi Adjustment.

---

# 9. Adjustment

Adjustment digunakan untuk memperbaiki saldo akibat kesalahan pencatatan atau hasil rekonsiliasi.

Adjustment bukan transaksi operasional normal.

---

## Core Fields

- adjustmentNumber
- financialAccountId
- adjustmentType
- amount
- reason
- approvedBy
- status

---

## Business Rules

- Wajib memiliki alasan (Reason).
- Dapat memerlukan Approval sesuai kebijakan Organization.
- Menghasilkan Business Event untuk Finance Domain.
- Seluruh Adjustment wajib tercatat pada Audit Trail.

---

# 10. Workflow

Treasury Domain mengikuti Unified State Machine BusinessOS.

Lifecycle standar:

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

## Workflow Rules

### WF-001

Perubahan saldo hanya terjadi setelah transaksi mencapai status Posted.

---

### WF-002

Transaksi Completed tidak dapat diubah secara langsung.

---

### WF-003

Seluruh perubahan status wajib menghasilkan Audit Trail.

---

### WF-004

Approval bersifat configurable sesuai kebijakan Organization.
# 11. Performance Guidelines

Treasury Domain harus mampu memproses transaksi keuangan secara cepat, konsisten, dan aman.

---

## PG-001

Daftar transaksi wajib mendukung pagination atau lazy loading.

---

## PG-002

Saldo Financial Account harus dapat ditampilkan tanpa menghitung ulang seluruh histori transaksi.

Implementasi dapat menggunakan saldo tersimpan (cached balance) yang diperbarui secara atomik setiap transaksi berhasil diposting.

---

## PG-003

Pencarian transaksi minimal mendukung:

- Nomor Transaksi
- Financial Account
- Rentang Tanggal
- Nominal
- Status

---

## PG-004

Sinkronisasi transaksi dilakukan secara incremental sesuai AICTX-003 System Architecture.

---

# 12. Security Guidelines

Treasury Domain mengikuti AICTX-006 Authentication & RBAC.

---

## SEC-001

Pengguna hanya dapat melihat Financial Account sesuai hak akses Organization dan Branch.

---

## SEC-002

Hak membuat, menyetujui, memposting, membatalkan, dan melakukan Adjustment ditentukan oleh Role dan Permission.

---

## SEC-003

Seluruh perubahan saldo wajib menghasilkan Audit Trail.

---

## SEC-004

Adjustment dan Reconciliation dapat memerlukan Approval sesuai kebijakan Organization.

---

# 13. Reports

Treasury Domain menyediakan data bagi Reporting Engine.

Contoh laporan:

- Saldo Kas
- Saldo Rekening Bank
- Saldo Dompet Digital
- Arus Kas Masuk
- Arus Kas Keluar
- Mutasi Financial Account
- Transfer Antar Akun
- Rekonsiliasi Saldo
- Riwayat Adjustment

Treasury Domain tidak menghasilkan laporan akuntansi seperti Neraca atau Laba Rugi.

---

# 14. References

Dokumen ini mengacu pada:

- AICTX-003 System Architecture
- AICTX-005 Database Design
- AICTX-006 Authentication & RBAC
- AICTX-007 Finance Module
- AICTX-011 Master Data Ownership

---

# 15. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial Treasury Domain Specification |

---

# 16. Approval

Status dokumen mengikuti tahapan:

Draft

↓

Review

↓

Architecture Review

↓

Locked

Treasury Domain dinyatakan Locked setelah seluruh Master Data, transaksi, workflow, dan integrasi disetujui.

---

# End of Document