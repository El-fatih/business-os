---
document:
  id: AICTX-004
  title: Technology Stack
  version: 1.0.0
  status: Locked
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan seluruh teknologi resmi yang digunakan oleh BusinessOS.
  Dokumen ini merupakan satu-satunya referensi teknologi yang boleh digunakan
  selama pengembangan MVP.

dependencies:
  - 001_PROJECT_CONTEXT.md
  - 002_PRODUCT_REQUIREMENTS.md
  - 003_SYSTEM_ARCHITECTURE.md

next_documents:
  - 005_DATABASE_DESIGN.md
---

# 004_TECH_STACK

# 1. Executive Summary

Dokumen ini mendefinisikan seluruh keputusan teknologi resmi (Official Technology Decisions) untuk BusinessOS.

Tujuan utama dokumen ini adalah menghilangkan ambiguitas dalam proses implementasi sehingga AI Studio dan seluruh developer menggunakan teknologi yang sama secara konsisten.

Seluruh teknologi yang tercantum dalam dokumen ini berstatus **LOCKED** dan tidak boleh diganti tanpa melalui Architecture Decision Record (ADR).

---

# 2. Technology Principles

Seluruh pemilihan teknologi harus mengikuti prinsip berikut.

## TP-001 Simplicity First

Pilih teknologi yang paling sederhana namun mampu memenuhi kebutuhan BusinessOS.

Kompleksitas hanya ditambahkan apabila benar-benar diperlukan.

---

## TP-002 AI Compatibility

Teknologi harus menghasilkan implementasi yang konsisten ketika digunakan oleh AI Studio.

---

## TP-003 Production Ready

Teknologi harus stabil dan layak digunakan pada aplikasi produksi.

---

## TP-004 Modularity

Teknologi harus mendukung arsitektur modular yang telah ditetapkan.

---

## TP-005 Offline First

Teknologi harus mendukung strategi Offline-First yang menjadi fondasi BusinessOS.

---

## TP-006 Long-Term Support

Teknologi dipilih berdasarkan prospek pemeliharaan jangka panjang, komunitas aktif, dan dokumentasi yang baik.

---

# 3. Technology Selection Matrix

| Responsibility | Official Technology | Alternatives Evaluated | Status |
|----------------|---------------------|------------------------|--------|
| Mobile Framework | Flutter | React Native, Kotlin | LOCKED |
| Programming Language | Dart | Kotlin | LOCKED |
| State Management | Riverpod | Bloc, Provider | LOCKED |
| Routing | GoRouter | AutoRoute | LOCKED |
| Local Database | Isar | Drift, Hive, SQLite | LOCKED |
| Cloud Database | Cloud Firestore | Supabase, Appwrite | LOCKED |
| Authentication | Firebase Authentication | Auth0, Supabase Auth | LOCKED |
| Backend | Cloud Functions | Custom API, Appwrite Functions | LOCKED |
| File Storage | Firebase Storage | Supabase Storage | LOCKED |
| Push Notification | Firebase Cloud Messaging | OneSignal | LOCKED |
| Analytics | Firebase Analytics | PostHog | LOCKED |
| Crash Reporting | Firebase Crashlytics | Sentry | LOCKED |
| Remote Configuration | Firebase Remote Config | ConfigCat | LOCKED |
| Source Control | Git + GitHub | GitLab | LOCKED |

---

# 4. Technology Decision Rules

Seluruh implementasi wajib mengikuti aturan berikut.

## TDR-001

Hanya satu teknologi resmi untuk setiap tanggung jawab.

---

## TDR-002

Tidak diperbolehkan menggunakan library lain yang memiliki fungsi sama tanpa ADR.

Contoh:

- Tidak menggunakan Hive bersama Isar.
- Tidak menggunakan Provider bersama Riverpod.
- Tidak menggunakan AutoRoute bersama GoRouter.

---

## TDR-003

Semua package baru harus dievaluasi berdasarkan:

- kebutuhan nyata,
- kompatibilitas dengan arsitektur,
- dukungan komunitas,
- kompatibilitas dengan AI Studio.

---

## TDR-004

Versi package harus dikendalikan melalui `pubspec.yaml`.

Tidak menggunakan versi eksperimental pada lingkungan produksi kecuali telah disetujui melalui ADR.
---

# 5. Frontend Technology Stack

Seluruh aplikasi mobile BusinessOS dibangun menggunakan teknologi berikut.

## Mobile Framework

**Official Technology**

Flutter

**Status**

LOCKED

**Reason**

- Cross-platform.
- Performa tinggi.
- Dukungan AI Studio sangat baik.
- Ekosistem matang.
- Dokumentasi lengkap.
- Sangat cocok untuk arsitektur modular.

---

## Programming Language

**Official Technology**

Dart

**Status**

LOCKED

**Reason**

- Bahasa resmi Flutter.
- Null Safety.
- Modern.
- Produktif.
- Sangat didukung AI Studio.

---

## State Management

**Official Technology**

Riverpod

**Status**

LOCKED

**Reason**

- Compile-safe.
- Mudah diuji.
- Tidak bergantung pada BuildContext.
- Sangat cocok untuk Clean Architecture.
- Sangat konsisten dihasilkan AI Studio.

**Alternatives Evaluated**

- Bloc
- Provider
- GetX

---

## Navigation

**Official Technology**

GoRouter

**Status**

LOCKED

**Reason**

- Routing deklaratif.
- Mendukung deep linking.
- Cocok untuk aplikasi modular.
- Integrasi baik dengan Riverpod.

---

## Dependency Injection

**Official Technology**

Riverpod Provider Container

**Status**

LOCKED

**Reason**

- Tidak memerlukan service locator tambahan.
- Konsisten dengan State Management.
- Mempermudah testing.
- Mengurangi kompleksitas.

---

## Local Database

**Official Technology**

Isar Database

**Status**

LOCKED

**Reason**

- Performa tinggi.
- Query cepat.
- Reactive.
- Offline-first.
- Integrasi sangat baik dengan Flutter.

**Alternatives Evaluated**

- Drift
- Hive
- SQLite

---

## Local Secure Storage

**Official Technology**

Flutter Secure Storage

**Status**

LOCKED

**Digunakan Untuk**

- Token autentikasi.
- Session identifier.
- Encryption key.
- Pengaturan sensitif.

Data rahasia tidak boleh disimpan di Isar.

---

# 6. Development Standards

Seluruh implementasi Flutter wajib mengikuti standar berikut.

## DS-001

Null Safety wajib digunakan.

---

## DS-002

Tidak menggunakan `dynamic` kecuali benar-benar diperlukan.

---

## DS-003

Tidak menggunakan business logic di Widget.

---

## DS-004

Seluruh state harus immutable.

---

## DS-005

Widget harus reusable jika digunakan lebih dari satu feature.

---

## DS-006

Seluruh model memiliki mapper yang jelas antara:

- DTO
- Entity
- Database Model

---

## DS-007

Seluruh asynchronous operation harus menggunakan mekanisme yang konsisten dan mudah diuji.

---

## DS-008

Tidak boleh ada hardcoded configuration.

Seluruh konfigurasi berasal dari environment atau configuration layer.

---

## DS-009

Package baru harus melalui evaluasi dan terdokumentasi sebelum digunakan.

---

## DS-010

Seluruh kode harus mengikuti struktur Feature First + Clean Architecture yang telah ditetapkan pada `03_SYSTEM_ARCHITECTURE.md`.
---

# 7. Backend Technology Stack

BusinessOS menggunakan arsitektur Backend-as-a-Service (BaaS) yang diperkuat dengan layanan serverless untuk mempercepat pengembangan tanpa mengorbankan skalabilitas.

## Cloud Platform

**Official Technology**

Firebase

**Status**

LOCKED

**Reason**

- Integrasi sangat baik dengan Flutter.
- Skalabilitas tinggi.
- Dukungan AI Studio sangat baik.
- Infrastruktur global.
- Mendukung Offline-First melalui Cloud Firestore.

---

## Cloud Database

**Official Technology**

Cloud Firestore

**Status**

LOCKED

**Digunakan Untuk**

- Sinkronisasi data.
- Penyimpanan data cloud.
- Kolaborasi multi-user.
- Backup operasional.

Firestore bukan sumber data utama saat aplikasi berjalan. Data operasional berasal dari Local Database dan disinkronkan ke Firestore.

---

## Authentication

**Official Technology**

Firebase Authentication

**Status**

LOCKED

**Authentication Method**

- Phone Number (OTP)
- Email (untuk administrator apabila diperlukan)

Metode autentikasi tambahan dapat ditambahkan melalui ADR.

---

## Backend Logic

**Official Technology**

Cloud Functions

**Status**

LOCKED

**Responsibilities**

- AI Gateway
- Validasi server
- Automation
- Scheduled Tasks
- Integrasi pihak ketiga
- Operasi yang membutuhkan otorisasi server

Cloud Functions tidak menjadi tempat business rule utama.

---

## File Storage

**Official Technology**

Firebase Storage

**Status**

LOCKED

**Digunakan Untuk**

- Foto profil.
- Lampiran transaksi.
- Dokumen.
- Bukti pembayaran.
- Berkas pendukung lainnya.

---

## Push Notification

**Official Technology**

Firebase Cloud Messaging (FCM)

**Status**

LOCKED

**Digunakan Untuk**

- Notifikasi transaksi.
- Persetujuan.
- Pengingat.
- Informasi sistem.

---

## Analytics

**Official Technology**

Firebase Analytics

**Status**

LOCKED

Digunakan hanya untuk analitik penggunaan aplikasi dan peningkatan produk.

Data sensitif pengguna tidak boleh dikirim ke Analytics.

---

## Crash Reporting

**Official Technology**

Firebase Crashlytics

**Status**

LOCKED

Seluruh crash produksi harus tercatat agar dapat dianalisis dan diperbaiki.

---

## Remote Configuration

**Official Technology**

Firebase Remote Config

**Status**

LOCKED

Digunakan untuk:

- Feature Flag.
- Konfigurasi aplikasi.
- Aktivasi bertahap fitur baru.
- Parameter yang tidak memerlukan pembaruan aplikasi.

---

# 8. Artificial Intelligence Stack

BusinessOS memperlakukan AI sebagai layanan (AI as a Service), bukan sebagai bagian langsung dari modul bisnis.

## AI Architecture

Flutter App

↓

AI Service

↓

AI Gateway (Cloud Functions)

↓

AI Provider

↓

AI Model

---

## AI Gateway

Seluruh komunikasi dengan model AI harus melalui AI Gateway.

AI Gateway bertanggung jawab atas:

- Keamanan API Key.
- Validasi permintaan.
- Logging.
- Rate Limiting.
- Monitoring penggunaan.
- Pemilihan model AI.

Aplikasi mobile tidak diperbolehkan mengakses AI Provider secara langsung.

---

## AI Provider

Provider awal yang digunakan:

- Gemini

Struktur AI Gateway harus memungkinkan penambahan provider lain di masa depan tanpa mengubah aplikasi mobile.

---

## AI Usage Principles

- AI hanya diaktifkan ketika dibutuhkan.
- Tidak ada koneksi AI yang aktif secara permanen.
- Seluruh proses AI bersifat asynchronous.
- Kegagalan layanan AI tidak boleh mengganggu fungsi utama aplikasi.

AI merupakan fitur pendukung, bukan ketergantungan utama BusinessOS.
---

# 9. Development Environment

BusinessOS menggunakan tiga environment yang terpisah untuk memastikan proses pengembangan, pengujian, dan produksi berjalan dengan aman.

| Environment | Purpose | Firebase Project | Status |
|-------------|---------|------------------|--------|
| Development | Pengembangan harian | businessos-dev | LOCKED |
| Staging | QA & UAT | businessos-staging | LOCKED |
| Production | Aplikasi produksi | businessos-prod | LOCKED |

## Environment Rules

- Setiap environment memiliki Firebase Project yang berbeda.
- Database antar environment tidak boleh saling berbagi data.
- Authentication dipisahkan untuk setiap environment.
- Cloud Functions dipisahkan untuk setiap environment.
- Remote Config dipisahkan untuk setiap environment.
- AI Gateway dipisahkan untuk setiap environment apabila diperlukan.

---

# 10. Build Strategy

BusinessOS menggunakan Flutter Build Flavors.

## Official Flavors

- dev
- staging
- production

Setiap flavor memiliki:

- Firebase Configuration sendiri
- App Name sendiri
- Application ID sendiri
- Remote Config sendiri
- AI Gateway Configuration sendiri

Contoh:

Development

com.businessos.dev

↓

Staging

com.businessos.staging

↓

Production

com.businessos

---

# 11. Performance Budget

BusinessOS dirancang untuk berjalan optimal pada perangkat Android kelas menengah dan perangkat lama yang masih umum digunakan oleh UMKM.

## Target Performance

| Metric | Target |
|---------|---------|
| Cold Start Time | < 2 detik* |
| Warm Start Time | < 1 detik* |
| Idle Memory | < 120 MB* |
| Cold Start Memory | < 150 MB* |
| APK Size (Release) | Target < 40 MB* |
| Local Query Response | < 100 ms* |
| UI Frame Rate | Target 60 FPS |

\* Target engineering yang akan divalidasi melalui pengujian performa pada perangkat nyata.

## Performance Principles

- Offline First.
- Lazy Loading Module.
- Lazy Initialization Service.
- Minimal Background Process.
- Reactive UI tanpa rebuild yang tidak perlu.
- Optimized Database Query.
- Efficient Network Usage.
- Efficient Memory Usage.

---

# 12. Technology Constraints

Seluruh implementasi wajib mematuhi batasan berikut.

## TC-001

Tidak menggunakan teknologi di luar Official Technology Stack tanpa ADR.

---

## TC-002

Tidak boleh menggunakan dua library dengan tanggung jawab yang sama.

Contoh:

- Riverpod + Provider
- Isar + Hive
- GoRouter + AutoRoute

---

## TC-003

Seluruh package harus aktif dipelihara (actively maintained) dan kompatibel dengan versi Flutter yang digunakan.

---

## TC-004

Hindari package yang menambah ukuran APK secara signifikan apabila terdapat alternatif resmi yang lebih ringan.

---

## TC-005

Setiap dependency baru harus dievaluasi berdasarkan:

- Performa.
- Stabilitas.
- Keamanan.
- Dukungan komunitas.
- Kompatibilitas dengan AI Studio.

---

## TC-006

AI Studio harus mengikuti seluruh keputusan teknologi yang didefinisikan pada dokumen ini dan tidak diperbolehkan mengganti teknologi resmi tanpa Architecture Decision Record (ADR).

---

# 13. References

Dokumen ini bergantung pada:

- 01_PROJECT_CONTEXT.md
- 02_PRODUCT_REQUIREMENTS.md
- 03_SYSTEM_ARCHITECTURE.md

Dokumen yang bergantung pada Tech Stack:

- 05_DATABASE_DESIGN.md
- 06_AUTHENTICATION_AND_RBAC.md
- 07_MODULE_SPECIFICATION.md
- 09_API_AND_BACKEND_RULES.md
- 12_MASTER_PROMPT.md

---

# 14. Revision History

| Version | Date | Description |
|---------|------------|---------------------------|
| 1.0.0 | 2026-07-31 | Initial release of Technology Stack. |

---

# 15. Document Approval

| Role | Status |
|------|--------|
| Product Owner | Approved |
| Technical Architect | Approved |
| AI Context Maintainer | Approved |

---

> End of AICTX-004 — Technology Stack v1.0.0