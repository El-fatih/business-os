---
document:
  id: AICTX-001
  title: Project Context
  version: 1.0.0
  status: Approved
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  This document is the primary source of truth for understanding the BusinessOS project.
  Every AI session must read this document before reading any other documentation.

related_documents:
  - 02_PRODUCT_REQUIREMENTS.md
  - 03_SYSTEM_ARCHITECTURE.md
  - 04_TECH_STACK.md
  - 05_DATABASE_DESIGN.md
  - 06_AUTHENTICATION_AND_RBAC.md
  - 07_MODULE_SPECIFICATION.md
  - 08_UI_UX_GUIDELINES.md
  - 09_API_AND_BACKEND_RULES.md
  - 10_AI_DEVELOPMENT_RULES.md
  - 11_IMPLEMENTATION_PLAYBOOK.md
  - 12_MASTER_PROMPT.md
---

# 01_PROJECT_CONTEXT

> **This document is the highest-level overview of BusinessOS.**
>
> Every implementation decision must remain consistent with the vision, principles, and architecture described here.

---

# 1. Executive Summary

BusinessOS adalah sebuah **Modular Business Operating System** yang dirancang untuk membantu individu, UMKM, dan perusahaan berkembang mengelola seluruh aktivitas bisnis dalam satu platform yang terintegrasi.

BusinessOS bukan sekadar aplikasi pencatatan keuangan.

BusinessOS adalah fondasi sebuah ekosistem bisnis yang dapat berkembang secara bertahap melalui modul-modul independen tanpa mengubah arsitektur inti.

Versi pertama akan memfokuskan diri pada **Financial Foundation**, kemudian berkembang menuju platform operasional bisnis yang lengkap.

Target akhir BusinessOS adalah menjadi platform yang mampu mengelola:

- Personal Finance
- Business Finance
- Cash Flow
- Multi Branch Management
- Inventory
- Point of Sale (POS)
- Human Resource
- Payroll
- Asset Management
- Customer Relationship
- Reporting & Analytics
- AI Assistant
- Business Automation

Seluruh modul harus dibangun di atas arsitektur yang sama sehingga dapat berkembang tanpa melakukan rewrite aplikasi.

---

# 2. Project Overview

## Project Name

BusinessOS

---

## Project Category

AI-Native Modular Business Management Platform

---

## Primary Platform

Android

---

## Future Platform

- Web
- Desktop
- iOS

---

## Primary Target

Micro Business (UMKM)

Small Business

Growing Business

Personal Finance Users

---

## Development Philosophy

BusinessOS dibangun menggunakan pendekatan:

> **Build Once, Expand Forever**

Artinya fondasi sistem harus cukup kuat sehingga modul baru dapat ditambahkan kapan saja tanpa mengubah struktur inti.

---

# 3. Business Vision

Menjadi platform operasi bisnis paling mudah digunakan oleh UMKM Indonesia namun memiliki kualitas arsitektur setara perangkat lunak enterprise.

BusinessOS harus memungkinkan pemilik usaha kecil mengelola bisnisnya dengan cara yang sebelumnya hanya dapat dilakukan oleh perusahaan besar.

Platform harus membantu pengguna mengambil keputusan berdasarkan data, bukan berdasarkan perkiraan.

---

# 4. Product Vision

BusinessOS bertujuan menjadi pusat kendali seluruh aktivitas bisnis.

Semua informasi penting harus tersedia dalam satu aplikasi.

Contohnya:

- Berapa keuntungan hari ini.
- Cabang mana paling produktif.
- Produk mana paling laku.
- Pengeluaran terbesar bulan ini.
- Cash flow minggu ini.
- Target penjualan.
- Kinerja karyawan.
- Laporan keuangan.

Pengguna tidak boleh berpindah-pindah aplikasi hanya untuk memperoleh informasi tersebut.

---

# 5. Product Mission

BusinessOS memiliki lima misi utama.

## Mission 1

Menyederhanakan pengelolaan bisnis.

---

## Mission 2

Membantu pemilik usaha memahami kondisi bisnis secara real-time.

---

## Mission 3

Mengintegrasikan seluruh proses bisnis dalam satu platform.

---

## Mission 4

Menyediakan fondasi AI sehingga BusinessOS dapat berkembang menjadi Intelligent Business Assistant.

---

## Mission 5

Membangun sistem modular yang mampu berkembang selama bertahun-tahun tanpa harus melakukan redesign besar.

---

# 6. Core Principles

Semua keputusan pengembangan wajib mengikuti prinsip berikut.

## 6.1 Modular First

Tidak boleh ada fitur yang bergantung kuat pada fitur lain.

Setiap modul harus dapat dikembangkan secara independen.

---

## 6.2 Simplicity First

Fitur yang kompleks harus terasa sederhana bagi pengguna.

Target pengguna bukan programmer maupun akuntan profesional.

---

## 6.3 Data First

Seluruh keputusan bisnis harus didasarkan pada data yang akurat.

Tidak boleh ada data yang disimpan tanpa tujuan yang jelas.

---

## 6.4 AI Ready

Setiap modul harus dirancang agar mudah dipahami AI.

Nama file, struktur folder, penamaan class, API, dan database harus konsisten.

---

## 6.5 Security by Design

Keamanan bukan fitur tambahan.

Keamanan adalah bagian dari desain sistem sejak awal.

---

## 6.6 Scalability

BusinessOS harus mampu berkembang dari:

1 pengguna

↓

10 pengguna

↓

100 pengguna

↓

10.000+ pengguna

tanpa perubahan besar pada arsitektur.
---

# 7. Target Users

BusinessOS dirancang untuk melayani berbagai jenis pengguna dengan tingkat kebutuhan yang berbeda, namun tetap menggunakan satu platform dan satu arsitektur data.

## 7.1 Personal User

Pengguna individu yang ingin mengelola keuangan pribadi.

Kebutuhan utama:

- Mencatat pemasukan.
- Mencatat pengeluaran.
- Mengelola anggaran.
- Memantau tabungan.
- Memantau target keuangan.
- Melihat laporan keuangan pribadi.

BusinessOS harus mampu digunakan sebagai aplikasi personal finance tanpa mengaktifkan modul bisnis.

---

## 7.2 Business Owner

Pemilik usaha merupakan pengguna dengan hak akses tertinggi.

Tanggung jawab:

- Melihat seluruh data perusahaan.
- Mengelola cabang.
- Mengelola pengguna.
- Menentukan harga produk.
- Menentukan promo.
- Melihat laporan.
- Mengelola seluruh modul.

Owner adalah pemilik data.

Tidak ada pengguna lain yang dapat mengambil alih kepemilikan sistem.

---

## 7.3 Operational Manager

Manager bertanggung jawab terhadap operasional harian.

Hak akses disesuaikan dengan cabang yang ditugaskan.

Manager dapat:

- Membuka operasional.
- Menutup operasional.
- Mengelola stok.
- Mengelola transaksi.
- Melihat laporan cabang.
- Mengelola karyawan pada cabangnya.

Manager tidak boleh mengakses data perusahaan secara penuh.

---

## 7.4 Employee

Employee merupakan pengguna operasional.

Hak akses sangat terbatas.

Contoh:

- Kasir.
- Admin.
- Gudang.
- Produksi.

Employee hanya dapat menjalankan tugas yang diberikan sesuai Role Based Access Control (RBAC).

---

## 7.5 Future User Types

BusinessOS harus mendukung penambahan tipe pengguna baru tanpa perubahan besar pada sistem.

Contoh:

- Finance Manager
- HR Manager
- Auditor
- Franchise Partner
- Accountant
- External Consultant

---

# 8. Business Scope

Versi pertama BusinessOS akan difokuskan pada fondasi bisnis yang paling penting.

## Phase 1 — Financial Foundation

Fitur utama:

- Personal Finance
- Business Finance
- Cash Flow
- Income
- Expense
- Account Management
- Category Management
- Dashboard
- Financial Report

Tujuan utama:

Membantu pengguna memahami kondisi keuangan secara akurat.

---

## Phase 2 — Business Operation

Setelah fondasi keuangan stabil, sistem berkembang menjadi platform operasional.

Modul yang ditambahkan:

- Inventory
- Product
- Supplier
- Customer
- Purchase
- Sales
- POS
- Stock Movement

---

## Phase 3 — Organization

BusinessOS berkembang menjadi sistem manajemen organisasi.

Meliputi:

- Employee
- Attendance
- Payroll
- Branch Management
- Permission
- Task Management

---

## Phase 4 — Intelligence

BusinessOS menjadi platform yang mampu memberikan rekomendasi berbasis AI.

Contoh:

- Prediksi cash flow.
- Analisis keuntungan.
- Rekomendasi stok.
- Analisis penjualan.
- Deteksi anomali transaksi.
- Ringkasan bisnis otomatis.
- AI Assistant.

---

# 9. Out of Scope

Fitur berikut **tidak menjadi prioritas pada versi awal**.

- ERP skala perusahaan besar.
- Manufacturing kompleks.
- Integrasi mesin industri.
- Trading saham.
- Cryptocurrency.
- Marketplace umum.
- Media sosial.
- Sistem perbankan.

Pembatasan ini bertujuan menjaga fokus pengembangan sehingga fondasi sistem tetap kuat.

---

# 10. High-Level Modules

BusinessOS dibangun menggunakan arsitektur modular.

Modul inti meliputi:

## Core

- Authentication
- Authorization
- User
- Branch
- Organization
- Settings

---

## Finance

- Personal Finance
- Business Finance
- Cash Flow
- Account
- Budget
- Financial Report

---

## Sales

- POS
- Order
- Invoice
- Customer

---

## Inventory

- Product
- Stock
- Warehouse
- Supplier
- Purchase

---

## Human Resource

- Employee
- Attendance
- Payroll

---

## Analytics

- Dashboard
- KPI
- Report
- Business Insight

---

## AI

- AI Assistant
- Recommendation Engine
- Forecasting
- Smart Report

Semua modul harus dapat dikembangkan secara independen namun menggunakan standar arsitektur yang sama.

---

# 11. High-Level System Overview

BusinessOS menggunakan pendekatan **AI-Native Modular Architecture**.

Secara konseptual, sistem terdiri dari beberapa lapisan utama.

## Presentation Layer

Berisi antarmuka pengguna.

Target platform:

- Android (prioritas)
- Web
- Desktop
- iOS

---

## Application Layer

Mengelola logika bisnis.

Lapisan ini bertanggung jawab atas:

- Validasi data.
- Orkestrasi proses bisnis.
- Pengelolaan state aplikasi.
- Komunikasi antar modul.

---

## Data Layer

Bertanggung jawab terhadap:

- Penyimpanan lokal.
- Sinkronisasi cloud.
- Caching.
- Repository pattern.

Seluruh akses data harus melalui lapisan ini.

Tidak diperbolehkan modul mengakses database secara langsung.

---

## Cloud Layer

Berfungsi sebagai pusat sinkronisasi.

Komponen utama:

- Authentication
- Firestore
- Cloud Functions
- Cloud Storage
- Messaging

Cloud Layer tidak boleh berisi logika bisnis yang seharusnya berada di Application Layer.

---

## AI Layer

Lapisan AI bertanggung jawab untuk:

- Analisis data.
- Rekomendasi.
- Ringkasan otomatis.
- Prediksi.
- Otomatisasi proses.

AI harus menggunakan data yang sudah tervalidasi dan tidak boleh mengubah data transaksi tanpa persetujuan pengguna.
---

# 12. Technology Strategy

BusinessOS dibangun menggunakan teknologi modern yang mendukung pengembangan jangka panjang, skalabilitas tinggi, dan kolaborasi dengan AI.

Pemilihan teknologi dilakukan berdasarkan beberapa prinsip:

- Long-term support
- Cross-platform capability
- Cloud-native architecture
- Offline-first capability
- Strong developer ecosystem
- AI-assisted development
- Easy maintenance
- Low operational cost

Teknologi boleh berubah di masa depan, namun perubahan tersebut tidak boleh mengubah arsitektur inti sistem.

---

# 13. Technology Overview

## Frontend

BusinessOS menggunakan Flutter sebagai framework utama.

Alasan:

- Android menjadi target utama.
- Satu codebase dapat digunakan untuk Android, Web, Desktop, dan iOS.
- Memiliki performa tinggi.
- Mendukung arsitektur modular.
- Memiliki komunitas yang sangat besar.

---

## Backend

Backend menggunakan Firebase sebagai Backend-as-a-Service (BaaS).

Komponen utama:

- Firebase Authentication
- Cloud Firestore
- Cloud Functions
- Firebase Storage
- Firebase Cloud Messaging
- Firebase Remote Config
- Firebase Crashlytics
- Firebase Analytics

BusinessOS menghindari backend server tradisional pada fase awal agar fokus pada pengembangan produk.

---

## Artificial Intelligence

Seluruh fitur AI akan menggunakan ekosistem Google AI.

Target integrasi:

- Gemini
- AI Studio
- AI SDK
- Cloud AI Services

AI bukan sekadar chatbot.

AI merupakan komponen inti yang membantu pengguna memahami kondisi bisnis dan membantu developer mengembangkan sistem secara konsisten.

---

## Local Storage

Aplikasi harus tetap dapat berjalan ketika koneksi internet tidak tersedia.

Karena itu seluruh transaksi penting harus mendukung penyimpanan lokal dan sinkronisasi otomatis ketika koneksi tersedia kembali.

Prinsip yang digunakan:

Offline First.

---

## Source Control

Seluruh source code menggunakan Git.

Repository utama berada di GitHub.

Tidak boleh ada source code yang berkembang di luar repository resmi.

---

# 14. Architecture Principles

Seluruh pengembangan wajib mengikuti prinsip berikut.

## Modular Monolith

Versi awal menggunakan Modular Monolith.

Alasan:

- Lebih mudah dikembangkan.
- Lebih mudah dipahami AI.
- Lebih mudah melakukan debugging.
- Tidak memiliki kompleksitas Microservices.

Apabila sistem berkembang sangat besar, migrasi ke Microservices dilakukan secara bertahap tanpa mengubah kontrak antar modul.

---

## Clean Architecture

BusinessOS menggunakan Clean Architecture.

Setiap modul memiliki lapisan yang jelas.

Contoh:

Presentation

↓

Application

↓

Domain

↓

Infrastructure

Tidak diperbolehkan terjadi ketergantungan terbalik.

---

## Domain Driven Thinking

BusinessOS dikembangkan berdasarkan domain bisnis.

Contoh domain:

Finance

Inventory

Sales

HR

Organization

Notification

AI

Masing-masing domain memiliki tanggung jawab yang jelas.

---

## Repository Pattern

Semua akses data harus melalui Repository.

UI tidak boleh mengakses database secara langsung.

Cloud Functions juga tidak boleh diakses secara langsung oleh UI.

---

## Dependency Injection

Seluruh service menggunakan Dependency Injection.

Tidak diperbolehkan membuat dependency secara langsung di dalam UI.

---

# 15. Repository Organization

Repository harus menjadi satu-satunya sumber kebenaran proyek.

Semua perubahan dilakukan melalui Git.

Tidak boleh ada dokumentasi penting yang hanya berada di chat, email, atau media lain.

Repository dibagi menjadi beberapa area utama:

```text
bootstrap/
docs/
sdk/
playbooks/
implementation/
knowledge/
templates/
.github/
```

Setiap folder memiliki tanggung jawab yang jelas.

Tidak boleh ada file yang ditempatkan tanpa alasan.

---

# 16. Coding Philosophy

BusinessOS dikembangkan dengan filosofi berikut.

## Readability Over Cleverness

Kode harus mudah dipahami.

Lebih baik kode sedikit lebih panjang daripada sulit dipelihara.

---

## Explicit Over Implicit

Semua perilaku penting harus terlihat jelas.

Hindari logika tersembunyi.

---

## Small Independent Modules

Modul harus kecil.

Modul besar harus dipecah.

---

## Testable Design

Semua business logic harus dapat diuji.

Logic tidak boleh bergantung pada UI.

---

## Documentation First

Perubahan besar harus dimulai dari dokumentasi.

Dokumentasi menjadi referensi implementasi.

---

# 17. AI Collaboration Principles

AI dianggap sebagai anggota tim engineering.

Namun AI bukan pengambil keputusan.

AI bertugas:

- membantu implementasi,
- menghasilkan boilerplate,
- membantu refactoring,
- membantu review,
- membantu dokumentasi.

AI tidak boleh:

- mengubah arsitektur tanpa persetujuan,
- menghapus fitur inti,
- mengubah kontrak API,
- mengubah struktur database,
- mengubah aturan keamanan,
- mengubah keputusan bisnis.

Semua keputusan strategis tetap berada pada Product Owner.

Setiap sesi AI harus menggunakan dokumen ini sebagai konteks utama sebelum membaca dokumen lain.
---

# 18. Development Workflow

BusinessOS dikembangkan menggunakan pendekatan iteratif yang berorientasi pada dokumentasi dan implementasi yang dapat diverifikasi.

Setiap fitur harus melalui tahapan berikut:

1. Perencanaan kebutuhan.
2. Penyusunan atau pembaruan dokumentasi.
3. Persetujuan desain.
4. Implementasi.
5. Pengujian.
6. Review kode.
7. Integrasi.
8. Dokumentasi perubahan.
9. Release.

Tidak diperbolehkan melewati tahapan dokumentasi untuk perubahan yang memengaruhi arsitektur, keamanan, atau model data.

---

# 19. Decision-Making Principles

Seluruh keputusan teknis harus mengikuti urutan prioritas berikut:

1. Keamanan (Security)
2. Integritas data (Data Integrity)
3. Kebenaran bisnis (Business Correctness)
4. Kemudahan pemeliharaan (Maintainability)
5. Skalabilitas (Scalability)
6. Performa (Performance)
7. Kemudahan penggunaan (User Experience)

Apabila terdapat konflik antara performa dan integritas data, maka integritas data harus diprioritaskan.

Apabila terdapat konflik antara kemudahan implementasi dan keamanan, maka keamanan harus diprioritaskan.

---

# 20. Success Criteria

BusinessOS dianggap berhasil apabila mampu memenuhi tujuan berikut.

## Product Success

- Membantu pengguna mengelola keuangan pribadi dan bisnis dalam satu platform.
- Menyediakan informasi bisnis yang akurat dan mudah dipahami.
- Mengurangi pekerjaan manual melalui otomatisasi.

## Technical Success

- Arsitektur tetap modular ketika modul baru ditambahkan.
- Seluruh fitur mengikuti standar pengembangan yang sama.
- Dokumentasi tetap sinkron dengan implementasi.

## AI Success

AI mampu:

- memahami struktur proyek,
- menghasilkan kode yang konsisten,
- mengikuti standar repository,
- tidak menyimpang dari arsitektur yang telah ditetapkan.

---

# 21. Current Project Status

Status proyek saat dokumen ini disusun.

| Area | Status |
|------|--------|
| Product Vision | ✅ Completed |
| Repository Foundation | ✅ Completed |
| AI Context Documents | 🚧 In Progress |
| System Architecture | ⏳ Planned |
| Database Design | ⏳ Planned |
| Module Specification | ⏳ Planned |
| AI Studio Preparation | ⏳ Planned |
| Application Development | ⏳ Not Started |

Dokumen ini menjadi titik awal seluruh aktivitas pengembangan berikutnya.

---

# 22. References

Dokumen ini harus dibaca sebelum membaca dokumen AI Context lainnya.

Urutan pembacaan yang direkomendasikan:

1. 01_PROJECT_CONTEXT.md
2. 02_PRODUCT_REQUIREMENTS.md
3. 03_SYSTEM_ARCHITECTURE.md
4. 04_TECH_STACK.md
5. 05_DATABASE_DESIGN.md
6. 06_AUTHENTICATION_AND_RBAC.md
7. 07_MODULE_SPECIFICATION.md
8. 08_UI_UX_GUIDELINES.md
9. 09_API_AND_BACKEND_RULES.md
10. 10_AI_DEVELOPMENT_RULES.md
11. 11_IMPLEMENTATION_PLAYBOOK.md
12. 12_MASTER_PROMPT.md

AI maupun developer wajib menggunakan urutan tersebut agar konteks proyek dipahami secara bertahap dan konsisten.

---

# Document Approval

| Role | Status |
|------|--------|
| Product Owner | Approved |
| Technical Architect | Approved |
| AI Context Maintainer | Approved |

---

# Change History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-07-31 | Initial official release of the BusinessOS Project Constitution. |

---

> **BusinessOS Constitution v1.0.0**

Dokumen ini merupakan sumber kebenaran (Single Source of Truth) tingkat tertinggi untuk proyek BusinessOS. Seluruh keputusan produk, arsitektur, implementasi, dan kolaborasi AI harus konsisten dengan prinsip-prinsip yang ditetapkan di dalam dokumen ini.