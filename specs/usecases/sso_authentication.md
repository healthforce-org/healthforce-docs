# 🧩 Use Case: SSO Authentication untuk Pengguna Internal SDMK

## 🆔 ID Use Case
UC-SSO-001

## 🎯 Tujuan
Memungkinkan pengguna internal SDMK (admin, operator, verifikator) melakukan login ke sistem HealthForce menggunakan akun Single Sign-On (SSO) yang telah ditetapkan secara terpusat.

## 👥 Aktor Terkait
- Pengguna internal SDMK
- Sistem SSO pusat
- Backend Golang (API Gateway)
- Modul Odoo Admin Dashboard

## 🗂️ Modul Terkait
- Backend Service: `auth-service`
- Odoo Module: `sdmk_core`, `sdmk_integration_manager`

## 📚 Preconditions
- Pengguna telah memiliki akun aktif di sistem SSO pusat.
- Sistem SSO dapat dijangkau dan tidak sedang maintenance.
- Client frontend telah terdaftar di sistem SSO (client_id & redirect_uri valid).

## 🔁 Alur Normal (Main Flow)
1. Pengguna mengakses URL dashboard admin HealthForce.
2. Sistem mendeteksi belum ada sesi login → redirect ke endpoint SSO Login.
3. Pengguna mengisi kredensial di halaman login SSO pusat.
4. Setelah autentikasi berhasil, SSO mengirimkan `code` ke backend HealthForce.
5. Backend melakukan pertukaran `code` → `access_token` ke SSO server.
6. Backend memverifikasi token dan membaca data profil pengguna.
7. Jika pengguna valid dan terdaftar → session dibuat, redirect ke dashboard.
8. Trace ID dibuat sejak awal request dan dibawa sampai ke Odoo via header.

## 🔄 Alur Alternatif (Exception Flow)
- **Kondisi**: `code` tidak valid / expired
  - Backend mengembalikan error "SSO expired"
  - Log error dikirim ke Prometheus/Grafana
- **Kondisi**: Pengguna tidak terdaftar di sistem internal HealthForce
  - Backend menolak login meskipun token SSO valid
  - Tampilkan pesan: "Akun Anda belum didaftarkan dalam sistem HealthForce"
- **Kondisi**: SSO server tidak bisa diakses
  - Sistem fallback: redirect ke halaman error / retry
  - Monitoring mengirim alert ke Grafana/Slack

## ✅ Postconditions
- Pengguna berhasil login ke dashboard dengan session aktif.
- Akses pengguna tercatat dalam audit trail.
- Trace ID disimpan di log backend & frontend.

## 🧪 Validasi & Logika
- Email/token pengguna harus sesuai dengan domain yang diizinkan (`@kemkes.go.id`).
- Role pengguna ditentukan berdasarkan mapping dari SSO → HealthForce.
- Token disimpan hanya dalam memory/session, bukan di DB.

## 🔐 Keamanan & Logging
- Otentikasi via OAuth2 Authorization Code Grant.
- Token hanya dikirim via HTTPS.
- Semua permintaan login/log-out dilengkapi dengan trace ID.
- Audit log: login time, IP, user agent.

## 📈 Monitoring & Observabilitas
- Request login SSO dimonitor di Prometheus (durasi, success/fail count).
- Trace ID: `X-Request-ID` dari API Gateway → Odoo.
- Grafana panel:
  - Jumlah login/hari
  - Error SSO
  - Durasi login rata-rata

## 🧩 Referensi Terkait
- [Modul Odoo terkait autentikasi](../odoo.md)
- [Integrasi SSO](../integration.md)
- [Panduan API](../api-guidelines.md)
- [Diagram konteks SSO](../diagrams/architecture/c4/context-diagram.puml)
