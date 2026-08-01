---
document:
  id: AICTX-015
  title: Reporting & Dashboard
  version: 1.0.0
  status: Draft
  owner: BusinessOS
  classification: AI Context Document
  priority: Critical

purpose:
  Mendefinisikan Reporting Engine dan Dashboard Engine BusinessOS sebagai pusat penyajian informasi bisnis, analitik, KPI, dan visualisasi data. Domain ini bertanggung jawab menyediakan insight bisnis tanpa menjadi Data Owner dari transaksi operasional.

dependencies:
  - 03_SYSTEM_ARCHITECTURE.md
  - 05_DATABASE_DESIGN.md
  - 06_AUTHENTICATION_AND_RBAC.md
  - 011_MASTER_DATA_OWNERSHIP.md
  - 014_SETTINGS_CONFIGURATION.md

next_documents:
  - AI Studio Implementation
---

# 1. Executive Summary

Reporting & Dashboard merupakan lapisan presentasi (Presentation Layer) BusinessOS.

Domain ini tidak menyimpan transaksi bisnis sebagai sumber kebenaran (Source of Truth), melainkan menyusun data dari seluruh domain menjadi informasi yang berguna bagi pengguna.

Reporting Engine menyediakan laporan operasional dan analitik.

Dashboard Engine menyajikan indikator bisnis (Key Performance Indicators / KPI) secara ringkas dan real-time sesuai hak akses pengguna.

---

# 2. Scope

Reporting & Dashboard mencakup:

- Operational Reports
- Financial Reports
- Inventory Reports
- Treasury Reports
- CRM Reports
- Executive Dashboard
- KPI Dashboard
- Analytics
- Data Export

Domain ini tidak mengubah data transaksi.

---

# 3. Design Principles

## RPT-001

Reporting Domain bukan Data Owner.

---

## RPT-002

Seluruh laporan berasal dari data resmi milik Domain Owner.

---

## RPT-003

Reporting tidak boleh mengubah transaksi bisnis.

---

## RPT-004

Hak akses laporan mengikuti AICTX-006 Authentication & RBAC.

---

## RPT-005

Dashboard menampilkan data sesuai Organization, Branch, dan Permission pengguna.

---

## RPT-006

Reporting mendukung Offline-First sesuai AICTX-003.

---

# 4. Reporting Engine

Reporting Engine bertanggung jawab menghasilkan laporan dari seluruh domain BusinessOS.

Jenis laporan meliputi:

- Operational Report
- Financial Report
- Inventory Report
- Treasury Report
- Procurement Report
- CRM Report

Spesifikasi masing-masing laporan dijelaskan pada bagian berikutnya.
# 5. Operational Reports

Operational Reports digunakan untuk memantau aktivitas harian bisnis.

---

## 5.1 Sales Reports

### Available Reports

- Sales Summary
- Sales Detail
- Sales by Product
- Sales by Category
- Sales by Customer
- Sales by Cashier
- Sales by Branch
- Hourly Sales
- Return & Refund Report

### Standard Filters

- Organization
- Branch
- Date Range
- Cashier
- Customer
- Product
- Category
- Payment Method

---

## 5.2 Procurement Reports

### Available Reports

- Purchase Summary
- Purchase Detail
- Purchase by Supplier
- Purchase by Product
- Outstanding Purchase Order
- Goods Receipt Report
- Supplier Invoice Report
- Purchase Return Report

### Standard Filters

- Organization
- Branch
- Supplier
- Product
- Date Range
- Status

---

# 6. Inventory Reports

Inventory Reports digunakan untuk memantau stok dan pergerakannya.

---

## Available Reports

- Stock On Hand
- Stock Card
- Stock Movement
- Low Stock
- Reorder Report
- Dead Stock
- Inventory Valuation
- Stock Opname Result
- Transfer Report

---

## Standard Filters

- Organization
- Branch
- Warehouse
- Product
- Category
- Date Range

---

# 7. Treasury Reports

Treasury Reports digunakan untuk memantau arus kas dan saldo akun keuangan.

---

## Available Reports

- Cash Balance
- Bank Balance
- Digital Wallet Balance
- Cash In
- Cash Out
- Transfer Report
- Reconciliation Report
- Adjustment Report
- Cash Flow Summary

---

## Standard Filters

- Organization
- Branch
- Financial Account
- Payment Method
- Date Range

---

# 8. CRM Reports

CRM Reports digunakan untuk analisis pelanggan dan pemasok.

---

## Customer Reports

- Customer List
- Active Customer
- Inactive Customer
- Top Customer
- Customer Purchase History
- Customer Group Analysis

---

## Supplier Reports

- Supplier List
- Active Supplier
- Top Supplier
- Supplier Purchase History
- Supplier Performance (Roadmap)

---

## Standard Filters

- Organization
- Branch
- Customer Group
- Supplier Group
- Date Range
# 6. Financial Reports

Financial Reports menyediakan informasi keuangan berdasarkan data resmi dari Finance Domain dan Treasury Domain.

Reporting Domain tidak menghitung ulang transaksi, melainkan menyajikan data yang telah divalidasi oleh Domain Owner.

---

## Standard Financial Reports

BusinessOS menyediakan laporan berikut:

- Profit & Loss
- Balance Sheet
- Cash Flow
- General Ledger
- Trial Balance
- Journal Report
- Account Mutation
- Accounts Receivable
- Accounts Payable

---

## Standard Filters

Seluruh Financial Reports mendukung filter:

- Organization
- Branch
- Accounting Period
- Date Range
- Account
- Status

---

## Business Rules

- Laporan hanya menampilkan transaksi berstatus Posted atau Completed.
- Hak akses mengikuti Role dan Permission.
- Nilai historis tidak berubah akibat perubahan konfigurasi.

---

# 7. Operational Reports

Operational Reports menyajikan aktivitas harian Organization.

---

## Sales Reports

- Sales Summary
- Sales Detail
- Sales by Product
- Sales by Category
- Sales by Customer
- Sales by Branch
- Sales by Cashier
- Discount Report
- Void Transaction Report
- Return Report

---

## Procurement Reports

- Purchase Summary
- Purchase Detail
- Purchase by Supplier
- Outstanding Purchase Order
- Goods Receipt Report

---

## Inventory Reports

- Stock Balance
- Stock Movement
- Stock Valuation
- Slow Moving Items
- Fast Moving Items
- Stock Adjustment
- Stock Opname History

---

## Treasury Reports

- Cash In
- Cash Out
- Transfer Report
- Reconciliation Report
- Financial Account Mutation

---

## CRM Reports

- Customer List
- Supplier List
- Customer Activity
- Supplier Activity
- Top Customer
- Top Supplier

---

# 8. Dashboard Engine

Dashboard Engine menyediakan ringkasan informasi sesuai Role pengguna.

Dashboard bersifat configurable melalui AICTX-014 Settings & Configuration.

---

## Dashboard Types

BusinessOS menyediakan Dashboard untuk:

- Owner
- Manager
- Finance
- Purchasing
- Warehouse
- Cashier

---

## Owner Dashboard

Owner Dashboard menampilkan indikator utama bisnis.

Contoh widget:

- Today's Revenue
- Today's Profit
- Cash Balance
- Bank Balance
- Digital Wallet Balance
- Accounts Receivable
- Accounts Payable
- Inventory Value
- Top Selling Products
- Top Customers
- Sales Trend
- Expense Trend

---

## Manager Dashboard

Manager Dashboard berfokus pada operasional harian.

Contoh widget:

- Sales Today
- Purchase Today
- Pending Approval
- Low Stock Items
- Cash Position
- Shift Status

---

## Cashier Dashboard

Cashier Dashboard berfokus pada aktivitas POS.

Contoh widget:

- Shift Status
- Sales Today
- Transaction Count
- Payment Methods
- Cash Difference
# 6. Financial Reports

Financial Reports menyediakan informasi keuangan berdasarkan data resmi dari Finance Domain dan Treasury Domain.

Reporting Domain tidak menghitung ulang transaksi, melainkan menyajikan data yang telah divalidasi oleh Domain Owner.

---

## Standard Financial Reports

BusinessOS menyediakan laporan berikut:

- Profit & Loss
- Balance Sheet
- Cash Flow
- General Ledger
- Trial Balance
- Journal Report
- Account Mutation
- Accounts Receivable
- Accounts Payable

---

## Standard Filters

Seluruh Financial Reports mendukung filter:

- Organization
- Branch
- Accounting Period
- Date Range
- Account
- Status

---

## Business Rules

- Laporan hanya menampilkan transaksi berstatus Posted atau Completed.
- Hak akses mengikuti Role dan Permission.
- Nilai historis tidak berubah akibat perubahan konfigurasi.

---

# 7. Operational Reports

Operational Reports menyajikan aktivitas harian Organization.

---

## Sales Reports

- Sales Summary
- Sales Detail
- Sales by Product
- Sales by Category
- Sales by Customer
- Sales by Branch
- Sales by Cashier
- Discount Report
- Void Transaction Report
- Return Report

---

## Procurement Reports

- Purchase Summary
- Purchase Detail
- Purchase by Supplier
- Outstanding Purchase Order
- Goods Receipt Report

---

## Inventory Reports

- Stock Balance
- Stock Movement
- Stock Valuation
- Slow Moving Items
- Fast Moving Items
- Stock Adjustment
- Stock Opname History

---

## Treasury Reports

- Cash In
- Cash Out
- Transfer Report
- Reconciliation Report
- Financial Account Mutation

---

## CRM Reports

- Customer List
- Supplier List
- Customer Activity
- Supplier Activity
- Top Customer
- Top Supplier

---

# 8. Dashboard Engine

Dashboard Engine menyediakan ringkasan informasi sesuai Role pengguna.

Dashboard bersifat configurable melalui AICTX-014 Settings & Configuration.

---

## Dashboard Types

BusinessOS menyediakan Dashboard untuk:

- Owner
- Manager
- Finance
- Purchasing
- Warehouse
- Cashier

---

## Owner Dashboard

Owner Dashboard menampilkan indikator utama bisnis.

Contoh widget:

- Today's Revenue
- Today's Profit
- Cash Balance
- Bank Balance
- Digital Wallet Balance
- Accounts Receivable
- Accounts Payable
- Inventory Value
- Top Selling Products
- Top Customers
- Sales Trend
- Expense Trend

---

## Manager Dashboard

Manager Dashboard berfokus pada operasional harian.

Contoh widget:

- Sales Today
- Purchase Today
- Pending Approval
- Low Stock Items
- Cash Position
- Shift Status

---

## Cashier Dashboard

Cashier Dashboard berfokus pada aktivitas POS.

Contoh widget:

- Shift Status
- Sales Today
- Transaction Count
- Payment Methods
- Cash Difference
# 9. KPI Engine

KPI (Key Performance Indicator) Engine menyediakan indikator bisnis utama yang digunakan untuk memantau kinerja Organization.

KPI dihitung berdasarkan data resmi dari Domain Owner.

Reporting Domain tidak menjadi sumber kebenaran (Source of Truth).

---

## Standard KPI

BusinessOS menyediakan KPI standar berikut.

### Financial KPI

- Revenue
- Gross Profit
- Net Profit
- Cash Position
- Bank Balance
- Digital Wallet Balance
- Cash Flow
- Accounts Receivable
- Accounts Payable

---

### Sales KPI

- Total Sales
- Number of Transactions
- Average Transaction Value
- Sales Growth
- Sales per Branch
- Sales per Cashier

---

### Inventory KPI

- Inventory Value
- Inventory Turnover
- Low Stock Items
- Out of Stock Items
- Fast Moving Products
- Slow Moving Products

---

### Procurement KPI

- Purchase Value
- Outstanding Purchase Orders
- Supplier Performance
- Average Procurement Cycle

---

### CRM KPI

- Active Customers
- New Customers
- Top Customers
- Active Suppliers
- Top Suppliers

---

# 10. Analytics

Analytics menyediakan visualisasi tren bisnis berdasarkan data historis.

Analytics digunakan untuk membantu pengambilan keputusan.

---

## Standard Analytics

BusinessOS menyediakan analisis berikut:

- Sales Trend
- Purchase Trend
- Revenue Trend
- Expense Trend
- Profit Trend
- Cash Flow Trend
- Inventory Movement Trend
- Customer Growth
- Supplier Growth

---

## Business Rules

- Analytics menggunakan data historis yang telah diposting.
- Seluruh grafik mendukung filter Organization, Branch, dan Date Range.
- Analytics tidak mengubah data transaksi.

---

# 11. Export Engine

Reporting Engine mendukung ekspor laporan.

---

## Supported Formats

- PDF
- Microsoft Excel (.xlsx)
- CSV

---

## Business Rules

- Data hasil ekspor harus sama dengan data yang ditampilkan pada layar.
- Hak ekspor mengikuti Role dan Permission.
- Seluruh aktivitas ekspor dapat dicatat pada Audit Trail sesuai kebijakan Organization.

---

# 12. Performance Guidelines

Reporting Domain harus mampu menghasilkan laporan secara efisien tanpa mengganggu transaksi operasional.

---

## PG-001

Laporan besar harus mendukung pagination.

---

## PG-002

Dashboard hanya memuat widget yang aktif.

---

## PG-003

Widget dapat diperbarui secara independen tanpa memuat ulang seluruh Dashboard.

---

## PG-004

Reporting Service dapat menggunakan cache untuk laporan yang sering diakses.

---

## PG-005

Export dilakukan secara asynchronous untuk laporan berukuran besar.

---

# 13. Security Guidelines

Reporting Domain mengikuti AICTX-006 Authentication & RBAC.

---

## SEC-001

Pengguna hanya dapat melihat laporan sesuai Organization dan Branch.

---

## SEC-002

Hak akses laporan ditentukan oleh Role dan Permission.

---

## SEC-003

Dashboard hanya menampilkan widget yang diizinkan.

---

## SEC-004

Data sensitif mengikuti kebijakan keamanan Organization.

---

# 14. References

Dokumen ini mengacu pada:

- AICTX-003 System Architecture
- AICTX-005 Database Design
- AICTX-006 Authentication & RBAC
- AICTX-007 Finance Module
- AICTX-008 Inventory Module
- AICTX-009 POS Module
- AICTX-010 Procurement Module
- AICTX-011 Master Data Ownership
- AICTX-012 CRM Domain
- AICTX-013 Treasury Domain
- AICTX-014 Settings & Configuration

---

# 15. Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | Initial Draft | Initial Reporting & Dashboard Specification |

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

Reporting & Dashboard dinyatakan Locked setelah seluruh laporan, dashboard, KPI, analytics, dan mekanisme ekspor disetujui.

---

# End of Document