---
document:
  id: AICTX-006
  title: Authentication and RBAC
  version: 1.0.0
  status: Locked
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan arsitektur autentikasi, otorisasi, serta Role-Based Access Control (RBAC) sebagai fondasi keamanan BusinessOS. Dokumen ini menjadi acuan implementasi Firebase Authentication, Authorization Service, dan pengelolaan hak akses pada seluruh modul BusinessOS.

dependencies:
  - 001_PROJECT_CONTEXT.md
  - 002_PRODUCT_REQUIREMENTS.md
  - 003_SYSTEM_ARCHITECTURE.md
  - 004_TECH_STACK.md
  - 005_DATABASE_DESIGN.md

next_documents:
  - 007_FINANCE_MODULE.md
---

# 1. Executive Summary

BusinessOS menggunakan pendekatan Authentication dan Role-Based Access Control (RBAC) yang terpisah secara jelas.

Authentication bertanggung jawab memverifikasi identitas pengguna.

Authorization bertanggung jawab menentukan hak akses pengguna terhadap data dan fitur aplikasi.

Hak akses tidak disimpan pada User, melainkan dihitung melalui RoleAssignment yang menghubungkan User, Role, Organization, dan Branch.

Pendekatan ini memungkinkan BusinessOS mendukung organisasi multi-cabang, multi-peran, serta siap berkembang menuju kebutuhan enterprise tanpa mengubah arsitektur keamanan.

---

# 2. Security Goals

## SG-001

Seluruh pengguna wajib terautentikasi sebelum mengakses data bisnis.

---

## SG-002

Seluruh akses data harus melewati proses authorization.

---

## SG-003

Hak akses ditentukan berdasarkan RoleAssignment.

---

## SG-004

Data antar Organization harus sepenuhnya terisolasi.

---

## SG-005

Pengguna hanya dapat mengakses Branch sesuai ruang lingkup RoleAssignment.

---

## SG-006

Seluruh aktivitas keamanan harus dapat diaudit.

---

## SG-007

Sistem harus tetap mendukung penggunaan Offline-First tanpa mengorbankan keamanan.

---

# 3. Authentication Principles

## AP-001 Identity and Permission Separation

Authentication hanya membuktikan identitas pengguna.

Authentication tidak menentukan hak akses.

---

## AP-002 Firebase Authentication

Seluruh identitas pengguna menggunakan Firebase Authentication sebagai Identity Provider.

BusinessOS hanya menyimpan referensi firebaseUid pada entity User.

---

## AP-003 Offline Session

Setelah login berhasil, pengguna dapat tetap menggunakan aplikasi secara offline sesuai kebijakan sesi yang masih berlaku.

---

## AP-004 Secure Token

Aplikasi tidak menyimpan password pengguna.

Seluruh proses autentikasi menggunakan mekanisme token yang disediakan Firebase Authentication.

---

## AP-005 Single Identity

Satu pengguna hanya memiliki satu identitas digital.

Hak akses terhadap banyak Organization maupun Branch dikelola melalui RoleAssignment, bukan melalui akun yang berbeda.

---

# 4. Authentication Flow

Authentication terdiri dari beberapa tahapan.

```text
Application
      │
      ▼
Firebase Authentication
      │
      ▼
Identity Verified
      │
      ▼
Load User Profile
      │
      ▼
Load RoleAssignment
      │
      ▼
Authorization Service
      │
      ▼
BusinessOS Session
```

Authentication dinyatakan berhasil apabila:

- Firebase Authentication berhasil memverifikasi identitas.
- User ditemukan pada BusinessOS.
- Minimal terdapat satu RoleAssignment aktif.

---

# 5. Login Methods

BusinessOS mendukung metode login berikut.

## Primary

- Nomor telepon (OTP)

## Secondary

- Email dan Password

## Future Roadmap

- Google Sign-In
- Apple Sign-In
- Microsoft Entra ID
- Single Sign-On (SSO)

Seluruh metode login harus menghasilkan identitas pengguna yang sama melalui firebaseUid.

---# 6. Authorization Architecture

Authentication dan Authorization merupakan dua proses yang berbeda.

Authentication hanya memverifikasi identitas pengguna.

Authorization menentukan tindakan yang diperbolehkan terhadap sumber daya BusinessOS.

BusinessOS menggunakan pendekatan Role-Based Access Control (RBAC) yang dikombinasikan dengan ruang lingkup Organization dan Branch.

```text
User
   │
   ▼
RoleAssignment
   │
   ▼
Role
   │
   ▼
Permission
```

Authorization tidak pernah membaca Role langsung dari User.

Seluruh evaluasi hak akses dilakukan melalui RoleAssignment.

---

# 7. RBAC Principles

## RBAC-001

User tidak menyimpan Role.

---

## RBAC-002

Role hanya merupakan kumpulan Permission.

---

## RBAC-003

Permission tidak pernah diberikan langsung kepada User.

---

## RBAC-004

Seluruh hak akses diberikan melalui RoleAssignment.

---

## RBAC-005

Satu User dapat memiliki lebih dari satu RoleAssignment.

---

## RBAC-006

RoleAssignment dapat berlaku pada tingkat Organization maupun Branch.

---

## RBAC-007

Authorization dilakukan setiap kali User mengakses fitur atau data.

---

## RBAC-008

Hak akses selalu mengikuti prinsip Least Privilege.

Pengguna hanya memperoleh hak minimum yang diperlukan untuk menjalankan pekerjaannya.

---

# 8. Authorization Service

## Purpose

Authorization Service merupakan Domain Service yang bertanggung jawab mengevaluasi seluruh hak akses pengguna.

Authorization Service bukan Entity dan bukan Repository.

---

## Responsibilities

Authorization Service bertanggung jawab untuk:

- memuat RoleAssignment aktif,
- memuat Role,
- memuat Permission,
- menentukan akses Organization,
- menentukan akses Branch,
- mengevaluasi izin terhadap fitur,
- mengevaluasi izin terhadap data.

---

## Authorization Flow

```text
Authenticated User
        │
        ▼
Load User
        │
        ▼
Load Active RoleAssignments
        │
        ▼
Load Roles
        │
        ▼
Load Permissions
        │
        ▼
Evaluate Access Scope
        │
        ▼
Grant / Deny
```

Authorization dilakukan pada setiap request yang membutuhkan akses terhadap data maupun fitur.

---

# 9. Role

Role merupakan kumpulan Permission.

Role tidak menentukan siapa yang memperoleh hak akses.

Role hanya mendefinisikan kemampuan yang dimiliki.

## Default Roles

- Owner
- Manager
- Cashier
- Employee

BusinessOS mendukung penambahan Role baru sesuai kebutuhan Organization.

---

## Default Capabilities

### Owner

- Mengelola seluruh modul.
- Mengelola pengguna.
- Mengelola konfigurasi organisasi.
- Mengakses seluruh laporan.

---

### Manager

- Mengelola operasional cabang.
- Mengelola transaksi.
- Melihat laporan sesuai ruang lingkup.

---

### Cashier

- Membuat transaksi penjualan.
- Menerima pembayaran.
- Melihat transaksi miliknya.

---

### Employee

- Menggunakan fitur sesuai penugasan.
- Tidak memiliki hak administrasi.

---

# 10. Permission

Permission merupakan hak akses granular terhadap fitur maupun aksi tertentu.

Format penamaan menggunakan pola:

```text
module.action
```

Contoh:

```text
finance.read
finance.create
finance.update
finance.delete

inventory.read
inventory.update

sales.create
sales.cancel

report.export

settings.manage

user.manage
```

Permission harus bersifat eksplisit dan tidak ambigu.

---

# 11. Role Assignment

RoleAssignment merupakan pusat seluruh mekanisme authorization BusinessOS.

RoleAssignment menghubungkan:

- User
- Role
- Organization
- Branch

dalam satu relasi yang utuh.

```text
User
   │
   ▼
RoleAssignment
   ├── Organization
   ├── Branch
   └── Role
```

---

## Business Rules

- Satu User dapat memiliki banyak RoleAssignment.
- Satu Role dapat digunakan oleh banyak User.
- RoleAssignment dapat dibatasi pada satu Branch.
- RoleAssignment dapat berlaku untuk seluruh Organization.
- RoleAssignment dapat memiliki tanggal mulai dan tanggal berakhir.
- RoleAssignment dapat dinonaktifkan tanpa menghapus User.

---

# 12. Access Scope

BusinessOS menggunakan dua tingkat ruang lingkup akses.

## Organization Scope

Hak akses berlaku untuk seluruh Organization.

Contoh:

Owner.

---

## Branch Scope

Hak akses hanya berlaku pada Branch tertentu.

Contoh:

Manager Cabang A.

Kasir Cabang B.

---

Authorization Service wajib memverifikasi ruang lingkup sebelum memberikan akses terhadap data.

---

# 13. Permission Evaluation

Setiap akses terhadap fitur mengikuti urutan evaluasi berikut.

```text
Authenticated

↓

User Active

↓

RoleAssignment Active

↓

Organization Match

↓

Branch Match

↓

Permission Exists

↓

Access Granted
```

Apabila salah satu langkah gagal maka akses ditolak.

---

# 14. Access Matrix

| Action | Owner | Manager | Cashier | Employee |
|---------|:----:|:-------:|:--------:|:---------:|
| Manage Organization | ✓ | ✗ | ✗ | ✗ |
| Manage Branch | ✓ | ✓* | ✗ | ✗ |
| Manage User | ✓ | ✗ | ✗ | ✗ |
| Create Transaction | ✓ | ✓ | ✓ | ✓* |
| Update Transaction | ✓ | ✓ | ✓* | ✗ |
| Delete Transaction | ✓ | ✓* | ✗ | ✗ |
| View Report | ✓ | ✓ | ✗ | ✗ |
| Export Report | ✓ | ✓* | ✗ | ✗ |
| Manage Settings | ✓ | ✗ | ✗ | ✗ |

\* Bergantung pada Permission yang diberikan.

---# 15. Session Management

BusinessOS menggunakan mekanisme session yang aman dan mendukung arsitektur Offline-First.

Session merepresentasikan pengguna yang telah berhasil melalui proses Authentication dan Authorization.

Session tidak menyimpan password maupun kredensial sensitif.

---

## Session Lifecycle

```text
Login
   │
   ▼
Authenticated
   │
   ▼
Load User
   │
   ▼
Load RoleAssignments
   │
   ▼
Create Local Session
   │
   ▼
Active Session
   │
   ▼
Logout / Session Expired
```

---

## Session Rules

### SESSION-001

Session hanya dapat dibuat setelah Authentication dan Authorization berhasil.

---

### SESSION-002

Session disimpan secara aman pada perangkat menggunakan secure storage.

---

### SESSION-003

Session harus dapat dipulihkan ketika aplikasi dibuka kembali tanpa memerlukan login ulang selama token masih valid.

---

### SESSION-004

Ketika token tidak lagi valid, aplikasi wajib meminta pengguna melakukan autentikasi ulang.

---

# 16. Offline Authentication

BusinessOS mendukung penggunaan aplikasi ketika perangkat tidak memiliki koneksi internet.

Offline Authentication hanya berlaku apabila pengguna sebelumnya telah berhasil login.

---

## Rules

### OFFLINE-001

Pengguna tidak dapat melakukan login pertama kali tanpa koneksi internet.

---

### OFFLINE-002

Apabila session masih valid, aplikasi dapat digunakan secara offline.

---

### OFFLINE-003

Authorization menggunakan RoleAssignment terakhir yang telah berhasil disinkronkan.

---

### OFFLINE-004

Perubahan hak akses baru akan diterapkan setelah proses sinkronisasi berikutnya.

---

# 17. Security Policies

Seluruh modul BusinessOS wajib mengikuti kebijakan keamanan berikut.

## SEC-001

Password tidak pernah disimpan oleh BusinessOS.

---

## SEC-002

Password hanya dikelola oleh Firebase Authentication.

---

## SEC-003

Setiap request terhadap data bisnis wajib melewati Authorization Service.

---

## SEC-004

Pengguna hanya dapat mengakses Organization yang dimilikinya melalui RoleAssignment.

---

## SEC-005

Pengguna hanya dapat mengakses Branch sesuai ruang lingkup RoleAssignment.

---

## SEC-006

Permission harus selalu dievaluasi sebelum menjalankan suatu aksi.

---

## SEC-007

Akses ditolak secara default (Default Deny).

Hak akses hanya diberikan apabila seluruh validasi berhasil.

---

# 18. Audit Requirements

Seluruh aktivitas keamanan harus dapat ditelusuri.

Minimal aktivitas berikut wajib dicatat:

- Login
- Logout
- Login gagal
- Perubahan RoleAssignment
- Perubahan Permission
- Perubahan Role
- Perubahan Organization
- Perubahan Branch
- Sinkronisasi data
- Perubahan pengaturan keamanan

Audit Log harus bersifat append-only dan tidak dapat dimodifikasi.

---

# 19. Future Roadmap

Arsitektur Authentication dan RBAC dirancang agar dapat diperluas tanpa perubahan fundamental.

Roadmap yang telah dipersiapkan:

- Multi Organization untuk satu User.
- Single Sign-On (SSO).
- Google Sign-In.
- Apple Sign-In.
- Microsoft Entra ID.
- Multi-Factor Authentication (MFA).
- Policy-Based Access Control (PBAC).
- Attribute-Based Access Control (ABAC).
- Delegated Administration.
- Temporary Access.
- API Access Token.
- Service Account.

Seluruh fitur di atas merupakan perluasan dari arsitektur yang telah ada dan tidak memerlukan perubahan pada model data utama.

---

# 20. References

Dokumen ini mengacu pada:

- AICTX-001 Project Context
- AICTX-002 Product Requirements
- AICTX-003 System Architecture
- AICTX-004 Tech Stack
- AICTX-005 Database Design

---

# 21. Revision History

| Version | Date | Description |
|----------|------|-------------|
| 1.0.0 | Initial Draft | Initial Authentication & RBAC Specification |

---

# 22. Approval

Status dokumen mengikuti tahapan berikut:

Draft

↓

Review

↓

Architecture Review

↓

Locked

Dokumen hanya dapat dinyatakan **Locked** setelah seluruh keputusan arsitektur terkait keamanan disetujui.

---

# End of Document