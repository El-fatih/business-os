---
document:
  id: AICTX-014
  title: Settings & Configuration
  version: 1.0.0
  status: Locked
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan pusat konfigurasi (Configuration Center) BusinessOS. Domain ini bertanggung jawab mengelola seluruh kebijakan bisnis (Business Policies), preferensi organisasi, serta parameter sistem yang dapat dikonfigurasi tanpa mengubah kode aplikasi.

dependencies:
  - 001_PROJECT_CONTEXT.md
  - 003_SYSTEM_ARCHITECTURE.md
  - 005_DATABASE_DESIGN.md
  - 006_AUTHENTICATION_AND_RBAC.md
  - 011_MASTER_DATA_OWNERSHIP.md
  - 013_TREASURY_DOMAIN.md

next_documents:
  - 015_REPORTING_DASHBOARD.md
---

# 1. Executive Summary

Settings & Configuration merupakan pusat pengelolaan seluruh konfigurasi BusinessOS.

Seluruh kebijakan bisnis yang dapat diubah oleh Organization harus dikelola melalui domain ini.

Domain lain tidak diperbolehkan menyimpan konfigurasi bisnis secara mandiri.

---

# 2. Scope

Settings & Configuration mencakup:

- Organization Settings
- Business Policies
- Numbering Configuration
- Approval Configuration
- Module Configuration
- Localization
- System Preferences

Domain ini tidak mengelola transaksi operasional.

---

# 3. Design Principles

## CFG-001

Seluruh konfigurasi bisnis harus berasal dari Settings Domain.

---

## CFG-002

Domain lain hanya membaca konfigurasi melalui Configuration Service.

---

## CFG-003

Perubahan konfigurasi tidak boleh mengubah data historis, kecuali secara eksplisit dilakukan melalui proses migrasi atau penyesuaian yang terdokumentasi.

---

## CFG-004

Seluruh perubahan konfigurasi harus tercatat pada Audit Trail.

---

## CFG-005

Konfigurasi dapat ditetapkan pada tingkat Organization dan, jika diizinkan, ditimpa (override) pada tingkat Branch.

---

## CFG-006

Seluruh konfigurasi mengikuti prinsip Offline-First dan mendukung sinkronisasi.

---

# 4. Configuration Categories

Settings Domain mengelompokkan konfigurasi ke dalam kategori berikut:

- Organization Settings
- Finance Settings
- Treasury Settings
- Inventory Settings
- POS Settings
- Procurement Settings
- Reporting Settings
- Security Settings
- Notification Settings (Roadmap)

Setiap kategori dapat berkembang tanpa mengubah arsitektur dasar BusinessOS.
# 5. Organization Settings

Organization Settings menyimpan konfigurasi dasar yang berlaku untuk seluruh Organization.

---

## Core Fields

- organizationName
- legalBusinessName
- businessType
- defaultCurrency
- timezone
- language
- dateFormat
- timeFormat
- fiscalYearStart
- logoUrl
- status

---

## Business Rules

- Seluruh modul menggunakan Organization Settings sebagai konfigurasi utama.
- Branch dapat melakukan override hanya pada konfigurasi yang diizinkan.
- Perubahan Organization Settings tidak mengubah data historis.

---

# 6. Numbering Settings

Numbering Settings mengelola format penomoran seluruh Master Data dan transaksi.

---

## Configurable Objects

### Master Data

- Customer
- Supplier
- Product
- Asset
- Financial Account

### Business Transactions

- Sales
- Purchase
- Cash In
- Cash Out
- Transfer
- Adjustment
- Journal
- Stock Opname
- Inventory Adjustment

---

## Configurable Properties

- Prefix
- Suffix
- Running Number Length
- Reset Policy
- Separator
- Include Year
- Include Month
- Include Branch Code

---

## Business Rules

- Setiap jenis dokumen memiliki konfigurasi penomoran sendiri.
- Nomor harus unik dalam ruang lingkup Organization.
- Penomoran menggunakan Numbering Service.

---

# 7. Approval Settings

Approval Settings menentukan transaksi yang memerlukan persetujuan.

---

## Configurable Objects

- Purchase
- Treasury Adjustment
- Cash Out
- Inventory Adjustment
- Stock Opname
- Journal
- Asset Disposal

---

## Configurable Properties

- Approval Required
- Approval Level
- Minimum Amount
- Maximum Amount
- Multi-Level Approval
- Auto Approval

---

## Business Rules

- Approval bersifat opsional sesuai kebijakan Organization.
- Multi-Level Approval didukung untuk transaksi tertentu.
- Approval mengikuti Role dan Permission yang berlaku.
# 8. Treasury Settings

Treasury Settings mengatur kebijakan operasional pada Treasury Domain.

---

## Configurable Properties

- Allow Overdraft
- Default Financial Account
- Default Payment Method
- Reconciliation Frequency
- Auto Posting
- Transfer Approval Required
- Cash Adjustment Approval Required
- Maximum Cash Balance (Optional)
- Minimum Cash Balance (Optional)

---

## Business Rules

- Seluruh transaksi Treasury mengikuti konfigurasi yang berlaku.
- Approval bersifat configurable.
- Overdraft hanya diizinkan apabila diaktifkan oleh Organization.

---

# 9. Inventory Settings

Inventory Settings mengatur perilaku operasional Inventory Domain.

---

## Configurable Properties

- Allow Negative Stock
- Inventory Valuation Method
- Default Warehouse
- Default Storage Location
- Reorder Point Enabled
- Safety Stock Enabled
- Auto Generate Stock Movement
- Stock Adjustment Approval Required

---

## Inventory Valuation Method

BusinessOS mendukung metode:

- FIFO
- Weighted Average

LIFO tidak didukung karena tidak diperbolehkan pada standar akuntansi di banyak negara dan semakin jarang digunakan.

---

## Business Rules

- Perubahan metode penilaian persediaan tidak berlaku surut terhadap transaksi historis.
- Negative Stock hanya diperbolehkan apabila diaktifkan.
- Reorder Point bersifat opsional.

---

# 10. POS Settings

POS Settings mengatur perilaku operasional Point of Sale.

---

## Configurable Properties

- Shift Required
- Auto Open Shift
- Auto Close Shift
- Receipt Template
- Receipt Footer
- Default Payment Method
- Default Customer
- Enable QRIS
- Enable Digital Wallet
- Enable Discount
- Maximum Discount Percentage
- Require Manager Approval for Discount
- Round Transaction Total

---

## Business Rules

- Shift dapat diwajibkan sesuai kebijakan Organization.
- Diskon di atas batas maksimum memerlukan Approval.
- Receipt Template dapat disesuaikan tanpa mengubah kode aplikasi.

---

# 11. Procurement Settings

Procurement Settings mengatur perilaku operasional pembelian.

---

## Configurable Properties

- Default Payment Term
- Purchase Approval Required
- Multi-Level Approval
- Auto Close Purchase Order
- Default Supplier
- Allow Partial Receipt

---

## Business Rules

- Approval mengikuti konfigurasi Organization.
- Partial Receipt dapat diaktifkan atau dinonaktifkan.
- Purchase Order dapat ditutup otomatis setelah seluruh item diterima.
# 12. Finance Settings

Finance Settings mengatur kebijakan keuangan Organization.

---

## Configurable Properties

- Accounting Period
- Fiscal Year
- Default Currency
- Tax Configuration
- Rounding Method
- Decimal Precision
- Expense Approval Required
- Journal Approval Required
- Period Closing Policy

---

## Business Rules

- Periode akuntansi dapat dibuka atau ditutup sesuai kebijakan Organization.
- Transaksi pada periode tertutup tidak dapat diubah tanpa proses khusus.
- Perubahan konfigurasi Finance tidak mengubah transaksi historis.

---

# 13. Reporting Settings

Reporting Settings mengatur perilaku penyajian laporan.

---

## Configurable Properties

- Default Report Period
- Default Dashboard
- Number Format
- Currency Display Format
- Export Format
- Report Access Permission

---

## Supported Export

BusinessOS mendukung:

- PDF
- Excel
- CSV

---

## Business Rules

- Hak melihat laporan mengikuti Role dan Permission.
- Dashboard dapat berbeda berdasarkan Role pengguna.
- Export data mengikuti hak akses pengguna.

---

# 14. Security Settings

Security Settings mengatur kebijakan keamanan Organization.

---

## Configurable Properties

- Password Policy
- Session Timeout
- Maximum Login Attempt
- Two Factor Authentication (Roadmap)
- Device Authorization
- Login Notification

---

## Business Rules

- Security Settings tidak boleh menurunkan aturan keamanan minimum sistem.
- Owner selalu memiliki akses pemulihan Organization.
- Seluruh aktivitas keamanan dicatat pada Audit Trail.

---

# 15. Notification Settings

Notification Settings mengatur komunikasi sistem.

---

## Configurable Properties

- Enable Notification
- Notification Channel
- Reminder Schedule
- Approval Reminder
- Transaction Alert

---

## Supported Channels

Roadmap:

- In-App Notification
- Email
- WhatsApp
- Push Notification

---

## Business Rules

- Notification tidak boleh mengubah hasil transaksi.
- Notification bersifat pendukung informasi.
- Kegagalan pengiriman notifikasi tidak membatalkan transaksi.
# 16. Configuration Hierarchy

BusinessOS menerapkan hierarki konfigurasi untuk mendukung fleksibilitas berbagai skala bisnis.

Urutan prioritas konfigurasi adalah sebagai berikut:

Organization

↓

Branch (Opsional)

↓

User (Opsional)

---

## Configuration Resolution

Saat aplikasi membaca konfigurasi, urutan pencarian dilakukan sebagai berikut:

1. User Configuration
2. Branch Configuration
3. Organization Configuration
4. System Default

Nilai pertama yang ditemukan menjadi konfigurasi aktif.

---

## Business Rules

- Organization merupakan sumber konfigurasi utama.
- Branch hanya dapat melakukan override pada konfigurasi yang diizinkan.
- User hanya dapat melakukan personal preference, bukan Business Policy.
- Apabila konfigurasi tidak ditemukan, sistem menggunakan System Default.

---

# 17. Configuration Cache

Seluruh konfigurasi harus tersedia secara Offline.

Implementasi dapat menggunakan cache lokal agar konfigurasi dapat diakses tanpa koneksi internet.

---

## Cache Rules

### CFG-CACHE-001

Konfigurasi dibaca dari Local Database apabila tersedia.

---

### CFG-CACHE-002

Sinkronisasi dilakukan secara incremental.

---

### CFG-CACHE-003

Konfigurasi diperbarui setelah sinkronisasi berhasil.

---

### CFG-CACHE-004

Perubahan konfigurasi menghasilkan Configuration Changed Event.

---

# 18. Security Guidelines

Settings Domain mengikuti AICTX-006 Authentication & RBAC.

---

## SEC-001

Hanya pengguna yang memiliki Permission yang dapat mengubah konfigurasi.

---

## SEC-002

Seluruh perubahan konfigurasi wajib menghasilkan Audit Trail.

---

## SEC-003

Perubahan konfigurasi kritikal dapat memerlukan Approval sesuai kebijakan Organization.

---

## SEC-004

Konfigurasi tidak boleh digunakan untuk melemahkan keamanan minimum sistem.

---

# 19. Performance Guidelines

Settings Domain harus mampu memberikan konfigurasi secara cepat kepada seluruh modul.

---

## PG-001

Konfigurasi harus dapat diakses tanpa query berulang ke database.

---

## PG-002

Configuration Service dapat menggunakan cache in-memory.

---

## PG-003

Konfigurasi harus mendukung lazy refresh setelah sinkronisasi.

---

## PG-004

Perubahan konfigurasi tidak boleh mengganggu transaksi yang sedang berjalan.

Konfigurasi baru berlaku untuk transaksi berikutnya kecuali dinyatakan berbeda oleh kebijakan Organization.

---

# 20. References

Dokumen ini mengacu pada:

- AICTX-003 System Architecture
- AICTX-005 Database Design
- AICTX-006 Authentication & RBAC
- AICTX-011 Master Data Ownership
- AICTX-013 Treasury Domain

---

# 21. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial Settings & Configuration Specification |

---

# 22. Approval

Status dokumen mengikuti tahapan:

Draft

↓

Review

↓

Architecture Review

↓

Locked

Settings & Configuration dinyatakan Locked setelah seluruh kategori konfigurasi dan kebijakan implementasi disetujui.

---

# End of Document