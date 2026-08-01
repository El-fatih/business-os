---
document:
  id: AICTX-007
  title: Finance Module
  version: 1.0.0
  status: Draft
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan aturan bisnis (Business Rules) untuk seluruh proses keuangan BusinessOS. Dokumen ini menjadi acuan implementasi Domain Layer, Use Case, Accounting Engine, Repository, serta antarmuka pengguna pada modul keuangan.

dependencies:
  - 01_PROJECT_CONTEXT.md
  - 02_PRODUCT_REQUIREMENTS.md
  - 03_SYSTEM_ARCHITECTURE.md
  - 04_TECH_STACK.md
  - 05_DATABASE_DESIGN.md
  - 06_AUTHENTICATION_AND_RBAC.md

next_documents:
  - 08_INVENTORY_MODULE.md
---

# 1. Executive Summary

Finance Module merupakan inti dari BusinessOS.

Seluruh aktivitas bisnis yang memiliki dampak finansial dicatat sebagai BusinessTransaction.

BusinessOS memisahkan transaksi bisnis dari pencatatan akuntansi.

Pengguna hanya berinteraksi dengan transaksi bisnis.

Accounting Engine bertanggung jawab menghasilkan Journal, Journal Entry, dan General Ledger secara otomatis.

Dengan pendekatan ini, aplikasi tetap sederhana digunakan oleh UMKM namun tetap menghasilkan laporan keuangan yang memenuhi prinsip akuntansi berpasangan (Double-Entry Accounting).

---

# 2. Finance Principles

## FP-001

Setiap aktivitas bisnis yang memengaruhi keuangan wajib dicatat sebagai BusinessTransaction.

---

## FP-002

BusinessTransaction merupakan sumber kebenaran (Source of Truth) seluruh aktivitas keuangan.

---

## FP-003

Pengguna tidak membuat Journal secara manual.

Journal dihasilkan oleh Accounting Engine.

---

## FP-004

Setiap BusinessTransaction harus menghasilkan Journal yang seimbang.

Total Debit harus sama dengan Total Credit.

---

## FP-005

Laporan keuangan dihasilkan dari General Ledger, bukan dari BusinessTransaction secara langsung.

---

## FP-006

Seluruh transaksi harus dapat dilacak hingga dokumen sumbernya (Auditability).

---

## FP-007

Modul keuangan harus tetap dapat beroperasi dalam mode Offline-First.

---

# 3. Financial Concepts

BusinessOS membedakan beberapa konsep utama berikut.

## Business Transaction

Aktivitas bisnis yang dilakukan oleh pengguna.

Contoh:

- Penjualan
- Pembelian
- Pengeluaran
- Penerimaan
- Transfer

---

## Accounting Transaction

Representasi akuntansi yang dihasilkan oleh Accounting Engine.

Accounting Transaction tidak dibuat secara langsung oleh pengguna.

---

## Chart of Account

Daftar akun akuntansi yang digunakan oleh sistem.

---

## Journal

Dokumen akuntansi hasil pemrosesan BusinessTransaction.

---

## General Ledger

Catatan permanen hasil posting Journal Entry.

---

# 4. Business Events

Business Event merupakan kejadian nyata yang terjadi pada bisnis.

Contoh:

- Barang terjual.
- Barang dibeli.
- Modal ditambahkan.
- Biaya operasional dibayar.
- Gaji dibayarkan.
- Piutang diterima.
- Hutang dibayar.
- Transfer kas dilakukan.

Business Event memicu terbentuknya BusinessTransaction.

---

# 5. Business Transaction Flow

Seluruh transaksi keuangan mengikuti alur berikut.

```text
Business Event
      │
      ▼
BusinessTransaction
      │
      ▼
Accounting Engine
      │
      ▼
Journal
      │
      ▼
Journal Entry
      │
      ▼
General Ledger
      │
      ▼
Financial Report
```

Tidak ada proses yang boleh melewati Accounting Engine.

---

# 6. Transaction Categories

Versi pertama BusinessOS mendukung kategori transaksi berikut.

## Income

Seluruh transaksi yang menambah aset atau pendapatan.

Contoh:

- Penjualan
- Pendapatan jasa
- Pendapatan lain

---

## Expense

Seluruh transaksi yang mengurangi kas atau menambah beban.

Contoh:

- Operasional
- Gaji
- Utilitas
- Transportasi
- Marketing

---

## Transfer

Pemindahan dana antar akun kas.

---

## Capital

Penambahan modal pemilik.

---

## Withdrawal

Pengambilan dana oleh pemilik.

---

## Accounts Receivable

Pencatatan piutang usaha.

---

## Accounts Payable

Pencatatan hutang usaha.

---# 7. Business Transaction Types

Business Transaction Type mendefinisikan jenis aktivitas bisnis yang dilakukan pengguna.

Setiap BusinessTransaction wajib memiliki satu Transaction Type.

Transaction Type digunakan oleh Accounting Engine untuk menentukan aturan akuntansi yang sesuai.

---

## 7.1 Sales

### Purpose

Mencatat transaksi penjualan barang atau jasa kepada pelanggan.

### Business Events

- Penjualan tunai
- Penjualan kredit
- Penjualan dengan diskon
- Penjualan dengan pajak
- Retur penjualan
- Pembatalan penjualan

### Expected Result

- Pendapatan bertambah.
- Persediaan berkurang (jika berlaku).
- Kas atau Piutang bertambah.

---

## 7.2 Purchase

### Purpose

Mencatat pembelian barang maupun jasa.

### Business Events

- Pembelian tunai
- Pembelian kredit
- Pembelian aset
- Pembelian persediaan
- Retur pembelian

### Expected Result

- Persediaan atau aset bertambah.
- Kas berkurang atau Hutang bertambah.

---

## 7.3 Expense

### Purpose

Mencatat biaya operasional bisnis.

### Business Events

- Pembayaran listrik
- Pembayaran air
- Pembayaran internet
- Biaya transportasi
- Biaya pemasaran
- Biaya administrasi
- Pembayaran gaji

### Expected Result

- Beban bertambah.
- Kas berkurang.

---

## 7.4 Income

### Purpose

Mencatat pendapatan di luar aktivitas penjualan utama.

### Business Events

- Pendapatan bunga
- Pendapatan sewa
- Pendapatan komisi
- Pendapatan lain-lain

### Expected Result

- Pendapatan bertambah.
- Kas atau Piutang bertambah.

---

## 7.5 Transfer

### Purpose

Memindahkan dana antar akun kas atau rekening.

### Business Events

- Kas ke Bank
- Bank ke Kas
- Bank A ke Bank B
- E-Wallet ke Bank

### Expected Result

- Saldo akun sumber berkurang.
- Saldo akun tujuan bertambah.
- Tidak memengaruhi laba rugi.

---

## 7.6 Capital Contribution

### Purpose

Mencatat penambahan modal oleh pemilik.

### Business Events

- Setoran modal awal
- Penambahan modal
- Investasi tambahan

### Expected Result

- Kas bertambah.
- Modal bertambah.

---

## 7.7 Owner Withdrawal

### Purpose

Mencatat pengambilan dana oleh pemilik untuk keperluan pribadi.

### Business Events

- Prive
- Penarikan kas
- Pengambilan aset

### Expected Result

- Kas berkurang.
- Modal berkurang.

---

## 7.8 Accounts Receivable

### Purpose

Mengelola piutang pelanggan.

### Business Events

- Penjualan kredit
- Pembayaran sebagian
- Pelunasan
- Penghapusan piutang

### Expected Result

- Saldo piutang berubah sesuai transaksi.

---

## 7.9 Accounts Payable

### Purpose

Mengelola hutang kepada pemasok.

### Business Events

- Pembelian kredit
- Pembayaran sebagian
- Pelunasan
- Penghapusan hutang

### Expected Result

- Saldo hutang berubah sesuai transaksi.

---

# 8. Accounting Engine Rules

Accounting Engine bertugas menerjemahkan BusinessTransaction menjadi pencatatan akuntansi.

Seluruh proses dilakukan secara otomatis tanpa intervensi pengguna.

---

## AER-001

Setiap BusinessTransaction menghasilkan tepat satu Journal.

---

## AER-002

Setiap Journal terdiri dari minimal dua JournalEntry.

---

## AER-003

Jumlah total Debit harus sama dengan jumlah total Credit.

---

## AER-004

Mapping akun dilakukan berdasarkan Transaction Type dan Category.

---

## AER-005

Apabila mapping akun tidak ditemukan, transaksi tidak boleh diposting.

---

## AER-006

Posting ke General Ledger hanya dilakukan setelah Journal lolos seluruh validasi.

---

## AER-007

Apabila terjadi kegagalan pada proses posting, BusinessTransaction tetap tersimpan dan diberi status **Posting Failed** untuk diproses ulang oleh sistem.
# 12. Treasury Accounts

## Purpose

Treasury Account merepresentasikan seluruh media penyimpanan dan perpindahan dana yang dimiliki Organization.

Treasury Account digunakan oleh pengguna ketika memilih sumber atau tujuan pembayaran.

Treasury Account bukan merupakan akun akuntansi.

Mapping ke Chart of Account dilakukan oleh Accounting Engine.

---

## Examples

Cash

Petty Cash

Bank Account

E-Wallet

QRIS Settlement

Marketplace Balance

Payment Gateway Balance

Virtual Account

---

## Responsibilities

Treasury Account bertanggung jawab terhadap:

- penyimpanan saldo operasional,
- sumber pembayaran,
- tujuan penerimaan,
- transfer antar akun treasury,
- rekonsiliasi saldo.

---

## Relationships

BusinessTransaction

↓

TreasuryAccount

↓

AccountingEngine

↓

ChartOfAccount

---

## Core Fields

- id
- organizationId
- branchId
- accountName
- accountType
- currency
- openingBalance
- currentBalance
- status

---

## Business Rules

- Satu Organization dapat memiliki banyak Treasury Account.
- Treasury Account dapat dimiliki oleh Branch tertentu.
- Transfer antar Treasury Account tidak memengaruhi laba rugi.
- Seluruh perubahan saldo wajib menghasilkan BusinessTransaction.
- Saldo Treasury tidak boleh diubah secara langsung kecuali melalui Opening Balance atau proses penyesuaian resmi.

---

## Future Ready

Treasury Account dirancang untuk mendukung:

- Multi Currency
- Payment Gateway
- QRIS
- Marketplace Settlement
- Bank Reconciliation
- Cash Forecast
- Cash Management
- Treasury Dashboard

---# 13. Financial Reporting Rules

Financial Report merupakan keluaran resmi dari Finance Module.

Seluruh laporan keuangan dihasilkan dari General Ledger yang telah diposting.

BusinessTransaction tidak digunakan secara langsung sebagai sumber laporan.

---

## FR-001 Single Source of Truth

General Ledger merupakan satu-satunya sumber data untuk seluruh laporan keuangan.

Seluruh laporan harus dihasilkan dari data yang telah diposting.

---

## FR-002 Real-Time Reporting

Laporan keuangan diperbarui secara otomatis setelah proses posting berhasil.

Apabila posting gagal, laporan tidak boleh berubah.

---

## FR-003 Immutable Reporting

Laporan tidak dapat diubah secara manual.

Perubahan laporan hanya dapat terjadi melalui BusinessTransaction baru yang menghasilkan Journal baru.

---

## FR-004 Organization Scope

Laporan dapat dibuat pada tingkat:

- Organization
- Branch

Apabila laporan dibuat pada tingkat Organization, seluruh data Branch yang termasuk dalam Organization tersebut harus digabungkan sesuai ruang lingkup akses pengguna.

---

## FR-005 Period Scope

Seluruh laporan harus mendukung filter berdasarkan periode.

Contoh:

- Hari
- Minggu
- Bulan
- Kuartal
- Tahun
- Rentang Tanggal

---

# 13.1 Profit and Loss Statement

## Purpose

Menampilkan pendapatan, beban, dan laba atau rugi selama periode tertentu.

### Data Source

- Revenue Accounts
- Expense Accounts

### Generated From

General Ledger

### Calculation

Net Profit = Total Revenue - Total Expense

---

# 13.2 Balance Sheet

## Purpose

Menampilkan posisi keuangan pada tanggal tertentu.

### Data Source

- Assets
- Liabilities
- Equity

### Generated From

General Ledger

### Validation Rule

Assets = Liabilities + Equity

---

# 13.3 Cash Flow Statement

## Purpose

Menampilkan arus kas masuk dan arus kas keluar.

### Categories

- Operating Activities
- Investing Activities
- Financing Activities

### Generated From

General Ledger dan klasifikasi Treasury Account.

---

# 13.4 Trial Balance

## Purpose

Memastikan keseimbangan debit dan kredit.

### Validation Rule

Total Debit = Total Credit

Apabila tidak seimbang, sistem wajib menolak proses penutupan periode.

---

# 13.5 General Ledger Report

## Purpose

Menampilkan seluruh mutasi setiap akun akuntansi.

### Data Source

General Ledger

### Features

- Filter Account
- Filter Period
- Filter Branch
- Export

---

# 13.6 Treasury Report

## Purpose

Menampilkan mutasi dan saldo seluruh Treasury Account.

### Data Source

BusinessTransaction dan Treasury Account.

### Features

- Saldo Awal
- Penerimaan
- Pengeluaran
- Transfer
- Saldo Akhir

---

# 14. Accounting Period

Accounting Period merupakan rentang waktu resmi untuk pencatatan transaksi keuangan.

Seluruh BusinessTransaction harus berada dalam Accounting Period yang aktif.

---

## Supported Period

- Bulanan
- Tahunan

Arsitektur harus memungkinkan penambahan periode fiskal khusus di masa depan.

---

## Accounting Period Status

- Open
- Closing
- Closed

---

## Business Rules

### APD-001

Transaksi hanya dapat dibuat pada Accounting Period yang berstatus **Open**.

---

### APD-002

Accounting Period yang telah **Closed** tidak dapat menerima transaksi baru.

---

### APD-003

Accounting Period hanya dapat ditutup apabila seluruh Journal telah berhasil diposting.

---

### APD-004

Apabila diperlukan koreksi terhadap periode yang telah ditutup, sistem harus menggunakan transaksi koreksi atau jurnal pembalik (Reversal), bukan mengubah transaksi historis secara langsung.

---

### APD-005

Proses penutupan periode harus menghasilkan jejak audit (Audit Log) yang mencatat waktu, pengguna, dan ringkasan proses penutupan.

---

# 15. Correction and Reversal

Kesalahan pencatatan tidak diperbaiki dengan mengubah histori transaksi.

BusinessOS menggunakan prinsip audit trail penuh.

## Rules

### CR-001

BusinessTransaction yang telah diposting tidak dapat diedit.

---

### CR-002

Koreksi dilakukan melalui BusinessTransaction baru yang memiliki referensi ke transaksi asal.

---

### CR-003

Accounting Engine menghasilkan Journal koreksi atau Journal pembalik sesuai jenis kesalahan.

---

### CR-004

Seluruh transaksi koreksi wajib menyimpan alasan koreksi dan identitas pengguna yang melakukan koreksi.

---

### CR-005

Laporan keuangan harus mencerminkan hasil akhir setelah transaksi koreksi diposting, tanpa menghapus histori sebelumnya.

---# 16. References

Dokumen ini disusun berdasarkan dan harus konsisten dengan dokumen berikut:

- AICTX-001 Project Context
- AICTX-002 Product Requirements
- AICTX-003 System Architecture
- AICTX-004 Tech Stack
- AICTX-005 Database Design
- AICTX-006 Authentication and RBAC

Seluruh perubahan pada Finance Module wajib mempertimbangkan konsistensi dengan dokumen-dokumen di atas.

---

# 17. Revision History

| Version | Date | Description |
|----------|------|-------------|
| 1.0.0 | Initial Draft | Initial Finance Domain Specification |
| 1.1.0 | Current | Enterprise Finance Architecture Revision |

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

Finance Module dinyatakan **Locked** setelah seluruh aturan bisnis, aturan akuntansi, dan integrasi dengan Foundation Documents telah disetujui.

---

# End of Document