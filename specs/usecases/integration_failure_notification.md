# 🧩 Use Case: Sistem Notifikasi dan Alert untuk Kegagalan Integrasi Sistem Eksternal

## 🆔 ID Use Case
UC-INTEGRATION-FAIL-001

## 🎯 Tujuan
Memastikan admin atau tim teknis mendapatkan notifikasi cepat jika terjadi kegagalan dalam integrasi dengan sistem eksternal (KKI, SISDMK, SSO).

## 👥 Aktor Terkait
- Admin Sistem
- Backend Golang (API Gateway)
- Modul Odoo `sdmk_integration_manager`
- Sistem monitoring (Datadog, Prometheus, Grafana)

## 🗂️ Modul Terkait
- Backend Service: `integration-service`
- Odoo Module: `sdmk_integration_manager`

## 📚 Preconditions
- Sistem integrasi aktif dan monitoring sudah dikonfigurasi.

## 🔁 Alur Normal (Main Flow)
1. Backend mendeteksi kegagalan pada request integrasi (timeout, error response).
2. Sistem otomatis mengirimkan notifikasi ke admin via email, Slack, atau dashboard Odoo.
3. Admin melihat notifikasi dan melakukan pengecekan status sistem eksternal.
4. Jika perlu, admin menonaktifkan sementara integrasi via modul `sdmk_integration_manager`.
5. Setelah sistem eksternal pulih, integrasi dapat diaktifkan kembali.

## 🔄 Alur Alternatif (Exception Flow)
- Notifikasi gagal dikirim → Log error dan retry.
- Admin tidak merespon dalam waktu tertentu → Eskalasi ke tim on-call.

## ✅ Postconditions
- Admin menerima alert kegagalan integrasi secara real-time.
- Status integrasi tercatat dalam modul manajemen integrasi.

## 🔐 Keamanan & Logging
- Log lengkap kegagalan request disimpan.
- Notifikasi hanya diterima oleh pengguna yang berwenang.

## 📈 Monitoring & Observabilitas
- Jumlah kegagalan integrasi per bulan.
- Durasi downtime integrasi.

## 🧩 Referensi Terkait
- [Modul sdmk_integration_manager](../odoo.md)
- [Monitoring dan Observabilitas](../odoo.md)
- [API Guidelines](../api-guidelines.md)
