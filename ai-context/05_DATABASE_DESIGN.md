---
document:
  id: AICTX-005
  title: Database Design
  version: 1.1.0
  status: Draft
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan Business Data Model sebagai fondasi seluruh implementasi data BusinessOS. Dokumen ini menjadi sumber kebenaran utama (Single Source of Truth) untuk desain entity, relasi, aturan bisnis, strategi sinkronisasi, dan implementasi Local Database (Isar) maupun Cloud Database (Cloud Firestore).

dependencies:
  - 01_PROJECT_CONTEXT.md
  - 02_PRODUCT_REQUIREMENTS.md
  - 03_SYSTEM_ARCHITECTURE.md
  - 04_TECH_STACK.md

next_documents:
  - 06_AUTHENTICATION_AND_RBAC.md
---

# 1. Executive Summary

Database merupakan fondasi utama BusinessOS.

BusinessOS tidak dirancang berdasarkan struktur database tertentu, tetapi berdasarkan Business Data Model yang merepresentasikan proses bisnis secara konsisten.

Seluruh implementasi Local Database, Cloud Database, Repository, Data Source, Synchronization Engine, Reporting Engine, maupun AI Engine harus mengacu pada model data yang didefinisikan dalam dokumen ini.

Dokumen ini tidak bergantung pada vendor database tertentu. Isar, Cloud Firestore, maupun teknologi database lain hanyalah implementasi dari Business Data Model yang sama.

Dengan pendekatan ini, BusinessOS dapat berkembang dari aplikasi untuk pelaku usaha perorangan hingga platform enterprise tanpa perubahan arsitektur data secara fundamental.

---

# 2. Database Design Goals

Database BusinessOS dirancang untuk memenuhi tujuan berikut.

## DG-001

Menjadi fondasi tunggal (Single Source of Truth) bagi seluruh model data aplikasi.

---

## DG-002

Mendukung arsitektur Offline-First sehingga seluruh proses bisnis tetap dapat berjalan tanpa koneksi internet.

---

## DG-003

Mendukung sinkronisasi data yang aman, konsisten, dan dapat dipulihkan apabila terjadi konflik.

---

## DG-004

Mendukung organisasi dengan satu cabang maupun multi cabang tanpa perubahan struktur data.

---

## DG-005

Mendukung pertumbuhan aplikasi dari UMKM hingga perusahaan berskala enterprise.

---

## DG-006

Menyediakan struktur data yang mudah dipahami oleh AI Studio sehingga proses generasi kode menjadi konsisten.

---

## DG-007

Menjaga performa aplikasi pada perangkat Android kelas menengah hingga kelas bawah tanpa mengorbankan skalabilitas.

---

# 3. Database Design Principles

Seluruh desain database BusinessOS wajib mengikuti prinsip berikut.

## DP-001 Business Model First

Model bisnis harus dirancang terlebih dahulu.

Implementasi database mengikuti model bisnis, bukan sebaliknya.

---

## DP-002 Single Source of Truth

Setiap informasi hanya memiliki satu sumber kebenaran.

Contoh:

- Role tidak disimpan pada User.
- Manager tidak disimpan pada Branch.
- Owner tidak disimpan pada Organization.

Seluruh hak akses ditentukan melalui Role Assignment.

---

## DP-003 Offline First

Seluruh entity bisnis harus dapat dibuat, diubah, dan digunakan tanpa koneksi internet.

Sinkronisasi dilakukan ketika koneksi tersedia.

---

## DP-004 Synchronization Ready

Seluruh entity harus mendukung mekanisme sinkronisasi data.

Setiap perubahan harus dapat dilacak melalui metadata sinkronisasi.

---

## DP-005 Auditability

Seluruh perubahan penting harus dapat ditelusuri.

Minimal mencatat:

- siapa yang membuat,
- siapa yang mengubah,
- kapan dibuat,
- kapan diubah.

---

## DP-006 Soft Delete

Data tidak langsung dihapus.

Data ditandai sebagai terhapus terlebih dahulu agar aman terhadap sinkronisasi, audit, dan pemulihan data.

---

## DP-007 Scalability

Seluruh model data harus mampu berkembang dari:

- Single User
- UMKM
- Multi Branch
- Company
- Enterprise

tanpa perubahan struktur fundamental.

---

## DP-008 Performance First

Seluruh model data harus dirancang untuk meminimalkan:

- Query yang berat.
- Relasi yang kompleks.
- Penggunaan memori yang berlebihan.
- Beban sinkronisasi yang tidak diperlukan.

---

## DP-009 AI Readability

Model data harus mudah dipahami oleh AI Studio maupun software engineer.

Nama entity, field, relasi, dan aturan bisnis harus eksplisit dan konsisten.

---

# 4. Entity Classification

Untuk menjaga batas tanggung jawab setiap modul, seluruh entity dikelompokkan menjadi tiga kategori utama.

## 4.1 Core Identity Entities

Entity yang mengatur identitas pengguna, organisasi, serta otorisasi sistem.

Meliputi:

- Organization
- Branch
- User
- Role
- RoleAssignment
- Permission

---

## 4.2 Business Entities

Entity yang merepresentasikan proses bisnis utama.

Contoh:

- Account
- Category
- Transaction
- Journal
- JournalEntry
- Ledger
- Product
- Inventory
- Customer
- Supplier
- Asset

Daftar entity bisnis akan berkembang sesuai modul BusinessOS.

---

## 4.3 System Entities

Entity yang mendukung operasional sistem.

Contoh:

- Attachment
- Notification
- AuditLog
- Settings
- SyncQueue
- Device
- FeatureFlag

Entity sistem tidak merepresentasikan proses bisnis secara langsung tetapi mendukung keamanan, performa, sinkronisasi, serta operasional aplikasi.

---

# 5. Base Entity Specification

Seluruh entity BusinessOS mewarisi struktur dasar berikut.

Beberapa field bersifat kontekstual dan hanya digunakan apabila relevan terhadap entity yang bersangkutan.

| Field | Type | Required | Description |
|--------|------|----------|-------------|
| id | UUID | Yes | Primary Key |
| organizationId | UUID | Contextual | Ruang lingkup organisasi |
| branchId | UUID | Contextual | Ruang lingkup cabang |
| createdAt | DateTime | Yes | Waktu pembuatan data |
| updatedAt | DateTime | Yes | Waktu perubahan terakhir |
| createdBy | UUID | Yes | User pembuat |
| updatedBy | UUID | Yes | User terakhir yang mengubah |
| version | Integer | Yes | Versi data untuk sinkronisasi |
| syncStatus | Enum | Yes | Status sinkronisasi |
| deletedAt | DateTime | Optional | Waktu soft delete |
| isDeleted | Boolean | Yes | Penanda soft delete |

## Base Entity Rules

### BE-001

Seluruh entity menggunakan UUID sebagai Primary Key.

---

### BE-002

Seluruh entity wajib memiliki metadata audit.

---

### BE-003

Seluruh entity wajib mendukung versioning.

---

### BE-004

Seluruh entity wajib mendukung sinkronisasi.

---

### BE-005

Seluruh entity wajib mendukung soft delete apabila menyimpan data bisnis.

---

### BE-006

Field `organizationId` dan `branchId` digunakan sebagai data scope dan hanya diterapkan pada entity yang memang memiliki kepemilikan organisasi atau cabang.

---

### BE-007

Tidak diperbolehkan membuat entity baru yang mengabaikan Base Entity tanpa Architecture Decision Record (ADR).

---

# 6. Foundation Overview

BusinessOS dibangun di atas enam entity fondasi yang menjadi dasar seluruh modul.

1. Organization
2. Branch
3. User
4. Role
5. RoleAssignment
6. Permission

Seluruh entity bisnis dan entity sistem harus bergantung pada fondasi ini secara langsung maupun tidak langsung.

---
# 7. Core Identity Entities

Core Identity Entities merupakan fondasi seluruh sistem BusinessOS.

Entity pada bagian ini mengatur identitas organisasi, cabang, pengguna, serta mekanisme otorisasi.

Entity ini harus tersedia sebelum modul bisnis lainnya dibangun.

---

## 7.1 Organization

### Purpose

Organization merepresentasikan satu entitas bisnis yang menggunakan BusinessOS.

Organization dapat berupa:

- UMKM
- Toko
- CV
- PT
- Yayasan
- Koperasi
- Badan usaha lainnya

Seluruh data bisnis selalu berada dalam ruang lingkup satu Organization.

---

### Responsibilities

Organization bertanggung jawab terhadap:

- kepemilikan data bisnis,
- konfigurasi organisasi,
- pengaturan mata uang,
- zona waktu,
- bahasa,
- identitas perusahaan,
- ruang lingkup seluruh Branch.

---

### Relationships

```text
Organization
├── Branch
├── RoleAssignment
├── Account
├── Category
├── Transaction
├── Journal
├── Ledger
├── Inventory
├── Product
├── Customer
├── Supplier
├── Asset
└── Settings
```

---

### Core Fields

| Field | Type | Required | Description |
|--------|------|----------|-------------|
| id | UUID | Yes | Primary Key |
| businessName | String | Yes | Nama usaha |
| legalName | String | Optional | Nama badan hukum |
| businessType | Enum | Yes | Jenis usaha |
| taxNumber | String | Optional | NPWP atau identitas pajak |
| phone | String | Optional | Nomor telepon |
| email | String | Optional | Email organisasi |
| website | String | Optional | Website |
| logoUrl | String | Optional | Logo |
| timezone | String | Yes | Zona waktu |
| currency | String | Yes | Mata uang utama |
| language | String | Yes | Bahasa default |
| status | Enum | Yes | Status organisasi |

---

### Validation Rules

- businessName wajib diisi.
- currency menggunakan standar ISO 4217.
- timezone menggunakan standar IANA Time Zone.
- language menggunakan standar ISO Language Code.

---

### Business Rules

- Minimal memiliki satu Branch aktif.
- Minimal memiliki satu RoleAssignment dengan Role Owner.
- Tidak dapat dihapus apabila masih memiliki data bisnis aktif.

---

### Security Rules

- Hanya Owner yang dapat mengubah konfigurasi Organization.
- Seluruh data Organization harus terisolasi dari Organization lain.

---

### Synchronization Rules

- Seluruh perubahan wajib disinkronkan ke cloud.
- Konflik menggunakan versioning.

---

### Future Considerations

Siap mendukung Workspace dan Holding Company pada roadmap berikutnya.

---

## 7.2 Branch

### Purpose

Branch merepresentasikan lokasi operasional suatu Organization.

---

### Responsibilities

Branch bertanggung jawab terhadap:

- transaksi operasional,
- kas,
- stok,
- pegawai,
- laporan per cabang.

---

### Relationships

```text
Organization
    │
    ▼
 Branch
    ├── Transaction
    ├── Inventory
    ├── Employee Activity
    ├── Cash Session
    └── Report
```

---

### Core Fields

| Field | Type | Required |
|--------|------|----------|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| branchName | String | Yes |
| branchCode | String | Yes |
| address | String | Optional |
| phone | String | Optional |
| email | String | Optional |
| latitude | Double | Optional |
| longitude | Double | Optional |
| status | Enum | Yes |

---

### Validation Rules

- branchCode harus unik dalam satu Organization.

---

### Business Rules

- Wajib dimiliki satu Organization.
- Tidak menyimpan Manager secara langsung.
- Manager ditentukan melalui RoleAssignment.

---

### Security Rules

Hak akses cabang ditentukan melalui RoleAssignment.

---

### Synchronization Rules

Perubahan Branch harus tersinkron ke seluruh pengguna yang memiliki akses.

---

## 7.3 User

### Purpose

User merepresentasikan identitas pengguna BusinessOS.

User hanya menyimpan identitas.

Hak akses tidak disimpan pada User.

---

### Responsibilities

- Identitas pengguna.
- Profil pengguna.
- Informasi autentikasi.

---

### Relationships

```text
User
└── RoleAssignment
```

---

### Core Fields

| Field | Type | Required |
|--------|------|----------|
| id | UUID | Yes |
| firebaseUid | String | Yes |
| fullName | String | Yes |
| phoneNumber | String | Yes |
| email | String | Optional |
| avatarUrl | String | Optional |
| status | Enum | Yes |

---

### Validation Rules

- firebaseUid harus unik.
- phoneNumber menggunakan format internasional.

---

### Business Rules

- User dapat menjadi anggota beberapa Organization (roadmap).
- User dapat memiliki lebih dari satu RoleAssignment.

---

### Security Rules

User tidak menyimpan Role secara langsung.

---

### Synchronization Rules

Perubahan profil disinkronkan ke seluruh perangkat pengguna.

---

## 7.4 Role

### Purpose

Role merupakan kumpulan Permission.

---

### Responsibilities

Mengelompokkan hak akses.

---

### Core Fields

| Field | Type |
|--------|------|
| id | UUID |
| roleCode | String |
| roleName | String |
| description | String |
| isSystemRole | Boolean |
| status | Enum |

---

### Default Roles

- Owner
- Manager
- Cashier
- Employee

Role tambahan dapat dibuat sesuai kebutuhan.

---

### Business Rules

Role tidak diberikan langsung kepada User.

Role diberikan melalui RoleAssignment.

---

## 7.5 RoleAssignment

### Purpose

RoleAssignment menghubungkan User dengan Role pada Organization atau Branch tertentu.

RoleAssignment merupakan sumber kebenaran seluruh proses otorisasi.

---

### Relationships

```text
User
   │
   ▼
RoleAssignment
   │
   ├── Organization
   ├── Branch
   └── Role
```

---

### Core Fields

| Field | Type |
|--------|------|
| id | UUID |
| userId | UUID |
| roleId | UUID |
| organizationId | UUID |
| branchId | UUID (Optional) |
| validFrom | DateTime |
| validUntil | DateTime |
| status | Enum |

---

### Business Rules

- Satu User dapat memiliki banyak RoleAssignment.
- Satu User dapat memiliki Role berbeda pada Branch berbeda.
- RoleAssignment dapat dinonaktifkan tanpa menghapus User.

---

### Security Rules

Seluruh authorization wajib menggunakan RoleAssignment.

---

## 7.6 Permission

### Purpose

Permission mendefinisikan hak akses granular terhadap fitur BusinessOS.

---

### Core Fields

| Field | Type |
|--------|------|
| id | UUID |
| permissionCode | String |
| permissionName | String |
| module | String |
| description | String |

---

### Examples

- finance.read
- finance.create
- finance.update
- finance.delete
- inventory.read
- inventory.update
- report.export
- user.manage
- settings.manage

---

### Business Rules

Permission diberikan kepada Role.

Role diberikan kepada User melalui RoleAssignment.

Permission tidak pernah diberikan langsung kepada User.

---

# 8. Core Identity Relationship Diagram

```text
Organization
      │
      ├──────────────┐
      ▼              ▼
   Branch          Role
      │              │
      └──────┐       │
             ▼       ▼
       RoleAssignment
             │
             ▼
           User

Role
 │
 ▼
Permission
```

---# 9. Finance Core Entities

Finance Core merupakan inti sistem pencatatan keuangan BusinessOS.

Seluruh transaksi bisnis pada akhirnya akan diproses menjadi pencatatan akuntansi melalui Accounting Engine.

BusinessOS memisahkan secara tegas antara transaksi bisnis yang dilakukan pengguna dengan pencatatan akuntansi internal sistem.

```text
Business Transaction
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
```

Pengguna hanya berinteraksi dengan Business Transaction.

Accounting Engine bertanggung jawab menerjemahkan transaksi tersebut menjadi pencatatan akuntansi yang benar.

---

# 9.1 Chart Of Account (COA)

## Purpose

Chart of Account merupakan daftar seluruh akun akuntansi yang digunakan BusinessOS.

Seluruh proses akuntansi wajib menggunakan akun yang terdaftar pada COA.

---

## Responsibilities

Chart of Account bertanggung jawab terhadap:

- struktur akun,
- klasifikasi akun,
- normal balance,
- hirarki akun,
- posting transaksi.

---

## Relationships

```text
ChartOfAccount

├── JournalEntry

├── Ledger

└── Financial Report
```

---

## Core Fields

| Field | Type | Required | Description |
|--------|------|----------|-------------|
| id | UUID | Yes | Primary Key |
| organizationId | UUID | Yes | Organization Owner |
| accountCode | String | Yes | Kode akun |
| accountName | String | Yes | Nama akun |
| accountType | Enum | Yes | Asset, Liability, Equity, Revenue, Expense |
| parentAccountId | UUID | Optional | Parent Account |
| normalBalance | Enum | Yes | Debit / Credit |
| isSystemAccount | Boolean | Yes | Tidak dapat dihapus |
| allowManualPosting | Boolean | Yes | Mengizinkan jurnal manual |
| status | Enum | Yes | Active / Inactive |

---

## Validation Rules

- accountCode wajib unik dalam satu Organization.
- parentAccountId tidak boleh membentuk circular reference.
- accountType wajib sesuai standar akuntansi.

---

## Business Rules

- Seluruh JournalEntry harus mengacu pada ChartOfAccount.
- System Account tidak dapat dihapus.
- Account dapat dinonaktifkan tetapi tidak boleh dihapus apabila telah digunakan transaksi.

---

## Synchronization Rules

Perubahan COA wajib disinkronkan ke seluruh perangkat Organization.

---

# 9.2 Category

## Purpose

Category merupakan klasifikasi bisnis yang digunakan pengguna saat melakukan transaksi.

Category tidak menggantikan ChartOfAccount.

Category bertujuan mempermudah penggunaan aplikasi.

Accounting Engine akan memetakan Category menjadi akun akuntansi yang sesuai.

---

## Relationships

```text
Category

├── Transaction

└── ChartOfAccount
```

---

## Core Fields

| Field | Type | Required |
|--------|------|----------|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| categoryCode | String | Yes |
| categoryName | String | Yes |
| transactionType | Enum | Yes |
| defaultAccountId | UUID | Yes |
| color | String | Optional |
| icon | String | Optional |
| status | Enum | Yes |

---

## Business Rules

- Setiap Category harus memiliki satu default ChartOfAccount.
- Category dapat digunakan ulang pada banyak transaksi.
- Category dapat dinonaktifkan tanpa menghapus histori transaksi.

---

# 9.3 Business Transaction

## Purpose

Business Transaction merepresentasikan aktivitas bisnis yang dilakukan pengguna.

Business Transaction merupakan satu-satunya entity yang dibuat secara langsung melalui antarmuka aplikasi.

Business Transaction bukan Journal dan bukan Ledger.

---

## Responsibilities

Mencatat aktivitas bisnis seperti:

- pemasukan,
- pengeluaran,
- transfer,
- hutang,
- piutang,
- penyesuaian,
- transaksi lainnya.

---

## Relationships

```text
Category
     │
     ▼
BusinessTransaction
     │
     ▼
Accounting Engine
```

---

## Core Fields

| Field | Type | Required |
|--------|------|----------|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| branchId | UUID | Yes |
| transactionNumber | String | Yes |
| transactionDate | DateTime | Yes |
| transactionType | Enum | Yes |
| categoryId | UUID | Yes |
| amount | Decimal | Yes |
| description | String | Optional |
| attachmentCount | Integer | Yes |
| status | Enum | Yes |

---

## Validation Rules

- amount harus lebih besar dari nol.
- categoryId wajib aktif.
- transactionDate tidak boleh kosong.

---

## Business Rules

- Business Transaction tidak menyimpan Debit dan Credit.
- Business Transaction tidak menyimpan Journal Entry.
- Business Transaction menjadi input utama Accounting Engine.

---

## Security Rules

Pengguna hanya dapat mengakses transaksi sesuai RoleAssignment.

---

## Synchronization Rules

Sinkronisasi dilakukan berdasarkan version dan syncStatus.

---

# 9.4 Accounting Engine

## Purpose

Accounting Engine merupakan lapisan domain yang menerjemahkan Business Transaction menjadi pencatatan akuntansi.

Accounting Engine bukan database entity.

Accounting Engine merupakan kontrak bisnis yang wajib diimplementasikan oleh Domain Layer.

---

## Responsibilities

- menghasilkan Journal,
- menghasilkan Journal Entry,
- menjaga keseimbangan Debit dan Credit,
- melakukan validasi akuntansi,
- melakukan posting ke Ledger.

---

## Business Rules

- Seluruh Journal dibuat melalui Accounting Engine.
- UI tidak boleh membuat Journal secara langsung.
- Repository tidak boleh membuat Journal secara langsung.
- Seluruh proses akuntansi harus melewati Accounting Engine.

---

# Architecture Decision

Business Layer tidak boleh melakukan manipulasi Ledger secara langsung.

Seluruh pencatatan akuntansi wajib melalui Accounting Engine.

---# 9.5 Journal

## Purpose

Journal merupakan dokumen akuntansi yang dihasilkan oleh Accounting Engine sebagai representasi resmi dari suatu BusinessTransaction.

Journal menjadi sumber pencatatan akuntansi sebelum diposting ke General Ledger.

Journal tidak dibuat secara langsung oleh pengguna maupun antarmuka aplikasi.

---

## Responsibilities

Journal bertanggung jawab untuk:

- merepresentasikan transaksi akuntansi,
- mengelompokkan Journal Entry,
- menjaga integritas transaksi akuntansi,
- menjadi dasar proses posting ke General Ledger.

---

## Relationships

```text
BusinessTransaction
        │
        ▼
     Journal
        │
        ▼
   JournalEntry
```

---

## Core Fields

| Field | Type | Required | Description |
|--------|------|----------|-------------|
| id | UUID | Yes | Primary Key |
| organizationId | UUID | Yes | Organization Owner |
| businessTransactionId | UUID | Yes | Source Transaction |
| journalNumber | String | Yes | Nomor jurnal |
| journalDate | DateTime | Yes | Tanggal jurnal |
| description | String | Optional | Keterangan jurnal |
| postingStatus | Enum | Yes | Draft, Posted, Reversed |
| postedAt | DateTime | Optional | Waktu posting |
| postedBy | UUID | Optional | User yang melakukan posting |

---

## Validation Rules

- Journal wajib memiliki minimal dua Journal Entry.
- Total Debit harus sama dengan total Credit.
- businessTransactionId wajib mengacu pada BusinessTransaction yang valid.

---

## Business Rules

- Journal dibuat oleh Accounting Engine.
- Journal tidak dapat diubah setelah diposting.
- Koreksi dilakukan melalui Reverse Journal.
- Journal Number harus unik dalam satu Organization.

---

## Synchronization Rules

- Journal hanya disinkronkan setelah validasi berhasil.
- Journal yang telah diposting tidak boleh diubah selama proses sinkronisasi.

---

## Lifecycle

```text
Draft
   │
   ▼
Posted
   │
   ▼
Reversed
```

---

# 9.6 Journal Entry

## Purpose

Journal Entry merupakan detail debit dan kredit dari sebuah Journal.

Setiap Journal terdiri dari minimal dua Journal Entry.

---

## Responsibilities

Journal Entry bertanggung jawab untuk:

- menyimpan nilai debit,
- menyimpan nilai kredit,
- menghubungkan transaksi dengan ChartOfAccount,
- menjaga keseimbangan akuntansi.

---

## Relationships

```text
Journal
    │
    ▼
JournalEntry
    │
    ▼
ChartOfAccount
```

---

## Core Fields

| Field | Type | Required | Description |
|--------|------|----------|-------------|
| id | UUID | Yes | Primary Key |
| journalId | UUID | Yes | Parent Journal |
| accountId | UUID | Yes | Chart Of Account |
| debit | Decimal | Yes | Nilai Debit |
| credit | Decimal | Yes | Nilai Credit |
| description | String | Optional | Keterangan |

---

## Validation Rules

- Salah satu dari debit atau credit harus bernilai lebih besar dari nol.
- Debit dan credit tidak boleh bernilai positif secara bersamaan.
- accountId wajib mengacu pada ChartOfAccount aktif.

---

## Business Rules

- Total Debit seluruh Journal Entry harus sama dengan total Credit.
- Journal Entry tidak dapat berdiri sendiri.
- Journal Entry mengikuti status Journal.

---

## Synchronization Rules

Journal Entry selalu mengikuti proses sinkronisasi Journal.

---

# 9.7 General Ledger

## Purpose

General Ledger merupakan hasil posting seluruh Journal Entry.

General Ledger digunakan sebagai sumber utama laporan keuangan.

General Ledger bukan merupakan entity yang dapat dimanipulasi secara langsung.

---

## Responsibilities

General Ledger bertanggung jawab terhadap:

- saldo akun,
- histori akun,
- dasar laporan keuangan,
- audit akuntansi.

---

## Relationships

```text
JournalEntry
      │
      ▼
GeneralLedger
      │
      ├── Trial Balance
      ├── Profit & Loss
      ├── Balance Sheet
      └── Cash Flow
```

---

## Core Fields

| Field | Type | Required | Description |
|--------|------|----------|-------------|
| id | UUID | Yes | Primary Key |
| organizationId | UUID | Yes | Organization Owner |
| accountId | UUID | Yes | Chart Of Account |
| journalEntryId | UUID | Yes | Source Journal Entry |
| postingDate | DateTime | Yes | Tanggal Posting |
| debit | Decimal | Yes | Nilai Debit |
| credit | Decimal | Yes | Nilai Credit |
| runningBalance | Decimal | Yes | Saldo Berjalan |

---

## Validation Rules

- Ledger hanya dapat dibuat melalui proses posting.
- journalEntryId wajib valid.
- accountId wajib valid.

---

## Business Rules

- Ledger bersifat read-only.
- Ledger tidak dapat diedit.
- Ledger tidak dapat dihapus.
- Koreksi dilakukan melalui Journal baru.

---

## Synchronization Rules

Ledger mengikuti proses sinkronisasi Journal.

---

# 9.8 Finance Relationship Model

```text
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
  Chart Of Account
          │
          ▼
   General Ledger
          │
          ├─────────────┐
          ▼             ▼
   Financial Report   Dashboard
```

---

# 9.9 Accounting Rules

## AR-001

Seluruh transaksi bisnis wajib menghasilkan Journal.

---

## AR-002

Seluruh Journal wajib seimbang.

```text
Total Debit = Total Credit
```

---

## AR-003

Seluruh Journal diposting ke General Ledger.

---

## AR-004

General Ledger tidak boleh dimodifikasi secara langsung.

---

## AR-005

Laporan keuangan selalu dihasilkan dari General Ledger.

---

## AR-006

BusinessTransaction tidak menyimpan informasi debit maupun credit.

---

## AR-007

Accounting Engine merupakan satu-satunya komponen yang diperbolehkan menghasilkan Journal.

---# 10. Supporting System Entities

Supporting System Entities merupakan entity yang mendukung operasional sistem BusinessOS.

Entity pada bagian ini tidak secara langsung merepresentasikan proses bisnis, namun berperan penting dalam keamanan, audit, sinkronisasi, konfigurasi, dan pengalaman pengguna.

---

## 10.1 Attachment

### Purpose

Menyimpan referensi dokumen atau media yang berhubungan dengan entity bisnis.

Contoh:

- Foto nota
- Invoice
- Bukti transfer
- Dokumen PDF
- Gambar produk

### Business Rules

- Attachment dapat dimiliki oleh berbagai entity melalui mekanisme polymorphic reference.
- File disimpan pada Cloud Storage.
- Database hanya menyimpan metadata file.

---

## 10.2 AuditLog

### Purpose

Mencatat seluruh aktivitas penting yang dilakukan pengguna maupun sistem.

### Contoh Aktivitas

- Login
- Logout
- Membuat transaksi
- Mengubah transaksi
- Menghapus transaksi
- Sinkronisasi
- Perubahan konfigurasi

### Business Rules

- AuditLog bersifat append-only.
- AuditLog tidak boleh diubah.
- AuditLog hanya dapat diarsipkan sesuai kebijakan retensi data.

---

## 10.3 Notification

### Purpose

Menyimpan notifikasi yang dikirimkan kepada pengguna.

### Contoh

- Sinkronisasi berhasil
- Sinkronisasi gagal
- Approval diperlukan
- Reminder pembayaran
- Peringatan stok minimum

---

## 10.4 Settings

### Purpose

Menyimpan konfigurasi aplikasi pada berbagai ruang lingkup.

### Scope

- System
- Organization
- Branch
- User

### Business Rules

- Konfigurasi mengikuti hierarki prioritas.
- Konfigurasi User mengesampingkan Branch.
- Konfigurasi Branch mengesampingkan Organization.
- Konfigurasi Organization mengesampingkan System.

---

## 10.5 SyncQueue

### Purpose

Mengelola antrean sinkronisasi Offline-First.

### Responsibilities

- Menyimpan perubahan lokal.
- Mengatur urutan sinkronisasi.
- Menangani retry.
- Menangani konflik sinkronisasi.

### Business Rules

- Sinkronisasi menggunakan mekanisme FIFO.
- Retry dilakukan secara otomatis dengan exponential backoff.
- Konflik diselesaikan oleh Synchronization Engine.

---

# 11. Relationship Model

Relationship antar entity mengikuti prinsip berikut.

## RM-001

Organization merupakan pemilik seluruh data bisnis.

---

## RM-002

Branch merupakan ruang lingkup operasional.

---

## RM-003

User tidak memiliki Role secara langsung.

Hak akses diberikan melalui RoleAssignment.

---

## RM-004

BusinessTransaction merupakan sumber seluruh aktivitas bisnis.

---

## RM-005

AccountingEngine menghasilkan Journal.

---

## RM-006

Journal menghasilkan JournalEntry.

---

## RM-007

JournalEntry diposting ke GeneralLedger.

---

## RM-008

Financial Report selalu dihasilkan dari GeneralLedger.

---

## Overall Relationship

```text
Organization
│
├── Branch
│
├── Role
│
├── User
│
│     │
│     ▼
│ RoleAssignment
│
├──────────────┐
│              ▼
│     BusinessTransaction
│              │
│              ▼
│      AccountingEngine
│              │
│              ▼
│          Journal
│              │
│              ▼
│        JournalEntry
│              │
│              ▼
│      ChartOfAccount
│              │
│              ▼
│      GeneralLedger
│              │
│              ▼
│      FinancialReport
│
└── Settings
```

---

# 12. Offline & Synchronization Strategy

BusinessOS menggunakan pendekatan Offline-First.

Seluruh proses bisnis harus dapat berjalan tanpa koneksi internet.

## Synchronization Principles

### SYNC-001

Perubahan selalu disimpan ke Local Database terlebih dahulu.

---

### SYNC-002

Sinkronisasi dilakukan secara asynchronous.

---

### SYNC-003

Cloud Firestore merupakan sumber sinkronisasi antar perangkat, bukan sumber operasi utama.

---

### SYNC-004

Seluruh entity menggunakan versioning.

---

### SYNC-005

Conflict Resolution dilakukan oleh Synchronization Engine.

---

### SYNC-006

Sinkronisasi bersifat incremental.

---

### SYNC-007

Pengguna tetap dapat bekerja ketika internet tidak tersedia.

---

# 13. Indexing Strategy

Untuk menjaga performa aplikasi, setiap entity wajib memiliki indeks sesuai kebutuhan query.

## Mandatory Index

- id
- organizationId
- branchId
- createdAt
- updatedAt
- syncStatus

## Conditional Index

Ditambahkan berdasarkan kebutuhan modul dan pola query.

Seluruh indeks harus mempertimbangkan performa Local Database maupun Cloud Firestore.

---

# 14. Naming Convention

Seluruh penamaan entity, field, dan relasi wajib mengikuti konvensi berikut.

## Entity

PascalCase

Contoh:

- BusinessTransaction
- ChartOfAccount
- RoleAssignment

---

## Field

camelCase

Contoh:

- organizationId
- createdAt
- accountCode

---

## Enum

PascalCase

Contoh:

- TransactionType
- SyncStatus
- OrganizationStatus

---

## Collection

Plural

Contoh:

- organizations
- transactions
- journalEntries

---

## Document ID

UUID Version 4.

---

# 15. References

Dokumen ini mengacu pada:

- AICTX-001 Project Context
- AICTX-002 Product Requirements
- AICTX-003 System Architecture
- AICTX-004 Tech Stack

Seluruh perubahan terhadap model data wajib mempertimbangkan konsistensi dengan dokumen-dokumen tersebut.

---

# 16. Revision History

| Version | Date | Description |
|----------|------|-------------|
| 1.0.0 | Initial Draft | Initial Database Design |
| 1.1.0 | Current | Enterprise Architecture Revision |

---

# 17. Approval

Status dokumen mengikuti tahapan berikut.

Draft

↓

Review

↓

Architecture Review

↓

Locked

Dokumen hanya dapat dinyatakan **Locked** setelah seluruh Architecture Decision Record (ADR) yang berkaitan telah disetujui.

---

# End of Document